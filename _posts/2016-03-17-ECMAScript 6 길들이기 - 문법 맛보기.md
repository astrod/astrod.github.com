---
layout: post
title: "ES6 문법 맛보기"
tags:
- javascript
category:
- js
---

* toc
{:toc}

# 들어가며
이 글에 나오는 내용은 ECMAScript 6 길들이기의 1장 내용의 요약이다.

# let
var로 선언한 변수를 함수 스코프 변수라고 하며, 함수 밖에 선언한 함수 스코프 변수는 전역변수로 스크립트 끝에서도 참조가 가능하다. 마찬가지로 함수 안에 선언하면 함수 밖을 제외한 내부 어디서라도 접근할 수 있다.

변수 스코프가 블록이 아니므로 메모리 누수가 발생할 소지가 있으며, 읽기 어렵고 디버깅이 곤란한 프로그램이 쓰여지기 쉽다. 이것을 막기 위해 ES6에서는 let이라는 변수 선언문을 추가하였다.

## 블록 스코프 변수 선언
let으로 선언한 변수를 블록 스코프 변수라고 하며, 함수 밖에 선언하면 함수 스코프 변수처럼 사용할 수 있다. 블록 안에 선언하면 자신을 정의한 블록에서만 접근 가능하며, 블록 밖에서는 볼 수 없다.

~~~javascript
let a = 12; // 전역 접근 가능
function MyFunction() {
	console.log(a);
	let b = 13; // 함수 안에서 접근 가능
	
	if(true) {
		let c = 14; // "if"문 안에서 접근 가능
		console.log(b);
	}
	
	console.log(c);
}
MyFunction();
~~~
출력값은

12 <br>
13 <br>
Uncaught ReferenceError: c is not defined(…)

이다. 이제 블록 스코프 밖의 함수에는 접근할 수 없게 되었다.

## 변수 재선언
같은 스코프에서 이미 var 로 선언한 변수를 다시 var로 선언하면 변수를 덮어쓴다. let은 이야기가 다르다. let으로 선언한 변수를 다시 let으로 선언하면 TypeError 예외가 발생한다.

~~~javascript
let a = 0;
let a = 1; //TypeError

function myFunction() {
	let b = 2;
	let b = 3; //TypeError
	
	if(true) {
		let c = 4;
		let c = 5; //TypeError
	}
}
~~~

함수 안에서 접근 가능한 변수명과 동일한 이름을 가진 변수명을 선언하면, 사용한 키워드에 따라 가리키는 대상이 달라진다.

~~~javascript
var a = 1;
var b = 2;

function myFunction() {
	var a = 3; // 전혀 다른 변수
	let b = 4; // 전혀 다른 변수
	
	if(true) {
		var a = 5; // 덮어쓴다
		let b = 6; // 전혀 다른 변수
	}
}
~~~

ES6 코드라면  var 대신에  let을 사용하는 습관을 들이자. 기억도 잘 되고 코드도 읽기 쉬우며, 스코프를 착각할 일이 없어진다.

# const 키워드
const 키워드는 값을 다시 할당할 수 없는 상수를 선언한다. 자바의 final과 비슷하다.

## 스코프
let과 스코프 규칙은 동일하다.

- 블록 스코프이다
- 호이스팅은 되지 않는다.

## 상수를 통한 객체 참조
자바의 final과 동일한데, const로 객체를 선언하면 할당된 객체는 불변이지만, 객체의 내부 프로퍼티는 가변적이다.

~~~ javascript
const a = {
	"name" : "mark"
};

console.log(a.name);

a.name = "JB";

console.log(a.anme);

a = {}; // 읽기 전용이므로 에러 발생
~~~

# 파라미터 기본값
이전에는 함수가 파라미터 값을 받지 못하면, 디폴트값을 지정할 방법이 마땅치 않았다. 그래서 파라미터값이 undefined인지 체크하여 기본값을 세팅하는 로직을 추가했었다.

~~~javascript
function myFunction(x, y, z) {
	x = x ? x : 1;
	y = y ? y : 1;
	z = z ? z : 1;
}
~~~

ES6에는 기본값을 세팅할 수 있는 새 구문이 추가되었다.

~~~javascript
function myFunction(x = 1, y = 2, z = 3) {
	console.log(x, y, z);
}
~~~
파라미터를 추가하지 않으면 위에 설정한 기본값을 세팅해준다.

# 펼침 연산자
이터러블 객체를 개별 값으로 나누는 spread operator는 "..."으로 표기한다.

예를 들면, 배열을 인자로 넘길 때 ES5까지는 apply 내장 메서드를 이용할수밖에 없었다. 펼침 연산자를 이용하면 한결 쉬워진다.

~~~javascript
function myFun(a, b) {
	return a + b;
}

let data = [1,4];
let result = myFun(...data);
console.log(result); // 5
~~~

자바스크립트 해석기는 ...data를 1,4로 치환한 후에, myFun 함수를 호출한다.

~~~javascript
let result = myFun(...data); => let result = myFun(1,4);
~~~

## 펼침 연산자의 다른 용례
- 펼침 연산자를 통해 배열 값을 다른 배열의 일부로 만들 수 있다.

~~~javascript
let array1 = [1,3,5];
let array2 = [4, ...array1,6,7,8];
console.log(array2); // [4,1,3,5,6,7,8]
~~~

- 배열 값을 다른 배열에 밀어넣을 수 있다.

~~~javascript
let array1 = [2,3,4];
let array2 = [1];

array2.push(...array1);

console.log(array2); // "1,2,3,4"
~~~

# 나머지 파라미터
나머지 파라미터(rest parameter)는 함수의 마지막 파라미터 앞에 "..."을 붙인 것으로, 이름 붙은 파라미터(named parameter)보다 함수 파라미터를 더 많이 포함한 배열이다.

개수가 가변적인 함수 인자를 포착하는 용도로 사용한다.

ES5까지는 arguments객체로 전달된 함수 인자를 꺼내서 사용했는데, 이 객체는 배열은 아니지만 배열과 유사하게 사용할 수 있었다.

~~~javascript
function myFun(a, b) {
	var args = Array.prototype.slice.call(arguments, myFun.length);
	
	console.log(args);
}

myFun(1,2,3,4,5); // "3,4,5"
~~~
arguments가 배열이 아니기 때문에 call로 slice를 호출해야 한다.

위의 코드를 나머지 파라미터를 쓰면 더 간단하게 해결할 수 있다.

~~~javascript
function myFun(a,b, ...args) {
	console.log(args); // "3,4,5"
}
~~~

# 해체 할당
해체 할당(destructuring assignment)는 이터러블이나 객체의 값/프로퍼티를 각각 배열이나 변수에 할당하는 표현식이다.

## 배열 해체 할당
이터러블 객체에서 값을 추출하여 변수에 할당한다. 배열 생성 리터럴과 비슷하여 이렇게 부른다. 이전에는 배열에 값을 할당하려면 이렇게 했었다.

~~~javascript
var myArray = [1,2,3];
var a = myArray[0];
var b = myArray[1];
var c = myArray[2];
~~~

이것이 ES6에서는 이렇게 변화되었다.

~~~javascript
let arr = [1,2,3];
let a,b,c;
[a,b,c] = arr; // 배열 해제 할당 구문
~~~

더 짧게 줄일 수도 있다.

~~~javascript
let [a,b,c] = [1,2,3];
~~~

할당 없이 값을 건너뛰는 것도 가능하다.

~~~javascript
let [a, , b] = [1, 2, 3];
console.log(a); // 1
console.log(c); // 3
~~~

나머지 연산자를 사용할 수도 있다.

~~~javascript
let [a, ...b] = [1, 2, 3,4,5,6];
console.log(b); // 2,3,4,5,6
~~~

## 객체 해체 할당
객체 해체 할당은 객체 프로퍼티 값을 추출하여 변수에 할당한다.

ES5 이전에는 객체 프로퍼티 값을 변수에 할당하려면 방법이 이것 뿐이었다.

~~~javascript
var obj = {"name" : "민호", "age" : 23};
var name = obj.name;
ver age = obj.age;
~~~

ES6부터는 객체 해체 할당문으로 해결할 수 있다.

~~~javascript
var obj = {"name" : "민호", "age" : 23};
let name, age;
({name, age} = object); // 배열 해체 할당 구문
~~~

객체 프로퍼티와 변수명은 반드시 같아야 한다. 만약 달리 하고 싶을 때는 다음처럼 하면 된다.

~~~javascript
var obj = {"name" : "민호", "age" : 23};
let x, y;
({name : x, age : y} = object);
~~~

