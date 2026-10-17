## 一、原型链继承

### 概念
- **是什么**：原型链继承是JavaScript实现继承的最基本方式，其核心是将子类的原型（`prototype`）指向父类的一个实例，从而让子类实例能够访问父类实例及其原型上的属性和方法。
- **为什么需要**：在JavaScript中，对象之间存在一种“原型”关系，通过原型链可以实现属性和方法的共享与复用，避免为每个实例重复定义相同的方法。
- **解决什么问题**：它解决了多个对象共享公共方法的问题，实现了基本的继承关系，让子类型能够继承父类型的属性和方法。

### 原理
- **底层实现**：每个构造函数都有一个原型对象（`prototype`），原型对象包含一个指向构造函数的指针（`constructor`），实例包含一个指向原型对象的内部指针（`[[Prototype]]`，通常通过`__proto__`访问）。当设置`SubType.prototype = new SuperType()`时，`SubType.prototype`就成为了`SuperType`的实例，因此`SubType`的实例可以通过原型链访问`SuperType`实例及其原型上的成员。
- **工作流程**：
    1. 创建父类构造函数，定义属性和方法。
    2. 创建子类构造函数。
    3. 将子类的原型对象设置为父类的一个实例。
    4. 在子类原型上添加新方法或覆盖父类方法。
    5. 创建子类实例，访问属性时沿着原型链查找。
- **关键技术点**：原型链的构建、原型与实例的关系、属性查找机制。

### 使用方法
- **基本用法**：
  ```javascript
  function SuperType() {
      this.property = true;
  }
  SuperType.prototype.getSuperValue = function() {
      return this.property;
  };

  function SubType() {
      this.subproperty = false;
  }

  // 继承：将子类原型指向父类实例
  SubType.prototype = new SuperType();

  SubType.prototype.getSubValue = function() {
      return this.subproperty;
  };

  let instance = new SubType();
  console.log(instance.getSuperValue()); // true
  ```
- **常见场景**：适用于所有方法都希望被复用，且不涉及引用类型属性共享问题的简单继承场景。
- **最佳实践**：通常不建议单独使用原型链继承，因为其引用值共享的缺陷难以控制。若确需使用，应确保父类的实例属性中不包含引用类型，或仅在原型上定义方法。

### 注意事项
- **常见错误**：
  - 忘记修正子类原型的`constructor`指向，导致`constructor`指向父类。
  - 在子类原型上添加方法时，必须在继承语句之后，否则会被覆盖。
  - 误以为可以像经典语言一样通过原型链实现多重继承，实际上只能单链继承。
- **性能问题**：属性查找沿原型链进行，过长的原型链会影响查找性能。
- **兼容性**：所有JavaScript引擎均支持，无兼容性问题。

### 代码示例
```javascript
function Animal(name) {
    this.name = name;
    this.colors = ['white', 'black']; // 引用类型
}
Animal.prototype.speak = function() {
    console.log(this.name + ' makes a sound.');
};

function Dog(name) {
    this.name = name;
}
Dog.prototype = new Animal(); // 继承

let dog1 = new Dog('Rex');
let dog2 = new Dog('Buddy');
dog1.colors.push('brown');
console.log(dog2.colors); // ['white', 'black', 'brown']（引用值被共享）
console.log(dog1.speak()); // Rex makes a sound.
```

---

## 二、借用构造函数继承

### 概念
- **是什么**：借用构造函数继承（也称伪造对象或经典继承）是在子类构造函数内部通过`call`或`apply`调用父类构造函数，从而将父类的实例属性复制到子类实例上。
- **为什么需要**：为了解决原型链继承中引用类型值被所有实例共享以及无法向父类传递参数的问题。
- **解决什么问题**：它让每个子类实例拥有独立的父类属性副本，避免了属性共享，并且支持向父类构造函数传递参数。

### 原理
- **底层实现**：函数是JavaScript中的一等公民，可以通过`call`或`apply`改变函数执行时的`this`指向。在子类构造函数中调用父类构造函数，并将子类实例的`this`绑定到父类函数，从而让父类中定义的属性直接添加到子类实例上。
- **工作流程**：
    1. 定义父类构造函数（包含属性和方法）。
    2. 定义子类构造函数，在内部使用`Parent.call(this, ...args)`调用父类。
    3. 创建子类实例时，每个实例都会执行一次父类构造函数，获得独立的属性。
- **关键技术点**：`call`/`apply`的使用、构造函数执行上下文。

### 使用方法
- **基本用法**：
  ```javascript
  function SuperType(name) {
      this.name = name;
      this.colors = ['red', 'blue'];
  }

  function SubType(name, age) {
      SuperType.call(this, name); // 借用构造函数
      this.age = age;
  }

  let instance1 = new SubType('Nicholas', 29);
  instance1.colors.push('green');
  console.log(instance1.colors); // ['red', 'blue', 'green']

  let instance2 = new SubType('Greg', 27);
  console.log(instance2.colors); // ['red', 'blue']（独立）
  ```
- **常见场景**：当需要子类实例拥有独立属性，且需要向父类传参时使用。
- **最佳实践**：通常与原型链继承组合使用（组合继承），很少单独使用，因为方法无法复用。

### 注意事项
- **常见错误**：
  - 忘记在子类构造函数中调用父类构造函数，导致无法继承属性。
  - 在`call`之后继续添加属性可能会覆盖父类同名属性（视需求而定）。
  - 误将方法定义在父类构造函数中，导致每个实例都创建方法副本，浪费内存。
- **性能问题**：每个实例都会创建一份父类方法的副本（如果方法定义在构造函数内），导致内存占用较高。
- **兼容性**：所有JavaScript引擎均支持。

### 代码示例
```javascript
function Person(name, age) {
    this.name = name;
    this.age = age;
    this.greet = function() { // 方法定义在构造函数内，每个实例独立
        console.log('Hello, I am ' + this.name);
    };
}

function Student(name, age, grade) {
    Person.call(this, name, age); // 继承属性
    this.grade = grade;
}

let student1 = new Student('Alice', 20, 3);
let student2 = new Student('Bob', 21, 4);
console.log(student1.greet === student2.greet); // false（方法独立，无法复用）
```

---

## 三、组合继承

### 概念
- **是什么**：组合继承（也称伪经典继承）是将原型链继承和借用构造函数继承结合起来的一种继承模式。它使用原型链继承原型上的属性和方法，通过借用构造函数继承实例属性。
- **为什么需要**：为了同时解决原型链继承的引用值共享问题和借用构造函数继承的方法无法复用问题。
- **解决什么问题**：既保证了每个实例拥有自己的属性副本，又实现了方法的复用，同时支持向父类传参，且能正确使用`instanceof`。

### 原理
- **底层实现**：通过两次继承：第一次在子类构造函数内部使用`Parent.call(this)`继承实例属性；第二次将子类原型设置为父类的一个实例，继承父类原型上的方法。这样，子类实例通过原型链可以访问父类原型上的方法，同时实例本身拥有父类实例属性的副本。
- **工作流程**：
    1. 定义父类构造函数，包含实例属性和原型方法。
    2. 定义子类构造函数，内部调用`Parent.call(this, ...args)`继承属性。
    3. 设置子类原型为父类的一个实例：`SubType.prototype = new Parent()`。
    4. 修正子类原型的`constructor`指向子类自身。
    5. 在子类原型上添加方法。
- **关键技术点**：同时使用构造函数和原型链，处理好`constructor`的修正。

### 使用方法
- **基本用法**：
  ```javascript
  function SuperType(name) {
      this.name = name;
      this.colors = ['red', 'blue'];
  }
  SuperType.prototype.sayName = function() {
      console.log(this.name);
  };

  function SubType(name, age) {
      SuperType.call(this, name); // 继承实例属性
      this.age = age;
  }

  SubType.prototype = new SuperType(); // 继承原型方法
  SubType.prototype.constructor = SubType;
  SubType.prototype.sayAge = function() {
      console.log(this.age);
  };

  let instance1 = new SubType('Nicholas', 29);
  let instance2 = new SubType('Greg', 27);
  instance1.colors.push('green');
  console.log(instance1.colors); // ['red', 'blue', 'green']
  console.log(instance2.colors); // ['red', 'blue']
  instance1.sayName(); // Nicholas
  instance2.sayAge(); // 27
  ```
- **常见场景**：这是ES5时代最常用的继承模式，适用于大多数需要继承的场景。
- **最佳实践**：始终记得修正子类原型的`constructor`，并确保在继承之后添加子类原型方法。

### 注意事项
- **常见错误**：
  - 忘记修正`constructor`，导致类型判断混乱。
  - 在设置原型继承之前给子类原型添加方法，这些方法会被覆盖。
  - 误以为`SubType.prototype = new SuperType()`会继承父类实例属性，实际上这些属性会成为子类原型的属性，被实例属性遮蔽。
- **性能问题**：父类构造函数被调用两次，导致子类原型上存在不必要的父类实例属性（这些属性会被实例属性覆盖，造成内存浪费）。
- **兼容性**：所有JavaScript引擎均支持。

### 代码示例
```javascript
function Vehicle(brand) {
    this.brand = brand;
    this.features = ['engine', 'wheels'];
}
Vehicle.prototype.showBrand = function() {
    console.log('Brand: ' + this.brand);
};

function Car(brand, model) {
    Vehicle.call(this, brand); // 第一次调用
    this.model = model;
}

Car.prototype = new Vehicle(); // 第二次调用
Car.prototype.constructor = Car;
Car.prototype.showModel = function() {
    console.log('Model: ' + this.model);
};

let car1 = new Car('Toyota', 'Camry');
let car2 = new Car('Honda', 'Civic');
car1.features.push('sunroof');
console.log(car2.features); // ['engine', 'wheels']（独立）
console.log(car1.showBrand()); // Toyota
```

---

## 四、原型式继承

### 概念
- **是什么**：原型式继承是一种不涉及严格意义上的构造函数，直接以一个现有对象为模板创建新对象的继承方式。它由道格拉斯·克罗克福德推广，并在ECMAScript 5中被标准化为`Object.create()`。
- **为什么需要**：在某些情况下，我们不需要创建自定义类型，只需要让一个对象与另一个对象保持相似，同时避免复杂的构造函数。
- **解决什么问题**：它提供了一种轻量级的对象继承方式，允许基于现有对象创建新对象，而无需定义构造函数。

### 原理
- **底层实现**：其核心是创建一个临时构造函数，将传入的对象作为该构造函数的原型，然后返回临时构造函数的实例。这样，新对象就可以通过原型链访问传入对象的属性和方法。
- **工作流程**：
    1. 定义一个函数`object(o)`，内部创建空构造函数`F`。
    2. 将`F.prototype`指向传入的对象`o`。
    3. 返回`new F()`，即一个原型为`o`的新对象。
- **关键技术点**：临时构造函数、原型委托。ES5的`Object.create()`直接提供了此功能，并允许第二个参数添加额外属性。

### 使用方法
- **基本用法**：
  ```javascript
  // 手动实现
  function object(o) {
      function F() {}
      F.prototype = o;
      return new F();
  }

  let person = {
      name: 'Nicholas',
      friends: ['Shelby', 'Court']
  };

  let anotherPerson = object(person);
  anotherPerson.name = 'Greg';
  anotherPerson.friends.push('Rob');

  let yetAnotherPerson = object(person);
  yetAnotherPerson.name = 'Linda';
  yetAnotherPerson.friends.push('Barbie');

  console.log(person.friends); // ['Shelby', 'Court', 'Rob', 'Barbie']（共享）
  ```
- **ES5标准化**：
  ```javascript
  let anotherPerson = Object.create(person, {
      name: { value: 'Greg' }
  });
  ```
- **常见场景**：适用于不需要创建构造函数，只需让一个对象与另一个对象类似，且不介意引用值共享的情况。
- **最佳实践**：当需要基于现有对象创建新对象，且可以接受引用值共享时，优先使用`Object.create()`。如果希望新对象拥有自己的属性，可以通过第二个参数定义。

### 注意事项
- **常见错误**：
  - 误以为原型式继承会复制对象属性，实际上是通过原型委托，因此引用类型属性会被共享。
  - 忘记`Object.create()`的第二个参数是属性描述符对象，直接传入普通对象会导致错误。
- **性能问题**：原型链查找同样存在性能开销，但通常可以忽略。
- **兼容性**：手动实现兼容所有环境；`Object.create()`在IE9及以上支持，旧环境需要polyfill。

### 代码示例
```javascript
let base = {
    type: 'animal',
    sound: function() {
        console.log('Making sound');
    }
};

let cat = Object.create(base);
cat.name = 'Whiskers';
cat.sound(); // Making sound
console.log(cat.type); // animal
```

---

## 五、寄生式继承

### 概念
- **是什么**：寄生式继承是与原型式继承紧密相关的一种模式，它创建一个仅用于封装继承过程的函数，在函数内部增强对象（添加属性和方法），然后返回该对象。
- **为什么需要**：当我们需要在基于某个对象创建新对象的同时，为新对象添加额外的方法或属性，而又不想创建新类型时，寄生式继承提供了一种简洁的方式。
- **解决什么问题**：它在原型式继承的基础上，允许动态地为对象添加功能，实现对象的“增强”。

### 原理
- **底层实现**：寄生式继承的核心是一个寄生函数，该函数接受一个对象作为模板，首先通过原型式继承（如`Object.create()`）创建一个新对象，然后以某种方式增强这个新对象（添加方法/属性），最后返回增强后的对象。
- **工作流程**：
    1. 定义寄生函数，接收基础对象。
    2. 在函数内部使用`Object.create()`创建基础对象的副本。
    3. 为副本添加所需的属性和方法。
    4. 返回副本。
- **关键技术点**：函数封装、对象增强、原型式继承。

### 使用方法
- **基本用法**：
  ```javascript
  function createAnother(original) {
      let clone = Object.create(original); // 创建副本
      clone.sayHi = function() {            // 增强对象
          console.log('hi');
      };
      return clone;
  }

  let person = {
      name: 'Nicholas',
      friends: ['Shelby', 'Court']
  };

  let anotherPerson = createAnother(person);
  anotherPerson.sayHi(); // 'hi'
  ```
- **常见场景**：当我们需要基于某个对象创建一系列相似对象，但每个对象可能需要额外的方法时，可以使用寄生式继承。例如，为从数据库获取的默认对象添加业务方法。
- **最佳实践**：寄生式继承适合在临时需要为对象添加功能时使用，但如果需要大量创建对象，应考虑构造函数模式以提高复用性。

### 注意事项
- **常见错误**：
  - 将方法直接定义在寄生函数内部，导致每个对象都创建独立的方法副本，无法复用。
  - 误以为寄生式继承会保持与原型对象的引用关系，实际上通过`Object.create()`创建的是新对象，原型指向原对象。
- **性能问题**：与借用构造函数类似，每个对象都会创建自己的方法，占用额外内存。
- **兼容性**：依赖`Object.create()`，IE9+支持；可手动实现原型式继承兼容旧环境。

### 代码示例
```javascript
let vehicle = {
    move: function() {
        console.log('Moving');
    }
};

function createCar(original) {
    let car = Object.create(original);
    car.drive = function() {
        console.log('Driving');
    };
    return car;
}

let myCar = createCar(vehicle);
myCar.move(); // Moving
myCar.drive(); // Driving
```

---

## 六、寄生组合式继承

### 概念
- **是什么**：寄生组合式继承是ES5时代最理想的继承范式，它通过借用构造函数继承属性，通过原型链的混成形式继承方法。其核心是不再通过调用父类构造函数来设置子类原型，而是创建一个父类原型的副本来赋值给子类原型。
- **为什么需要**：为了解决组合继承中父类构造函数被调用两次的问题（一次在创建子类原型时，一次在子类构造函数内部），避免子类原型上产生不必要的父类实例属性，提高效率。
- **解决什么问题**：它实现了只调用一次父类构造函数，同时保持原型链不变，属性独立、方法复用，且`instanceof`和`isPrototypeOf`正常工作。

### 原理
- **底层实现**：使用`Object.create()`创建父类原型的副本，将该副本作为子类的原型，并修正`constructor`指向。这样，子类原型只包含父类原型上的方法，而不包含父类实例属性。实例属性仍然通过`Parent.call(this)`在子类构造函数中获取。
- **工作流程**：
    1. 定义父类构造函数和原型方法。
    2. 定义子类构造函数，内部调用`Parent.call(this)`继承实例属性。
    3. 创建一个原型对象：`let prototype = Object.create(Parent.prototype)`。
    4. 设置该原型对象的`constructor`为子类。
    5. 将子类的`prototype`指向该原型对象。
    6. 在子类原型上添加额外方法。
- **关键技术点**：`Object.create()`、原型副本、constructor修正。

### 使用方法
- **基本用法**：
  ```javascript
  function inheritPrototype(subType, superType) {
      let prototype = Object.create(superType.prototype);
      prototype.constructor = subType;
      subType.prototype = prototype;
  }

  function SuperType(name) {
      this.name = name;
      this.colors = ['red', 'blue'];
  }
  SuperType.prototype.sayName = function() {
      console.log(this.name);
  };

  function SubType(name, age) {
      SuperType.call(this, name);
      this.age = age;
  }

  inheritPrototype(SubType, SuperType);

  SubType.prototype.sayAge = function() {
      console.log(this.age);
  };
  ```
- **常见场景**：任何需要继承且追求最优性能和正确性的场景，都应优先考虑寄生组合式继承（在不使用ES6 class的情况下）。
- **最佳实践**：将`inheritPrototype`函数封装为工具函数，方便复用。确保在继承之后再添加子类原型方法。

### 注意事项
- **常见错误**：
  - 忘记修正`constructor`，导致类型判断错误。
  - 使用`Object.create(superType.prototype)`后，直接修改`prototype`会影响父类原型（实际上不会，因为创建的是副本）。
  - 在`inheritPrototype`之前给子类原型添加方法，这些方法会被覆盖。
- **性能问题**：相比组合继承，它只调用一次父类构造函数，避免了不必要的属性创建，内存利用率更高。
- **兼容性**：需要`Object.create()`支持（IE9+），可手动实现原型式继承作为polyfill。

### 代码示例
```javascript
function Animal(name) {
    this.name = name;
    this.features = ['fur', 'eyes'];
}
Animal.prototype.eat = function() {
    console.log(this.name + ' is eating');
};

function Bird(name, wingspan) {
    Animal.call(this, name);
    this.wingspan = wingspan;
}

// 寄生组合继承
Bird.prototype = Object.create(Animal.prototype);
Bird.prototype.constructor = Bird;
Bird.prototype.fly = function() {
    console.log(this.name + ' is flying');
};

let eagle = new Bird('Eagle', 200);
let sparrow = new Bird('Sparrow', 30);
eagle.features.push('sharp beak');
console.log(sparrow.features); // ['fur', 'eyes']（独立）
eagle.eat(); // Eagle is eating
```

---

## 七、ES6 Class继承

### 概念
- **是什么**：ES6引入了`class`关键字，提供了更清晰、更接近传统面向对象语言的语法来定义类和实现继承。尽管语法不同，其底层依然基于原型链，本质是寄生组合式继承的语法糖。
- **为什么需要**：为了简化面向对象编程的复杂性，让代码更易读、更易维护，同时保持JavaScript的原型本质。
- **解决什么问题**：它解决了ES5中继承实现代码冗长、容易出错的问题，提供了更标准化的继承方式，并引入了`extends`和`super`关键字来处理继承关系。

### 原理
- **底层实现**：`class`定义的类本质是一个函数。`extends`关键字会建立两个原型链：子类的`prototype`的`[[Prototype]]`指向父类的`prototype`（用于实例方法继承），同时子类本身的`[[Prototype]]`指向父类（用于静态方法继承）。`super`关键字在构造函数中相当于调用父类构造函数，在方法中相当于调用父类原型上的方法，其内部通过`[[HomeObject]]`机制实现正确绑定。
- **工作流程**：
    1. 使用`class`定义类，`constructor`方法用于初始化实例。
    2. 使用`extends`指定父类。
    3. 在子类`constructor`中必须先调用`super()`才能使用`this`，确保父类实例正确初始化。
    4. 可以在子类方法中通过`super.method()`调用父类方法。
- **关键技术点**：`class`语法、`extends`、`super`、静态方法继承、`[[HomeObject]]`。

### 使用方法
- **基本用法**：
  ```javascript
  class Animal {
      constructor(name) {
          this.name = name;
      }
      speak() {
          console.log(`${this.name} makes a sound.`);
      }
      static info() {
          console.log('This is an animal class');
      }
  }

  class Dog extends Animal {
      constructor(name, breed) {
          super(name); // 调用父类构造函数
          this.breed = breed;
      }
      speak() {
          super.speak(); // 调用父类方法
          console.log(`${this.name} barks.`);
      }
      static info() {
          super.info(); // 调用父类静态方法
          console.log('This is a dog class');
      }
  }

  let dog = new Dog('Rex', 'German Shepherd');
  dog.speak(); // Rex makes a sound.  Rex barks.
  Dog.info(); // This is an animal class  This is a dog class
  ```
- **常见场景**：所有需要定义类和继承的现代JavaScript项目，尤其是使用ES6及以上版本的环境。
- **最佳实践**：
  - 始终在子类构造函数中首先调用`super()`。
  - 使用`super`调用父类方法，避免直接通过父类原型调用，以确保正确的`this`上下文。
  - 合理使用静态方法继承，实现类级别的功能复用。

### 注意事项
- **常见错误**：
  - 在子类构造函数中使用`this`前忘记调用`super()`，导致引用错误。
  - 误以为`class`是全新的面向对象模型，实际上仍是原型继承，因此依然需要注意引用类型属性的共享（虽然实例属性通常在构造函数中定义，避免了共享，但原型上的引用类型仍然共享）。
  - 试图在类外部直接修改原型可能导致意料之外的行为，应尽量在类定义内完成。
- **性能问题**：`class`的性能与构造函数相当，现代引擎有优化；但过度使用`super`或深层继承可能带来微小开销。
- **兼容性**：ES6 class在主流现代浏览器中均支持（IE不支持），旧环境需要通过Babel等工具转译为ES5。

### 代码示例（包含super的各种用法）
```javascript
class Rectangle {
    constructor(width, height) {
        this.width = width;
        this.height = height;
    }
    get area() {
        return this.width * this.height;
    }
    static describe() {
        return 'I am a rectangle';
    }
}

class Square extends Rectangle {
    constructor(side) {
        super(side, side); // 调用父类构造函数
    }
    get area() {
        return super.area; // 调用父类getter
    }
    static describe() {
        return super.describe() + ' and also a square'; // 调用父类静态方法
    }
}

let sq = new Square(5);
console.log(sq.area); // 25
console.log(Square.describe()); // I am a rectangle and also a square
```

---

以上笔记涵盖了JavaScript中从ES5到ES6的所有主要继承方式，并按照您提供的模板进行了详细整理。每个知识点都包含了概念、原理、使用方法、注意事项和代码示例，力求完整且深入。