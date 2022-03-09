
# 호이스팅이란?
> 모든 선언이 코드의 선두로 끌어올려진 것처럼 동작하는 자바스크립트 고유의 특징이다

과정은 다음과 같다.
1. 자바스크립트 Parse가 함수를 실행하기 전에 해당 함수를 일단 한번 훑는다
2. 함수 안에 존재하는 변수나 함수 선언에 대한 정보를 기억하여 실행한다
3. 유효 범위는 함수 블록이다

변수 선언이 소스코드가 한 줄씩 순차적으로 실행되는 시점,
즉 런타임이 아니라 ```그 이전 단계```에서 먼저 실행됨


---

# 호이스팅의 대상
> var 변수 선언과 함수 선언문에서만 호이스팅이 일어난다
즉, ```var 변수/함수 선언```만 위로 끌어올려지며, ```할당```은 끌어올려지지 ```않는다```
let / const 변수 선언과 함수 표현식에서는 호이스팅이 발생하지 않는다


- 함수 선언문
```js
var foo2; // [Hoisting] 함수표현식의 변수값 "선언"

function foo() { // [Hoisting] 함수선언문
          console.log("hello");
  }
```

- 함수 표현식
```js
var foo2 = function() { // 함수표현식
          console.log("hello2");
  }
```

## 함수 선언문에서의 호이스팅
> 함수 선언문은 코드를 구현한 위치와 상관없이 자바스크립트의 특징인 호이스팅에 따라 브라우저가 자바스크립트를 해석할 때, 맨 위로 끌어올려진다

즉, 선언을 한 코드가 호출보다 아래에 있어도 올바르게 동작한다
예시를 보자
```js
printName();	// 정상 작동된다

function printName(firstname) { 
    var result; // [Hoisting] var 변수 "선언"

    function inner() { // [Hoisting] 함수선언문
        return "inner value";
    }

    result = inner(); // "할당"
    console.log(typeof inner); // > "function"
    console.log("name is " + result); // > "name is inner value"
}
```

위의 상황처럼 선언문이 호출보다 아래에 있어도 함수 호이스팅이 먼저 일어나기 때문에 정상 작동 된다. 또한, printName함수 내에서 inner를  함수로 인식하는 것도 알 수 있다

## 함수 표현식에서의 호이스팅
> 함수 표현식은 함수 선언문과 달리 선언과 호출 순서에 따라서 정상적으로 실행이 안될 수도 있다. 
즉, 함수 표현식에서는 선언과 할당의 분리가 발생한다

만약, 함수 표현식의 선언이 호출보다 위에 있다면
```js
 /* 정상 */
 function printName(firstname) { // 함수선언문
     var inner = function() { // 함수표현식 
         return "inner value";
     }
        
     var result = inner(); // 함수 "호출"
     console.log("name is " + result);
 }

 printName(); // > "name is inner value"
```
정상 출력되지만,

함수 표현식의 선언이 호출보다 아래에 있는 경우(var 지정)에는
```js
 /* 오류 */
 function printName(firstname) { // 함수선언문
     console.log(inner); // > "undefined": 선언은 되어 있지만 값이 할당되어있지 않은 경우
     var result = inner(); // ERROR!!
     console.log("name is " + result);

     var inner = function() { // 함수표현식 
         return "inner value";
     }
 }
 printName(); // > TypeError: inner is not a function
```
undefined의 오류가 발생된다.

이와 달리 const / let 변수에 할당시키면,
```js
 /* 오류 */
 function printName(firstname) { // 함수선언문
     console.log(inner); // ERROR!!
     let result = inner();  
     console.log("name is " + result);

     let inner = function() { // 함수표현식 
         return "inner value";
     }
 }
 printName(); // > ReferenceError: inner is not defined
```
ReferenceError가 발생한다

이 차이는 var는 변수 호이스팅이 일어나는 반면, let, const는 변수 호이스팅이 일어나지 않기 때문이다


----


# 호이스팅의 우선순위
**같은 이름의 var 변수 선언과 함수 선언에서의 호이스팅**
- 변수 선언 >>> 함수 선언 

```js
 /** --- JS Parser 내부의 호이스팅(Hoisting)의 결과 --- */
  // 1. [Hoisting] 변수값 선언 
  var myName; 
  var yourName; 

  // 2. [Hoisting] 함수선언문
  function myName() {
      console.log("yuddomack");
  }
  function yourName() {
      console.log("everyone");
  }

  // 3. 변수값 할당
  myName = "hi";
  yourName = "bye";

  console.log(typeof myName); // > "string"
  console.log(typeof yourName); // > "string"
```

함수 선언문이 위에 존재하더라도 변수 선언문이 먼저 끌어올려지기 때문에 myName과 yourName의 타입은 string으로 존재하게 된다.

- 값이 할당되지 않은 변수인 경우, ```변수 선언문 << 함수 선언문```
- 값이 할당되어 있는 변수인 경우, ```변수 선언문 >> 함수 선언문```


```js
  var myName = "Heee"; // 값 할당 
  var yourName; // 값 할당 X

  function myName() { // 같은 이름의 함수 선언
      console.log("myName Function");
  }
  function yourName() { // 같은 이름의 함수 선언
      console.log("yourName Function");
  }

  console.log(typeof myName); // > "string"
  console.log(typeof yourName); // > "function"
```

때문에, 코드의 가독성과 유지보수를 위해서 호이스팅이 일어나지 않도록 가급적 코드 상단부에 선언하는 습관을 들이도록 해야하며 let / const 의 사용을 지향하도록 한다



