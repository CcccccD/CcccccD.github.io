---
title: 常用手写代码
date: 2021-11-30 10:29:13
tags: code
categories: 前端
---

## 常用手写代码

### 函数柯里化

维基百科上说道：柯里化，英语：Currying(果然是满满的英译中的既视感)，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

#### Currying有哪些好处呢？
1. 参数复用
2. 提前确认
3. 延迟运行

#### 手写代码
```
    function curry(fn,...args){
        if (args.length >= fn.length) return fn(...args)
            return function (...args2) {
                return curry(fn, ...args, ...args2);
        }
    }
```

### 防抖

防抖，一段时间内触发一次，再次触发会重置时间 debounce

#### 手写代码
```
function debounce(fn, wait) {
    var timer = null;
    return function () {
        let context = this;
        let args = arguments;
        if (timer) clearTimeout(timer);;
        timer = setTimeout(() => {
            fn.apply(context, args);
        }, wait);
    }
}
```

带立即执行需要引入callNow变量

```
function debounce3(fn, wait) {
    let timer;
    return function () {
        let context = this;
        let args = arguments;
        if (timer) clearTimeout(timer)
        let callNow = !timer;
        timer = setTimeout(() => {
            timer = null;
        }, wait)
        callNow && fn.apply(context, args)
    }
}
```


### 节流

节流，一段时间内只触发一次

#### 手写代码

```
 function throttle(fn, wait) {
    var timer = null;
    return function () {
        let context = this;
        if (!timer) {
            timer = setTimeout(() => {
                fn.apply(context, arguments);
                timer = null;
            }, wait)
        }
    }
}
```


### new的实现

#### new做了哪些事
1. 定义一个空对象
2.`Object.setPrototypeOf(obj, fn.prototype)` 即 `obj.__proto__ = Animal.prototype`， 建立了 obj 对象的原型链：`obj->Animal.prototype->Object.prototype->null`
3. 执行构造函数中的代码（为这个新对象添加属性并绑定this）；
4. 返回新对象 （如果构造函数返回一个对象就会返回构造函数返回的对象）。
```
function _new(fn, ...args) {
    let obj = {};
    Object.setPrototypeOf(obj, fn.prototype);
    let result = fn.apply(obj, args);
    return result instanceof Object ? result : obj;
}
```


### 深拷贝

#### 解释一下
我们在实际的项目中，肯定不能让每个对象的值都指向同一个堆内存，这样的话不便于我们做操作，所以自然而然的诞生了深拷贝
深拷贝作用在引用类型上！例如：Object，Array
深拷贝不会拷贝引用类型的引用，而是将引用类型的值全部拷贝一份，形成一个新的引用类型，这样就不会发生引用错乱的问题，使得我们可以多次使用同样的数据，而不用担心数据之间会起冲突

#### 递归实现代码
```
        // 定义一个深拷贝函数  接收目标target参数
        function deepClone(target) {
            // 定义一个变量
            let result;
            // 如果当前需要深拷贝的是一个对象的话
            if (typeof target === 'object') {
                // 如果是一个数组的话
                if (Array.isArray(target)) {
                    result = []; // 将result赋值为一个数组，并且执行遍历
                    for (let i in target) {
                        // 递归克隆数组中的每一项
                        result.push(deepClone(target[i]))
                    }
                    // 判断如果当前的值是null的话；直接赋值为null
                } else if (target === null) {
                    result = null;
                    // 判断如果当前的值是一个RegExp对象的话，直接赋值    
                } else if (target.constructor === RegExp) {
                    result = target;
                } else {
                    // 否则是普通对象，直接for in循环，递归赋值对象的所有值
                    result = {};
                    for (let i in target) {
                        result[i] = deepClone(target[i]);
                    }
                }
                // 如果不是对象的话，就是基本数据类型，那么直接赋值
            } else {
                result = target;
            }
            // 返回最终结果
            return result;
        }
```

#### 其他实现方式

##### JSON.stringify()以及JSON.parse()
使用JSON.stringify()以及JSON.parse()它是不可以拷贝 undefined ， function， RegExp 等等类型的

#### Object.assign(target, source)
一层对象来说是没有任何问题的，但是如果对象的属性对应的是其它的引用类型的话，还是只拷贝了引用，修改的话还是会有问题


### JSONP

#### 解释一下
JSONP 是利用script标签没有跨域限制的特性来解决跨域问题实现数据交互的一种方案

#### 代码实现

```
        function myJsonp(options) {
            return new Promise((resolve, reject) => {
                //判断是否是第一次jsonp请求
                if (!window.jsonpNum) {
                    window.jsonpNum = 1
                } else {
                    window.jsonpNum++
                }

                let {
                    url,
                    data,
                    timeout = 5000,
                    cbkey = 'callback',
                } = options

                //保证每次请求接收的方法都不会重复
                let funName = 'jsonpReceive' + window.jsonpNum

                //清除本次jsonp请求产生的一些无用东西
                function clear() {
                    window[funName] = null
                    script.parentNode.removeChild(script);
                    clearTimeout(timer)
                }

                //定义jsonp接收函数
                window[funName] = function (res) {
                    //一旦函数执行了，就等于说请求成功了
                    resolve(res)
                    clear()
                }

                //请求超时计时器
                let timer = setTimeout(() => {
                    reject('超时了')
                    clear()
                }, timeout)

                //定义请求的参数
                let params = ''

                //如果有参数
                if (Object.keys(data).length) {
                    for (let key in data) {
                        params += `&${key}=${encodeURIComponent(data[key])}`;
                    }

                    params = params.substr(1)
                }

                //拼接最终的请求路径，结尾拼接回调的方法名
                url = url + '?' + params + `&${cbkey}=${funName}`

                let script = document.createElement('script');
                script.src = url;
                document.body.appendChild(script);
            })
        }
```

使用：

```
     let options = {
            url: 'https://c.y.qq.com/musichall/fcgi-bin/fcg_yqqhomepagerecommend.fcg',
            cbkey: 'jsonpCallback',
            data: {
                g_tk: 1928093487,
                inCharset: 'utf-8',
                outCharset: 'utf-8',
                notice: 0,
                format: 'jsonp',
                platform: 'h5',
                uin: 0,
                needNewCode: 1
            },
            // QQ音乐接口Jsonp字段
        }

        myJsonp(options)
            .then(res => {
                console.log(res);
            }, err => {
                console.log(err)
            })
```