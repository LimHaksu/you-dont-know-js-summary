#### 배열

배열의 인덱스는 숫자, 배열 자체도 객체여서 키/프로퍼티 문자열을 추가할 수 있음.

```javascript
var a = [];
a[0] = 1;
a['foobar'] = 2;

a.length; // 1
a['foobar']; // 2
a.foobar; // 2
```

※ 문자열 값이 10진수로 타입이 바뀌면 숫자 키를 사용한 것과 같음

```javascript
var a = [];
a['13'] = 42;
a.length; // 14
```



#### 유사배열

유사배열을 진짜 배열로 바꾸기 

```javascript
function foo(){
    var arr = Array.prototype.slice.call( arguments ); // arguments 는 유사배열
    var arr = Array.from( arguments ); // ES6에서 사용법
    arr.push( 'bam' );
    console.log( arr );
}

foo( 'bar, 'baz' ); // ['bar', 'baz', 'bam']
```



#### 문자열

문자열은 배열과 닮음(유사배열). 공통점 : length, indexOf() (ES5), concat() 메서드를 갖고있음.

```javascript
var a = 'foo';
var b = ['f','o','o'];

a.length // 3
b.length // 3
a.indexOf('o') // 1
b.indexOf('o') // 1

var c = a.concat('bar') // 'foobar'
var d = b.concat(['b', 'a', 'r']) // ['f', 'o', 'o', 'b', 'a', 'r']

a === c // false
b === d // false
```



문자열은 Immutable, 배열은 Mutable

문자열에 대해 불변 배열 메소드는 빌려쓰기 가능

```javascript
var a = 'foo';
a.join; // undefined
a.map; // undefined

var c = Array.prototype.join.call(a, '-');
var d = Array.prototype.map.call(a, function(v){
  return v.toUpperCase() + ".";  
}).join("");

c; // 'f-o-o'
d; // 'F.O.O'
```



문자열 뒤집기

```javascript
var c = a.split("").reverse().join("");
```



#### 숫자

number가 유일, integer, float 모두 아우름



소수점 앞 정수가 0, 소수점 이하가 0이면 생략 가능

```javascript
0.42
.42

42.0
42.
```



아주 크거나, 아주 작은 숫자는 지수형으로 표현, toExponential() 메서드의 결과 값과 같음

```javascript
var a = 5E10;
a; // 50000000000
a.toExponential(); // '5e+10'

var b = a * a;
b; // 2.5e+21

var c = 1 / a;
c; // 2e-11
```



숫자값은 Number 객체 래퍼로 박싱가능 -> Number.prototype 메서드 접근 가능.

```javascript
var a = 42.59;
a.toFixed(0); // 43
a.toFixed(1); // 42.6
a.toFixed(3); // 42.590

// 유효숫자 toPrecision()
a.toPrecision(1); // 4e+1
a.toPrecision(2); // 43
```

숫자 리터럴로 접근할때는 조심

```javascript
//잘못됨
42.toFixed(3); // SyntaxError

// 올바름
(42).toFixed(3); // 42.000
0.42.toFixed(3); // 0.420
42..toFixed(3); // 42.000
42 .toFixed(3); // 42.000 // 42 뒤에 스페이스
```



2, 8, 16진

```javascript
0b11110011 // 243
0B11110011 // 243

0o363 // 243
0O363 // 243 0과 O(알파벳 오)가 헷갈리므로 소문자 사용하자

0xf3 // 243
0Xf3 // 243
```



#### 부동소수점 문제

```javascript
0.1 + 0.2 === 0.3 // false
```



epsilon을 이용한 비교

```
// Number.EPSILON 은 ES6에 정의
// 아래는 Polyfill 코드
if(!Number.EPSILON){
	Number.EPSILON = Math.pow(2, -52);
}

function numbersCloseEnoughToEqual(n1, n2){
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}

numbersCloseEnoughToEqual( 0.1 + 0.2 , 0.3 ); // true;
numbersCloseEnoughToEqual( 0.000001, 0.000002 ) // false;
```



최대값은 대략 1.798e+308 ( Number.MAX_VALUE )

최소값은 5e-324 ( Number.MIN_VALUE )



#### 안정한 정수 범위

```javascript
Number.MAX_SAFE_INTEGER // 2^53-1
Number.MIN_SAFE_INTEGER // -(2^53-1)
```



#### 정수인지 확인

```javascript
Number.isInteger( 42 ) // true
Number.isInteger( 42.000 ) // true
Number.isInteger( 42.3 ) // false

// Polyfill
if(!Number.isInteger){
	Number.isInteger = function(num){
        return typeof num == "number" && num % 1 == 0;
    }
}
```



안정한 정수 확인

```javascript
Number.isSafeInteger( Number.MAX_SAFE_INTEGER ) // true

// Polyfill
if(!Number.isSafeInteger){
	Number.isSafeInteger = function(num){
		return Number.isInteger( num ) &&
        	   Math.abs( num ) <= Number.MAX_SAFE_INTEGER;
    }
}
```



#### 32비트 부호있는 정수

비트 연산자의 경우 32비트까지만 가능하므로 32비트 부호있는 정수로 강제 변환

```javascript
a | 0
```



#### Undefined

```javascript
function foo(){
    undefined = 2; // 느슨한 모드에서는 가능하지만 비추천
}

function foo(){
    "use strict";
    undefined = 2; // 타입 에러 발생
}

function foo(){
	"use strict";
    var undefined = 2; // 지역변수 undefined는 언제나 생성 가능
}
```



#### void 연산자

void 연산자는 항상 undefined를 리턴함

```javascript
var a = 42;
void a; // undefined
a; // 42;
```



#### NaN 

유효하지 않은 숫자

```javascript
var a = 2 / "foo"; // NaN
typeof a === "number" // true
```

NaN은 다른 어떤 NaN과도 동등하지 않음. 자기 자신과도.

```javascript
NaN !== NaN // true
var a = 2 / "foo"
a == NaN; // false
a === NaN; // false
```



NaN 체크

```javascript
// NaN, '숫자 아님' 모두 체크
isNaN( 2 / "foo" ) // true
isNaN( "foo" ) // true

// Polyfill
if(!Number.isNaN){
    Number.isNaN =function(n){
		return (
        	typeof n === 'number' && window.isNaN( n )
        );
    };
}

Number.isNaN( 2 / 'foo' ) // true
Number.isNaN( 'foo' ) // false 

// NaN의 특성을 이용한 Polyfill
if(!Number.isNaN){
    Number.isNaN = function(n){
		return n !== n;
    }
}
```



#### 무한대

```javascript
var a = 1 / 0; // Number.POSITIVE_INFINITY
```

무한대 / 무한대 === NaN



#### 0

```javascript
var a = 0 / -3; // -0
var b = 0 * -3; // -0
```

-0을 문자열화(stringify) 하면 "0"

```javascript
var a = 0 / -3;

a; // -0

a.toString(); // '0'
a + ""; // '0'
String(a) // '0'
JSON.stringify(a) // '0'
```

문자에서 숫자로 바꾸면 있는 그대로

```javascript
+"-0" // -0
Number("-0"); // -0
JSON.parse("-0"); // -0
```



비교할때는 0 === -0

```javascript
var a = 0;
var b = 0 / -3;

a == b // true
-0 == 0 // true
a === b // true
-0 === 0 // true
0 > -0 // false
a > b  // false

function isNegZero(n){
    n = Number(n);
    return (n === 0) && (1 / n === -Infinity);
}
isNegZero(-0) // true
isNegZero( 0 / -3 ) // true
isNegZero( 0 ) // false
```



#### 동등비교

```javascript
var a = 2 / 'foo';
var b = -3 * 0;

Object.is( a, NaN ) // true
Object.is( b, -0 ) // true
Object.is( b, 0 ) // false

// Polyfill
if(!Object.is){
	Object.is = function(v1, v2){
        // '-0' 테스트
        if( v1 === 0 && v2 === 0 ){
            return 1 / v1 === 1 / v2;
        }
        // 'NaN' 테스트
        if( v1 !== v1 ){
            return v2 !== v2;
        }
        // 기타
        return v1 === v2;
    };
}
```



#### 값 vs 레퍼런스

null, undefined, string, number, boolean, symbol은 값 복사.

객체나, 함수 등 합성 값은 레퍼런스 생성

```javascript
function foo(x){
    x.push(4);
    x; // [1,2,3,4];
    
    x = [4,5,6]; // x가 바뀌는 것이지 a가 바뀌는게 아님
    x.push(7);
    x; // [4,5,6,7];
}

var a = [1,2,3];

foo(a);

a; // [1,2,3,4]
```

