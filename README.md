# promise
es6之promise

js在执行大量的异步操作时，会使用回调函数的不断嵌套，导致代码可读性极差，陷入Callback hell,比如当我们在进行多个ajax请求时，数据之间有依赖时，传统的写法是这样的：
```
$.ajax({
    url: "a.php"
    success: function (data) {
        $.ajax({
            // 要在第一个请求成功后才可以执行下一步
            url: 'b.php',
            success: function (data) {
                 // ......
            }
        });
    }
});

```
自从promise对象出现，并称为ECMA2015的标准后，我们就不必要这样写了。

## what's promise

Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6将其写进了语言标准，统一了用法，原生提供了Promise对象。

所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。
看一段代码：
```
function helloWorld (ready) {
    return new Promise(function (resolve, reject) {
        if (ready) {
            resolve("Hello World!");
        } else {
            reject("Good bye!");
        }
    });
}

helloWorld(true).then(function (message) {
    alert(message);
}, function (error) {
    alert(error);
});
```
上面的代码实现的功能非常简单，helloWord 函数接受一个参数，如果为 true 就打印 "Hello World!"，如果为 false 就打印错误的信息。helloWord 函数返回的是一个 Promise 对象。

在 Promise 对象当中有两个重要方法————resolve 和 reject。

resolve 方法可以使 Promise 对象的状态改变成成功，同时传递一个参数用于后续成功后的操作，在这个例子当中就是 Hello World! 字符串。

reject 方法则是将 Promise 对象的状态改变为失败，同时将错误的信息传递到后续错误处理的操作。

## Three status
上面提到了 resolve 和 reject 可以改变 Promise 对象的状态，那么它究竟有哪些状态呢？

Promise 对象有三种状态：
```
Fulfilled 可以理解为成功的状态
Rejected 可以理解为失败的状态
Pending 既不是 Fulfilld 也不是 Rejected 的状态，可以理解为 Promise 对象实例创建时候的初始状态
```
helloWorld 的例子中的 then 方法就是根据 Promise 对象的状态来确定执行的操作，resolve 时执行第一个函数（onFulfilled），reject 时执行第二个函数（onRejected）。
## then

helloWorld 的例子当中利用了 then(onFulfilld, onRejected) 方法来执行一个任务打印 "Hello World!"，在多个任务的情况下 then 方法同样可以用一个清晰的方式完成。
```
function printHello (ready) {
    return new Promise(function (resolve, reject) {
        if (ready) {
            resolve("Hello");
        } else {
            reject("Good bye!");
        }
    });
}

function printWorld () {
    alert("World");
}

function printExclamation () {
    alert("!");
}

printHello(true)
    .then(function(message){
        alert(message);
    })
    .then(printWorld)
    .then(printExclamation);

```
## catch

catch 方法是 then(onFulfilled, onRejected) 方法当中 onRejected 函数的一个简单的写法，也就是说可以写成 then(fn).catch(fn)，相当于 then(fn).then(null, fn)。使用 catch 的写法比一般的写法更加清晰明确。

## Promise.all 和 Promise.race
Promise.all 可以接收一个元素为 Promise 对象的数组作为参数，当这个数组里面所有的 Promise 对象都变为 resolve 时，该方法才会返回。
```
var p1 = new Promise(function (resolve) {
    setTimeout(function () {
        resolve("Hello");
    }, 3000);
});

var p2 = new Promise(function (resolve) {
    setTimeout(function () {
        resolve("World");
    }, 1000);
});

Promise.all([p1, p2]).then(function (result) {
    console.log(result); // ["Hello", "World"]
});
```
上面的例子模拟了传输两个数据需要不同的时长，虽然 p2 的速度比 p1 要快，但是 Promise.all 方法会按照数组里面的顺序将结果返回。

日常开发中经常会遇到这样的需求，在不同的接口请求数据然后拼合成自己所需的数据，通常这些接口之间没有关联（例如不需要前一个接口的数据作为后一个接口的参数），这个时候 Promise.all 方法就可以派上用场了。

还有一个和 Promise.all 相类似的方法 Promise.race，它同样接收一个数组，不同的是只要该数组中的 Promise 对象的状态发生变化（无论是 resolve 还是 reject）该方法都会返回。
