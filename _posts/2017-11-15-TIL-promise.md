---
layout:            post
title:             "17/11/15 - TIL - Promises"
menutitle:         "17/11/15 - TIL - Promises"
category:          js/node
author:            myohyun
tags:              js-node
---


# Promises

 1. 객체(클래스)이다.

 2. 비동기(asynchronous) 프로그래밍을 위한 개념이다.

 3. Promises로 쓸 수 있는 다양한 라이브러리가 있다. Node.js의 Global에 Promise객체가 이미 들어가 있다. Node.js



**장점: 함수가 리턴하는 Promise 객체와 .then()함수 등으로 기존의 콜백함수가 지저분하게 나열되는 것을 막을 수 있다.**





[Mongoose site 참고](http://mongoosejs.com/docs/promises.html)
[Using promises(번역해 놓고 보니 이게 더 예시가 잘 나온 듯한...??)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)

**아래의 글을 번역:**
[Promises in Node.js – An Alternative to Callbacks](https://developer.ibm.com/node/2016/08/24/promises-in-node-js-an-alternative-to-callbacks/)





콜백함수는 자바스크립트에서 비동기 코드를 다루기 위한 가장 단순한 메카니즘이다.
그렇지만 가공되지 않은 콜백함수는 control flow, exception handling, 동기화 코드와 친숙한 function의 의미를 희생시킨다. Promise는 위의 잃어버린 개념들을 다시 되찾을 수 있는 방법을 제공한다.

Promise 객체의 코어요소는 `then` method 이다. 이 method는 비동기 작업으로부터 성공시의 리턴값(fulfillment value)이나 예외상황(Rejection reason)을 어떻게 얻는지를 말해준다.

`then` mrthod는 onFulfilled 과 onRejected 두 상황을 각각 처리하는 콜백함수를 갖는다.

```c
var promise = doSomethingAync()
promise.then(onFulfilled, onRejected)
```

onFulfilled 와 onRejected 함수는 비동기 작업이 끝나 promise가 얻어졌을 때 불려진다. (당연히 비동기 프로세스는 성공이거나, 에러 둘 중 하나를 던질 테니, promise를 구한 후 두 함수중 한 함수만 실행되겠죠?) 


# Callbacks to promises

기초 Promise의 지식을 바탕으로, Node.js에서 자주 봐온 비동기 콜백함수를 보자.

```c
readFile(function (err, data) {
  if (err) return console.error(err)
  console.log(data)
})
```
만약 readFile 함수가 promise를 리턴한다면 다음과 같이 코드를 수정할 수 있다.

```c
var promise = readFile()
promise.then(console.log, console.error)

```
 대충 봤을 때는 (코드 길이의 간결함으로 인한 )심미적인 차이밖에 없는 듯 하다. 하지만 promise로 비동기 작업을 나타내는 값에 접근할 수 있게 되었다! 
우리는 이 promise 값을 다른 곳에 전달할 수 있고,  promise에 접근하는 누군가가 `then`함수를 사용하여 비동기 작업의 완료 여부에 상관없이 이 값을 사용할 수 있다.(promise라는 변수로 비동기 작업 값을 정의했기 때문에 이 변수를 이용하여 다른 코드를 짤 수 있다는 것을 말하는 듯.?) 또한, promise는 한 번만 구해지기 때문에 우리는 비동기 작업의 결과가 어떻든 간에 바뀌지 않는다는 보증을 얻을 수 있다. 

> 'then' method를 단지 두 개의 콜백함수를 가지는 함수로 볼게 아니라 비동기 작업으로부터 일어난 것들을 밝히기 위해 promise를 열어보는(unwrap) 함수라고 생각하는게 더 도움이 된다.

# Chaining and nesting promises

`then` method는 promise를 리턴한다.
```c
var promise = readFile()
var promise2 = promise.then(readAnotherFile, console.error)
```

위의 promise 는 onFulfilled 나 onRejected handlers의 리턴값을 나타낸다.


```c
var promise = readFile()
var promise2 = promise.then(function (data) {
  return readAnotherFile() // if readFile was successful, let's readAnotherFile
}, function (err) {
  console.error(err) // if readFile was unsuccessful, let's log it but still readAnotherFile
  return readAnotherFile()
})
promise2.then(console.log, console.error) // the result of readAnotherFile
```

`then` method가 promise를 리턴하기 때문에, 콜백지옥을 피하기 위해 promise를 계속 연결할 수 있다.


```c
readFile()
  .then(readAnotherFile)
  .then(doSomethingElse)
  .then(...)

```

클로저가 살아있도록 유지하는 것이 중요하다면 promise는 매우 적절하게 nested될 수 있다. 

```c
readFile()
  .then(function (data) {
    return readAnotherFile().then(function () {
      // do something with `data`
    })
  })
```


# Promises and synchronous functions

Promises는 동기(synchronous)함수들을 다양한 방식으로 모델링할 수 있다. 한 가지 방법은 다른 함수를 부르는 대신에 연속작업을 위해 return을 사용하는 것이다. 그 전 예에서는 `readFile()`이 끝난 후에 할 작업을 알려주기 위해 `readAnotherFile()`를 리턴한다.

만약 당신이 promise를 리턴한다면, 그것은 비동기 작업이 끝났을 때 다음 `then` method에 신호를 보낼 것이다. 당신은 또한 이외의 값들을 리턴할 수 있고 다음 `onFulfilled` 함수가 리턴된 값을 이용할 것이다.

```c
readFile()
  .then(function (buf) {
    return JSON.parse(buf.toString())
  })
  .then(function (data) {
    // do something with `data`
  })
```

# Error handling in promises

`return` 이외에도, `throw`, `try/catch` 키워드 또한 사용할 수 있다.
이것은 promise의 가장 강력한 특징 중의 하나라고 말할 수 있을 듯 하다. 예를 들어, 다음과 같은 동기화 코드(synchronous code)를 살펴보자.

```c
try {
  doThis()
  doThat()
} catch (err) {
  console.error(err)
}
```

이 예에서, `doThis()` 이나 `doThat()` 함수가 에러를 던진다면, 우리는  에러를 잡아 log할 수 있다. `try/catch` 블록은 여러 작업들을 그룹화하는 것을 허락하기 때문에, 각 작업마다 error를 명시적으로 다뤄야 하는 것을 피할 수 있다. 우리는 promise를 이용해 비동기적으로 이 작업을 할 수 있다.

```c
doThisAsync()
  .then(doThatAsync)
  .then(undefined, console.error)
```

만약 doThisAsync() 함수가 성공적이지 못 하면, 이 promise는 거부되고 onRejected handler와 함께 체인의 다음 `then` 함수   가 불릴 것이다. 위의 예시의 경우는  `console.error` 함수가 불릴 것이다.  `try/catch` 함수 블록과 같이, `doThatAsync()`함수는 절대 불리지 않는다.  이것은 우리가 각 스탭마다 명시적으로 에러를 다루어야 하는 raw 콜백함수를 넘어선 커다란 진전이다.

그러나, 이것은 더 좋아질 수 있다. `then` 콜백함수로부터 명시적이든, 암시적이튼 던져진 예외는 다시 promise로 다루어질 수 있다.

```c
doThisAsync()
  .then(function (data) {
    data.foo.baz = 'bar' // throws a ReferenceError as foo is not defined
  })
  .then(undefined, console.error)
```

다음 코드는 생성된 ReferenceError가 다음 chain의 onRejected handler에 의해 캐치되어진다.
물론, 이것은 명시적인 `throw`에 대해서도 잘 작동한다.

```c
doThisAsync()
  .then(function (data) {
    if (!data.baz) throw new Error('Expected baz to be there')
  })
  .catch(console.error) // catch(fn) is shorthand for .then(undefined, fn)
```


# An important note with error handling

아까 말했다 시피, promises는 `try/catch`를 모방한다. `try/catch` 블록에서 에러를 절대로 명시적으로 다루지 않음으로서 에러를 숨기는(mask) 것이 가능하다.

```c
try {
  throw new Error('never will know this happened')
} catch (e) {}
```
promises 에 대해 똑같이 작업이 가능하다.
```c
readFile()
  .then(function (data) {
    throw new Error('never will know this happened')
  })
  ```

감춰진 에러들을 보이기 위한 방법은 promise 체인을 간단한 .catch(onRejected)으로 끝내는 것이다.

```c
readFile()
  .then(function (data) {
    throw new Error('now I know this happened')
  })
  .catch(console.error)

```
Third-party libraries 는 un-handled rejections을 노출시키는 옵션들을 포함하고 있다.

# Promises in the concrete
지금까지, 우리의 예시들은 ES6 and [Promises/A+](https://promisesaplus.com/) `then` method를 설명하기 위해 promise를 return하는 dummy method들을 사용했다. 지금부터 좀 더 확실한? 예시들을 살펴보도록 하자.


# Converting callbacks to promises

어쩌면 promise 가 몇 번이나 생성가능한지 궁금해 할 수 있다. promise를 만들어내는 API는 Promise/A+에 명시되어 있지 않은데, 왜냐하면 (이 API에 대한 명세가 딱히)상호호환성에 필요하지 않기 때문이다.(의역?) 하지만 ES6는 Promise 생성자를 표준화하였다. promise 사용의 가장 보편적인 경우 중 하나는 존재하는 callback-base 라이브러리를 변환하는 것이다. 여기에 Bluebird와 같은 promise 라이브러리는 편리한 helpers를 제공한다.
예를 들어, Node의 코어 비동기함수들은 promise를 리턴하지 않고 콜백함수를 사용한다. 그러나 Bluebird를 이용해서 쉽게 이 함수들이 promise를 리턴하게끔 만들 수 있다.

```c
var fs = Bluebird.promisifyAll(fs)
var promise = fs.readFileAsync('myfile.txt')
promise.then(console.log, console.error)
```

Blubird는 노드와 다른 환경이 promise-aware할 수 있게 수많은 helper 함수들을 제공한다. 더 자세한 사항은 API 문서를 체크하세용~


# Creating raw promises
promise 를 Promise 생성자를 이용해서 수동으로 만들 수도 있다. `fs.reaFile()`을 손수 promise로 wrapping해서 promise-aware 하게 만드는 작업을 해보자.

```c
function readFileAsync (file, encoding) {
  return new Promise(function (resolve, reject) {
    fs.readFile(file, encoding, function (err, data) {
      if (err) return reject(err) // rejects the promise with `err` as the reason
      resolve(data)               // fulfills the promise with `data` as the value
    })
  })
}
readFileAsync('myfile.txt').then(console.log, console.error)
```

# Making APIs that support both callbacks and promises

우리는 지금까지 콜백함수를 promise 코드로 바꾸는 두 가지 방법을 보았다. 우리는 또한 promise와 콜인터페이스 둘다를 제공하는 api들을 만들 수 있다. 예를 들어, fs.readFile을 콜백함수와 promise 둘 다를 제공하는 API를 보자.

```c
function readFileAsync (file, encoding, cb) {
  if (cb) return fs.readFile(file, encoding, cb)

  return new Promise(function (resolve, reject) {
    fs.readFile(function (err, data) {
      if (err) return reject(err)
      resolve(data)
    })
  })
}
```

콜백함수가 제공되었을 때, 이 함수는 표준 Node 스타일(rr, result)로 불릴 것이다.

```c
readFileAsync('myfile.txt', 'utf8', function (er, data) {
  // ...
})
```

# Doing parallel operations with promises

지금까지, 우리는 연속적인 비동기 작업들에 대해서만 이야기했다. 병렬처리에 대해서 ES6는 Promise.all 메소드를 제공하는데, 이 메소드는 promise 배열을 받아 새로운 promise 값을 리턴한다.
새로운 promise는 모든 작업들이 성공적으로 마쳐졌을 때 만족되어진다. 만약 어떤 작업 중 하나라도 실패한다면, 새로운 promise 변수는 거절된다.

```c
var allPromise = Promise.all([ fs_readFile('file1.txt'), fs_readFile('file2.txt') ])
allPromise.then(console.log, console.error)
```

>promise가 함수를 모방한다는 것은 꽤 중요하다. 함수는 오직 하나의 값을 리턴한다. 두 개의 promise로 `Promise.all` 을 수행했을 때 onFulfilled 함수가 오직 하나의 argument(an array with both results)로 불려진다. 이것은 당신을 놀라게 할 수 있다. 그러나 동기적 상대?의 일관성은 promise가 제공하는 중요한 보장이다.


# Making promises even more concrete

The best way to really understand promises is to use them. Here are some ideas to get you started:

* Wrap some basic Node workflows, converting callbacks into promises.
* Rewrite an async method into one that uses promises.
* Write something recursively using promises (a directory tree would be a good start).
* Write a passing Promise A+ implementation. Here is my crude one.

# Further resources

* ES6 Promise Specification
* NodeUp – fortysix – a promises by promisers show
* Promises with Domenic Denicola and Kris Kowal
* Redemption from Callback Hell
* You’re Missing the Point of Promises
* Callbacks are imperative, promises are functional
* List of Promise/A+ compatible implementations
