# js-code  高频手写JS
##### 1.防抖

> 防抖原理：事件触发n秒后再执行回调，如果事件在n秒内又被触发则重新计时。

```javascript
// 需要注意的问题：1. 需要保留传入参数  2. 不要丢失this
const debounce = (fn, wait = 100) => {
  let timer = null;
  return function (...arg){
    timer && clearTimeout(timer);
    timer = setTimeout(() => {
      fn.apply(this, arg)
    }, wait)
  }
} 
```
##### 2.节流

> 节流原理：一个单位时间内只能执行一次回调，如果单位时间内触发了多次，只有一次会执行

```js
// 计时法
// 需要注意问题：lastTime应为0，不能为时间戳，以保证至少执行一次: 假设间隔为1000毫秒，如果在900毫秒中被多次触发后停止，
// 使用时间戳则目标函数并不会被执行，很明显是不合理的(这种情况可能不常见,因为创建节流到触发的过程一般会远大于间隔)。
function throttle (fn, wait = 100) {
  let lastTime = 0
  return function (...arg) {
    const now = Date.now()
    if(now - lastTime > wait) {
      lastTime = now
      fn.apply(this, arg)
    }
  }
}
// 标志位法
function throttle(fn, wait) {
  let flag = true
  return function(...arg) {
    if(!flag) return
    setTimeout(() => {
      flag = true
      fn.apply(this, arg)
    }, wait)
    flag = false
  }
}
```

##### 3. 深拷贝

```javascript
// JSON转换 
// 如果值为函数、undefined、Symbol，则转换后该字段会丢失；为RegExp、Error等特殊类型会变成空对象
// 如果有循环引用，会报错
const result = JSON.parse(JSON.stringify(obj))

// 递归
循环引用问题，递归爆栈
```

new的模拟实现：原理就是利用apply修改this指向，并返回执行结果。
> 需要注意的是保留原型链和判断执行结果是否是引用类型。
##### 4. 模拟new
```javascript
function new(confuc, ...args) {
  if(typeof confuc !== 'function'){
    throw '请传入函数'
  }
  let obj = new Object()
  obj.__proto__ = Object.create(confuc.prototype)
  let result = confuc.apply(obj, args)

  let isObject = result && typeof result === 'object'
  let isFunction = typeof result === 'function'
  return (isObject || isFunction) ? result : obj
}
```

##### 模拟call
call、apply的模拟实现：原理就是运用this指向，目标上新增一个函数挂载当前函数，并传入参数执行。
简单来说就是在target上新增一个一样的函数，新增函数的this指向的就是target。
```javascript
Function.prototype._call = function(context, ...args){
  const context = context || window
  context.fn = this
  var result = context.fn(...args)
  delete context.fn
  return result
}

```
##### 模拟apply
```javascript
Function.prototype._apply = function(context, args){
  const context = context || window
  context.fn = this
  var result = context.fn(...args)
  delete context.fn
  return result
}
```

##### 模拟bind
```javascript
Function.prototype._bind = function(context,...args){
  const _this = this;
  const fuc = function(...args2){
    const newArgs = [...args, ...args2]
    const target = this instanceOf _this ? this : context
    _this.apply(target, newArgs);
  }
  this.prototype && (fuc.prototype = Object.create(this.prototype))
  return fuc
}
```