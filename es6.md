# ES6 features

Спецификация ECMAScript 2015 дополнила, расширила и упростила инструменты языка

* [Определение переменных и констант](https://github.com/filonitta/nodejs-lecture/blob/master/es6.md#Определение-переменных-и-констант)
* [Оператор rest/spread](https://github.com/filonitta/nodejs-lecture#оператор-restspread)
* [Стрелочные функции](https://github.com/filonitta/nodejs-lecture#стрелочные-функции)
* [Деструктуризация](https://github.com/filonitta/nodejs-lecture#деструктуризация)
* [ООП](https://github.com/filonitta/nodejs-lecture#ооп)
* [Promise](https://github.com/filonitta/nodejs-lecture#promise)
* [asinc/await](https://github.com/filonitta/nodejs-lecture#asincawait)
* [Модули](https://github.com/filonitta/nodejs-lecture#модули)

## Определение переменных и констант

На смену ключевому слову `var` пришли два новых: `let` и `const`

#### #1
Переменные, объявленные с помощью ключевого слова `var` до непосредственного присвоение им значения, получают значение `undefined` (hoisting). Интерпретатор знает об их существовании, хотя не знает об их значении.
```javascript
alert(x); // undefined
var x = 10;
alert(x); // 10
```
Переменные, объявленные с помощью ключевого слова `let` не имеют свойства "поднятия", следовательно, обращаться к ним до объявления – нельзя
```javascript
alert(x); // ReferenceError: Cannot access 'x' before initialization
let x = 10;
alert(x);
```

#### #2
С использованием `var` повторное объявление одноименной переменной *не делает ничего*
```javascript
var x = 'hello';
alert(x);
var x = 'world';
alert(x);
```

С использованием `let` повторное объявление одноименной переменной *заканчивается ошибкой*
```javascript
let x = 'hello';
alert(x);
let x = 'world'; // SyntaxError: Identifier 'x' has already been declared
alert(x);
```

#### #3
Область видимости let-переменной – блок кода, в то время как для var-переменной область видимости - тело функции
```javascript
{
	let greeting = 'hello world';
}
alert(greeting); // ReferenceError: greeting is not defined
```

#### #4
Ключевое слово `const` позволяет определить неизменяемую переменную
```javascript
const START_DATE = 0;
START_DATE = 1; // TypeError: Assignment to constant variable.
```
Изменить значение, хранящееся в неизменяемой переменной, при этом можно
```javascript
const data = [];
data.push(1, 2, 3);
console.log(data); // [1, 2, 3]

data = [];  // TypeError: Assignment to constant variable.
```
В остальном `const` подобен `let` (см. #1, #2, #3)

## Оператор rest/spread
Оператор `...` в разных контекстах может играть разную роль

В списке формальных аргументов функции оператор аккумулирует фактически переданные параметры в массив и носит название **rest**
```javascript
function hello(...args) {
	alert(`Hello ${args.join(', ')}`);
}
hello('John', 'Mary');
```

Применительно к *итерабельным* объектам, как массивы или строки, оператор `...` "разбирает" объект на список элементов и называется **spread**
```javascript
const numbers = [1, 1, 2, 3];
const maxValue = Math.max(...numbers);
alert(maxValue);
```

## Стрелочные функции
Помимо двух типов функций *function declaration* и *functional expression*, появился тип функции - *стрелочна*
```javascript
// function declaration
function sum(a, b) {
	alert(a + b);
}

// functional expression
var sum = function(a, b) {
	alert(a + b);
}

// arrow functional expression
let sum = (a, b) => a + b;
```
**#1** Если тело функции состоит из одной строки, стрелочная функция позволяет реализовать *неявный возврат значения*

**#2** Стрелочная функция не имеет псевдомассива `arguments`, для получения списка всех параметров используется оператор `...`
```javascript
let showMessage = (text, ...persons) => {
	alert(`${text} ${persons.join(', ')}`);
}

showMessage('Hello to', 'John', 'Jan', 'Ivan');
```

**#3** Стрелочная функция также не имеет своего собственного объекта `this`: контекст выполнения берется из области определения функции
```javascript
let fn = () => {
	console.log(this); // window
}

fn();
```

## Деструктуризация
**Деструктуризация** - механизм разложения сложных объектов на более простые и способ записать отдельные значения сложного объекта в переменные

### Деструктуризация объектов
При необходимости можно менять имя переменной, в которую будет записано соответствующее свойство объекта, а также указывать значение по умолчанию для свойства, отсутствующего в объекте (иначе запишется `undefined`)

```javascript
let user = {
	firstname: 'John',
	lastname: 'Doe',
	age: 20
};
let {firstname: name, age, role = 'Guest'} = user;
console.log(name, age, role); // "John" 20 "Guest"
console.log(lastname); // ReferenceError: lastname is not defined
```

### Деструктуризация массивов
Значения из массива считываются по порядку. Если в массиве значение отсутствует, то в переменную запишется `undefined`, но можно установить значение по умолчанию для таких случаев
```javascript
let results = [{name: 'John'}, '2020-01-28'];

let [user, dateUpdate, currentDate = new Date()] = results;
console.log(user); // {name: "John"}
console.log(dateUpdate); // "2020-01-28"
console.log(currentDate);
```
Также возможно "собрать" последние элементы массива в одну переменную
```javascript
let [firstOfAll, ...rest] = 'сделать дз, покормить хомячка, слетать на Луну'.split(', ');
console.log(`Прежде всего мне надо ${firstOfAll}`);
console.log('Потом я собираюсь:', rest.join(', '));
```

## ООП
```javascript
class Animal {
	constructor(name) {
		this.name = name.trim();
	}

	getName() {
		return this.name;
	}

	setName(value) {
		this.name = value || this.name;
	}
}

class Hamster extends Animal {
	constructor(name) {
		super(name);
	}
}

let littleOne = new Hamster('John');
console.log(littleOne.getName());
```

## Promise
В том случае, когда мы имеем дело с рядом асинхронных операций, то есть подразумевается использование коллбэков, вложенность может достигать устрашающих масштабов (callback hell), и код быстро становится грязным и нечитаемым
```javascript
fs.mkdir('folder', error => {
	// some code here
	fs.writeFile('folder/file.txt', 'Hello', function(error) {
		// some code here
		fs.appendFile('folder/file.txt', ' World', function(error) {
			// some code here
			fs.readFile('folder/file.txt', 'utf-8', function(error, data) {
				console.log(data);
			});
		});
	});
});
```

### new Promise
В стандарте ES6 для организации работы с *асинхронным кодом* добавлен специальный объект – `Promise`

Выполняя асинхронный код, объект `Promise` может находиться в одном из трех состояний: **pending** (ожидание) => **fulfilled** (выполнено успешно), **rejected** (выполнено с ошибкой)

Promise-объект создается с помощью конструктора `new Promise`, который принимает коллбэк функцию с двумя параметрами-функциями: первая инициирует успешное выполнение кода, вторая – выполнение кода с ошибкой
```javascript
let promise = new Promise((_resolve, _reject) => {
	// асинхронный код
	setTimeout(() => {
		let rnd = Math.random();

		if (rnd > 0.5) {
			_resolve(rnd); // успех (Fulfilled)
		} else {
			_reject(rnd); // неудача (Rejected)
		}
	}, 1000);
});
```

### Promise.then
На событие завершения работы асинхронного кода с тем или иным результатом можно подписаться с помощью метода `then`, у которого два параметра-функции: первая выполнится в случае успешного завершения асинхронного кода, вторая - в случае неудачи

Синтаксис: `promise.then(onFulfilled, onRejected)`

```javascript
promise.then(data => {
	console.info('Fulfilled:', data);
}, data => {
	console.error('Rejected:', data);
});
```

#### Обработка ошибки
Для того, чтобы обработать только результат успешного выполнения кода, достаточно опустить второй параметр метода `then`: `promise.then(onFulfilled)`

Для того, чтобы обработать только ошибку, есть два подхода: `promise.then(null, onRejected)` или `promise.catch(onRejected)`

Если в promise-функции выбрасывается исключение с помощью `throw` – это равносильно вызову функции `_reject`

#### Полный пример
```javascript
var promise = new Promise((_resolve, _reject) => {
	// асинхронный код
	setTimeout(() => {
		var rnd = Math.random();

		if (rnd > 0.5) {
			_resolve(rnd); // успех, Fulfilled
		} else {
			_reject(rnd); // неудача, Rejected
		}
	}, 1000);
});

promise.then(data => {
	console.info('Fulfilled:', data);
}, data => {
	console.error('Rejected:', data);
});
```

### Chaining
С помощью промисов можно организовать несколько асинхронных блоков кода в виде последовательности вызовов, взамен множественным вложенным коллбэкам (callback hell)
```javascript
let getData = function(x) {
	return new Promise((resolve, reject) => {
		// здесь мог быть асинхронный запрос на сервер, который возвращает 1
		setTimeout(() => {
			resolve(x + 1);
		}, 1000);
	});
};

getData(0)
.then(response => {
	console.warn(response);
	return getData(response);
})
.then(response => {
	console.warn(response);
	return getData(response);
})
.then(result => {
	console.info(result);
});
```

### Promise.all
Метод `Promise.all()` возвращает объект в состоянии ожидания, и выполнится тогда, когда выполнится каждый из промисов в переданном перечисляемом объекте, либо если будет оклонен хотя бы один из них
```javascript
var getData = function(x) {
	return new Promise((resolve, reject) => {
		// асинхронный код
		setTimeout(() => {
			resolve( Math.round(Math.random() * x) );
		}, 1000);
	});
};

var promises = [
	getData(10),
	getData(100),
	getData(1000)
];

Promise.all(promises).then(result => {
	var [x, y, z] = result;
	console.info('x =', x);
	console.info('y =', y);
	console.info('z =', z);
});
```

### Принудительная промисификация
В случае, когда необходимо начать асинхронную цепочку вызовов, можно использовать методы `Promise.resolve` и `Promise.reject`

#### Promise.resolve
Команда `Promise.resolve([value])` создает успешно выполненное обещание с начальным результатом `value`
```javascript
Promise.resolve(42)
.then(nextFn1)
.then(nextFn2);
```
Этот код аналогичен следующему
```javascript
new Promise(resolve => resolve(42))
.then(nextFn1)
.then(nextFn2);
```

#### Promise.reject
Команда `Promise.reject([value])` создает выполнившееся *обещание* с ошибкой `value`

Этот метод используется редко ввиду его исключительной необходимости

## asinc/await
```javascript
```


## Модули
Концепция модулей предполагает разбиение кода приложения на несколько логических частей – модулей, описывающих какую-либо самостоятельную часть приложения

Каждый модуль находится в отдельном файле, из которых впоследствии "собирается" приложение

Для того, чтобы использовать модули в JavaScript, необходимо пометить главный файл специальным атрибутом `type="module"`

```html
<script type="module" src="index.js"></script>
```
Кроме того, необходимо запустить любой локальный сервер

### export
Модуль предоставляет отдельную область видимости: все, что не было экспортировано, инкапсулируется внутри модуля

Ключевое слово `export` можно ставить перед объявлением переменных, функций или классов либо отдельно

```javascript
export let name = 'John Doe';
```
```javascript
let x = 1, y = 2;
export {x, y}; // экспортировать можно одну или несколько переменных
```
```javascript
let z = Math.random();
export {z as rnd}; // экспортируемой переменной можно давать имя, под которым она будет доступна извне
```
```javascript
export function getName() {
    return name;
}; // для экспорта из модуля у функции обязательно должно быть имя
```
```javascript
export class User {
    constructor(name) {
        this.name = name.trim();
    }
};
```

### import
При помощи ключевого слова `import` другие модули могут подключать экспортированные значения

В результате в файле появятся локальные переменные, импортированные из модуля

```javascript
import { x, y } from './math.js'; // x, y - импортируемые переменные
alert(x + y);
```
```javascript
import { rnd } from './math.js';
```
```javascript
import { getName as getUserName } from './functions.js'; // импортировать можно под другим именем
alert( getUserName() );
```
```javascript
import { User } from './user.js';
```
Можно импортировать сразу все значения из модуля, они станут свойствами объекта, указанного после ключевого слова as

```javascript
import * as data from './data.js';
console.log(data.firstname, data.lastname, data.age);
​
let {firstname, lastname, age} = data; // или можно деструктуризировать объект
console.log(firstname, lastname, age);
```

### export default
Модули могут *экспортировать* как несколько значений, так и одно

Для такой ситуации предусмотрен "экспорт по умолчанию", что позоволяет *импортировать* модуль без фигурных скобок

```javascript
// main.js
export default class Main {...}

// index.js
import Main from './main.js';
```