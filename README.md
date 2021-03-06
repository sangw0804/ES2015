 ES2015
============

<br>

- 그 전에...
***

## 1. 클로져

```javascript
const outter = () => {
    let data = "remember me!";
    let data2 = "me too!"; //inner 함수에서 사용되지 않았으므로 클로져 처리 x
    const inner = () => {
        return data;
    };
    return inner;ㅍ
};

let innerVal = outter();
innerVal(); // "remember me!"
```
- 클로져를 사용하려면 안쪽 함수를 return 해야함.

- 즉, 안쪽 함수를 return 할 때, 상위 함수의 변수중 사용하는 것이 있다면 해당 변수는 할당 해제되지 않는다!

- 클로져를 이용해 자바스크립트에 없는 private 변수를 만들 수 있다!!!

```javascript
const thisWorksAsConstructor = () => {
    let dataPrivate = ["initialData"];
    return {
        getData: () => dataPrivate,
        setData: (data) => {
            dataPrivate.push(data);
        }
    };
};
```

## 2. this

  - this를 호출한 _함수_ 의 context에 따라 의미가 달라지는 예약어.

 1. 전역에서 호출했을 경우
 ```javascript
 console.log(this); // 이 경우 this 는 웹 환경에서 전역 객체인 window 객체를 의미한다.

 const fn1 = () => {
     console.log(this); // 함수 fn1은 전역에서 정의 되었으므로 window객체의 속성이다. 따라서 fn1안에서 this는 window객체를 의미한다.
     this.makeThisGlobal = "hoho"; //이런식으로 전역 변수를 생성할 수 있다.
 };

 fn1(); // window 객체
 console.log(makeThisGlobal); // "hoho"
 ```

 2. 객체 안에서 호출했을 경우 & 3. call,bind,apply

 ```javascript
 let person = {
    name: "james",
    sayHello: function () {
        return "Hello "+this.name;
    },
    isThis: this, // this를 설정하는 것은 함수! 이 경우 this를 함수안에서 호출하지 않았기 때문에 this의 값은 전역객체 window로 유지된다.
    dog: {
        bark: function () {
            return "bow! "+this.name; //함수가 새로 선언되었으므로 this의 값이 상위 객체 dog로 설정된다. 하지만 dog객체에는 name속성이 없기에 bark함수를 실행하면 "bow! undefined"가 출력된다.
        }
    },
    asyncFunc: function() {
        setTimeout(function () {
            console.log(this); //this는 전역객체 window를 의미한다.
        } ,2000);
    }
 };

 //위와 같은 문제를 해결하기 위한 메소드가 call,apply,bind이다.
 person.dog.bark.call(person); // "bow! james"
 let barkForPerson = person.dog.bark.bind(person);
 barkForPerson(); // "bow! james"

 ```

 4. 함수 앞에 new 생성자가 있을 경우
 - 해당 생성자가 생성한 인스턴스 객체를 의미한다.

## 3. new

> oop가 built-in 되어있지 않는 javascript가 oop를 구현하기 위한 방법.

```javascript
function Dog(name, age) {
    this.name = name;
    this.age = age;
    this.bark = function () {
        console.log(this.name+" just barked!");
    }
}



let rusty = new Dog("rusty", 12);
rusty.bark() // "rusty just barked!"

// ES2015이후
class Dog {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }

    bark() {
        console.log(this.name+ " just barked!");
    }

    static isDog(obj) {
        return obj instanceof Dog;
    }
}

let rusty = new Dog("rusty", 12);
Dog.isDog(rusty); // true

```

- new 예약어가 하는 일
  - 빈 object 생성
  - 함수의 this에 object 연결
  - __proto__속성에 프로토타입 객체 추가
  - 객체를 return해준다.

## 4. prototype

> 만약 어떤 객체에게 a라는 속성이 없다면, 자바스크립트 실행기는 해당 객체의 prototype, 즉 \_\_proto\_\_ 속성에서 해당 속성을 다시 찾는다. 따라서 해당 객체의 생성자 함수의 prototype을 이용해 그 생성자로 만든 모든 인스턴스 객체가 공유하는 속성을 설정해 줄 수 있다. 또한 prototype을 카피해 다른 생성자 함수의 prototype으로 설정할 수도 있다. 자바스크립트에서 상속을 구현하는 방법인 셈이다.

```javascript
function Car(name) {
    this.name = name;
}

let car = new Car("audi");
car.__proto__; // 이 객체의 프로토타입, 즉 Car.prototype에 접근.

Car.prototype.honk = function() {
    return this.name + " is honking!!!";
}

let car2 = new Car("turismo");

car.honk(); // "turismo is honking!!!";

function ExpensiveCar(name) {
    Car.apply(this, arguments);
}

ExpensiveCar.prototype = Object.create(Car.prototype);
ExpensiveCar.prototype.constructor = ExpensiveCar;
let eCar = new ExpensiveCar("mercedeze");

eCar.honk(); // "mercedeze is honking!!!"


///ES2015 이후
class Animal {
    constructor(legs, isCute) {
        this.legs = legs;
        this.isCute = isCute;
    }

    run() {
        return legs ? "다다다다ㅏ" : "시무룩";
    }
}

class Pig extends Animal {
    constructor(legs, isCute) {
        super(legs, isCute);
    }

    oink() {
        return "oink oink";
    }
}

let piggy = new Pig(4, true);
piggy.run(); // "다다다다ㅏ"
piggy.oink(); // "oink oink"
```

> ES2015 이후 추가된 class, extends, super 같은 기능들은 내부적으로는 기존의 object와 function, prototype을 사용하던 방식 그대로 작동한다!

<br>

- ES2015 새 문법들!
***

## 5. const & let

- 기존에 존재하던 var 키워드를 대체하기 위해 등장한 키워드들.

```javascript
const a = 1;
const b = [1,2,3];

a = 2; // 에러 발생! const 변수는 재 할당 불가
b.push(4); // 하지만 할당이 아닌 값의 변화는 줄 수 있음. 객체도 마찬가지!

if(true) {
    // var은 function scope, let과 const는 block scope
    let i = "let";
    var j = "var";
}

console.log(i); // undefined
console.log(j); // "var"

(function() {
    // var var1; 호이스팅 발생
    // let 변수도 호이스팅이 생기지만 접근하려고 하면 에러를 발생시킨다.
    console.log(var1); // undefined
    console.log(var2); // reference error 발생!
    var var1 = "hoisting";
    let var2 = "not hoisting";
})();
```

<br>

## 6. Arrow Function

- this 와 arguments를 자체적으로 설정하지 못함.
  - 따라서 rest연산자를 사용해주어야 함.

- 매개 변수가 1개일 경우 () 생략가능, return {} 생략가능.

```javascript
const af = () => {
    console.log(this); // 전역객체 window
    console.log(arguments); // reference error
};

const af2 = (...args) => {
    console.log(args);
}

af2(1,2,3); // [1,2,3];

const af3 = (...args, wrong) => { // 에러 발생
}

// spread 연산자
const needTwoParam = (one,two) => {
    console.log(`${one} and ${two}`);
}

let arr = ["first", "second"];
needTwoParam(arr); // ["first","second"] and undefined
needTwoParam(...arr); // first and second
...arr // 에러 발생!! spread 연산자는 순차적 요소가 필요한 위치에만 사용 가능하다.
```

## 7. Destructuring

```javascript
let obj = {one: 1, two: 2};
let {one, two} = obj;
console.log(one, two); // 1, 2

let {one: first, two: second} = obj;
console.log(first, second); // 1, 2

let arr = [1,2,3];
let [a,b,c] = arr;
console.log(a, b, c); // 1, 2, 3

const fn = ({name, age = 1} = {}) => {
    return [name, age];
}

fn({name:"woonjang", age: 25}); //["woonjang", 25]
fn({name:"jungmin"}); // ["jungmin",1]
fn(); // [undefined,1]
```

## 8. Array 메소드들

```javascript
let arr = [1,2,3,4,5];

// Array.map(callback) 콜백 함수로 배열의 요소를 하나씩 전달해서 리턴값으로 이루어진 배열을 리턴한다.
arr.map(val => val*2); // [2,4,6,8,10];

// Array.filter(callback) 콜백 함수로 배열의 요소를 하나씩 전달해서 리턴값이 truthy 한 요소들만으로 이루어진 배열을 리턴한다.
arr.filter(val => val % 2); // [1,3,5];

// Array.reduce(callback) 콜백 함수를 이용해 배열의 요소들을 하나씩 줄여나가며 하나의 값을 만들어 리턴한다.
arr.reduce((accu, next) => accu + next); // 15

// Array.slice(start, [end]) 주어진 위치의 배열을 복사해서 리턴한다.
arr.slice(0, 3); // [1,2,3]

// Array.splice(start, deleteCount[, item1...] ) 주어진 위치에서 주어진 개수만큼 요소를 제거한 뒤 아이템을 그 위치에 삽입한 뒤, 삭제된 요소들을 배열로 반환.
arr.splice(1,2,6,7); // [2,3]
console.log(arr) // [1,6,7,4,5]
```

## 9. Map & Set

```javascript
let map = new Map;
map.set(true, "hi");
map.set([1], {first:2});
map.size // 2
```
 - object와 비슷하나 key 값으로 string 뿐 아니라 어떤 타입의 데이터도 올 수 있고, size 측정이 쉽다는 특징이 있다.

 ```javascript
 let set = new Set;
 set.add(1);
 set.add(2);
 set.add(1); // 중복은 무시
 set.size; // 2

 set.has(1); //true
 ```

 - 집합을 나타내는 자료형. 중복 불가, 순서 없음, size 속성이 있음.

 ## 10. Promise

- 비동기 처리는 코드가 실행되고 해당 처리가 끝나기 전까지 무엇이 리턴될지 알 수 없다.
- 따라서 해당 자리에 무엇인가가 비동기적으로 리턴될 것이다 라고 약속하는 것이 Promise 객체이다.
```javascript
const asyncfunc = () => {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(Math.random() > 0.5) {
                resolve("success!");
            } else {
                reject("failure!");
            }
        }, 2000);
    });
}

asyncfunc().then((result) => console.log(result) ).catch((err) => console.log(err) );

// 이런식으로 then 안에서 다시 Promise를 만들어 연쇄적으로 비동기 처리를 하는 것이 가능하다!
asyncfunc().then((result) => {
    console.log(result);
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if(Math.random() > 0.5) {
                resolve("success, again!");
            } else {
                reject("failure!");
            }
        }, 2000);
    })
}).then((result) => console.log(result) ).catch((err) => console.log(err));
```

## 11. Generator


## 12. and other usefull methods..

- object reference issue
> 원시 자료형을 제외한 데이터들(배열, 객체 등)은 변수에 값이 아닌 참조(reference)가 저장된다. 따라서 다음과 같은 문제가 쉽게 발생한다.
```javascript
let obj = {name:"ingyu"};
let obj2 = obj;
obj2.name = "babo";
console.log(obj); // {name:"babo"}
```
> ES2015에서는 이런 문제를 해결하기 위한 손쉬운 방법을 제공한다.

- Object.assign()
```javascript
let obj = {name:"sangwoo"};
let obj2 = Object.assign({}, obj); // 이 함수는 원래 주어진 두개의 객체를 병합해서 새로운 객체를 리턴해주는 함수이다.
```

- Array.from()
```javascript
let divs = document.getElementsByTagName("div");
divs.forEach(div => console.log(div)); // divs는 배열이 아니므로 에러 발생!!

let arrDivs = Array.from(divs);
arrDivs.forEach(div => console.log(div)); // 성공!
```
