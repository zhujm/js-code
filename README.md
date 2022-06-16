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

> 节流原理：一个单位时间内只能执行一次回调，如果单位时间内

```js
// 计时法
// 需要注意问题：lastTime应为0，不能为时间戳，以保证至少执行一次: 假设间隔为1000毫秒，如果在900毫秒中被多次触发后停止，使用时间戳则目标函数并不会被执行，很明显是不合理的(这种情况可能不常见,因为创建节流到触发的过程一般会远大于间隔)。
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
// 如果值为函数、undefined，则转换后该字段会丢失；为RegExp、Error等特殊类型会变成空对象
// 如果有循环引用，会报错
const result = JSON.parse(JSON.stringify(obj))

// 递归
循环引用问题，递归爆栈
```



