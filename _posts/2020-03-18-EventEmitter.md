---
layout:     post
title:      "EventEmitter事件派发器"
subtitle:   ""
date:       2020-03-18
author:     "Faye"
tags:
    - Javascript
---

对于Event事件大家应该都很熟悉，比如dom中的button，可以通过addEventListener/attachEvent（IE）添加click事件处理。而一般的object对象是没有事件派发功能的，基于此需求，实现了一个EventEmitter。

> 代码如下（打包后）：

```Javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
var slice = Array.prototype.slice;
var EventEmitter = /** @class */ (function () {
    function EventEmitter() {
        this._events = Object.create(null);
    }
    /**
     * Listen for a event.
     * @param event Event name.
     * @param listener Event listener.
     */
    EventEmitter.prototype.on = function (event, listener) {
        (this._events[event] || (this._events[event] = [])).push(listener);
        return this;
    };
    /**
     * Listen for a event, but only once.
     * @param event Event name.
     * @param listener Event listener.
     */
    EventEmitter.prototype.once = function (event, listener) {
        var _this = this;
        var on = function () {
            var args = [];
            for (var _i = 0; _i < arguments.length; _i++) {
                args[_i] = arguments[_i];
            }
            _this.off(event, on);
            listener.apply(_this, args);
        };
        this.on(event, on);
        return this;
    };
    EventEmitter.prototype.off = function (event, listener) {
        if (arguments.length === 0) {
            this._events = Object.create(null);
            return this;
        }
        var listeners = this._events[event];
        if (!listeners) {
            return this;
        }
        if (arguments.length === 1) {
            delete this._events[event];
            return this;
        }
        for (var i = listeners.length - 1; i >= 0; i--) {
            if (listeners[i] === listener) {
                listeners.splice(i, 1);
                break;
            }
        }
        return this;
    };
    EventEmitter.prototype.emit = function (event) {
        var args = slice.call(arguments, 1);
        var listeners = this._events[event];
        if (listeners) {
            for (var _i = 0, listeners_1 = listeners; _i < listeners_1.length; _i++) {
                var listener = listeners_1[_i];
                listener.apply(this, args);
            }
        }
        return this;
    };
    return EventEmitter;
}());
exports.default = EventEmitter;
```

代码看起来还挺正确的，于是就上线跑起来了。
有一天，同事A跑来了，说：“伙计，你的代码在once的时候有bug诶”
我：“what???不会的”

人家丢出了测试用例
```Javascript
const eventBus = new EventEmitter();

eventBus.once('test', ()=>{console.log(1)});
eventBus.once('test', ()=>{console.log(2)});
eventBus.once('test', ()=>{console.log(3)});
eventBus.once('test', ()=>{console.log(4)});
eventBus.once('test', ()=>{console.log(5)});

eventBus.emit('test')
//1 3 5
```
丢了两个log，诶？？？

我查我查我查查~~~

用 once 注册的事件，在 emit 的时候会动态改变 listeners 的长度，造成 for 循环过程中每次执行一次 $once 注册的事件 上边界会-1，就会把 listener 后面的事件丢掉，因为 listeners 是浅拷贝的
![](https://s10.mogucdn.com/mlcdn/c45406/200318_4gelk8f62ca56f04f742hff89c9a9_1128x1410.png)


既然找到了问题，那我们加个深拷贝吧
```Javascript
EventEmitter.prototype.deepCopy = function(Obj) {
    if(typeof Obj !== 'object') {
      return
    }
    var newObj = Obj instanceof Array ? [] : {};
    for(var val in Obj) {
      if(Obj.hasOwnProperty(val)){
        newObj[val] = typeof Obj[val] === 'object' ? deepCopy(Obj[val]) : Obj[val] //递归便利数组和对象
      }
    }
    return newObj
 };
 //同时修改下listener_1
EventEmitter.prototype.emit = function (event) {
    var args = slice.call(arguments, 1);
    var listeners = this._events[event];
    if (listeners) {
        for (var _i = 0, listeners_1 = this.deepCopy(listeners); _i < listeners_1.length; _i++) {
            var listener = listeners_1[_i];
            console.log(`emit ->  listeners_1 length:  ${listeners_1.length} len:  ${listeners.length}`)
            listener.apply(this, args);
        }
    }
    return this;
};
```
再试下测试用例，恩，正常了


tips：
1. _events用于存放事件
2. on绑定事件，emit触发事件，使用apply来调用相应的事件listener


### 参考
3. [前端必懂EventEmitter，不懂会丢人](https://blog.csdn.net/weixin_42134714/article/details/85691808)