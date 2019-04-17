# ES6 - Class
- 语法糖，实质就是new 构造函数
    ```javascript
    class Person {
        constructor(name) {
            this.name = name
        }
    }
    typeof Person; // "function"
    console.log(Person === Person.prototype.constructor) // true
    ```
- 构造函数的prototype属性，在 ES6 的“类”上面继续存在。事实上，类的所有方法都定义在类的prototype属性上面。
    ```javascript
    class Person {  
        constructor() {
            // ...
        }  
        toString() {
            // ...
        }
    }
        
    // 等同于
    Person.prototype = {  
        constructor() {},
        toString() {},
    };
    ```
- 类的内部所有定义的方法，都是不可枚举的（non-enumerable）
    - 不可以被for in访问当，不可.keys()访问到，不可JSON.stringify访问到。其中for in会遍历原型链（除开最后的object）。.keys和JSON.stringify只遍历实例本身的属性。
- 实例的属性除非显式定义在其本身（即定义在this对象上），否则都是定义在原型上（即定义在class上）
- 类的方法内部如果含有this，它默认指向类的实例
    - 再次举例this的一个反逻辑的例子：
        ```javascript
        class Logger {
            printName(name = 'there') {
                this.print(`Hello ${name}`);
            }
            print(text) {
                console.log(text);
            }
        }
        const logger = new Logger();
        const { printName } = logger;
        printName(); // TypeError: Cannot read property 'print' of undefined
        ```
    - 原因就是printName方法中，this使用的是默认this。默认this会指向谁调用的printName。这个例子中将printName方法赋值给全局的printName变量。此时调用printName()，在相当于是window.printName。
如果修改这个bug呢？答：将函数内部的this改成指定this，不受外界调用影响。
        - 使用bind，显示给函数绑定this
            ```javascript
            class Logger {
              constructor() {
                this.printName = this.printName.bind(this);
              }
              // ...
            }
            ```
        - 使用箭头函数，箭头函数的this是定义时绑定当时的this，不再受后续调用变化
            ```javascript
            class Logger {
                constructor() {
                    this.printName = (name = 'there') => {
                        this.print(`Hello ${name}`);
                    };
                }
              // ...
            }
            ```

- 类相当于实例的原型，所有在类中定义的方法，都会被实例继承。如果在一个方法前，加上static关键字，就表示该方法不会被实例继承，而是直接通过类来调用，这就称为“静态方法”。注意，如果静态方法包含this关键字，这个this指的是类，而不是实例。
    ```javascript
    class A{constructor(){}}
    class B extends A{constructor(){super();}}  // 子类实现constructor，必须调用super
    console.log(B.prototype.constructor === B)  // true
    console.log(B.prototype.__proto__ === A.prototype)  // true
    ```
- Class 可以通过extends关键字实现继承
- 子类必须在constructor方法中调用super方法，否则新建实例时会报错。这是因为子类自己的this对象，必须先通过父类的构造函数完成塑造，得到与父类同样的实例属性和方法，然后再对其进行加工，加上子类自己的实例属性和方法。如果不调用super方法，子类就得不到this对象。
- 在子类的构造函数中，只有调用super之后，才可以使用this关键字，否则会报错。这是因为子类实例的构建，基于父类实例，只有super方法才能调用父类实例。
- 子类prototype属性的__proto__属性，表示继承关系，总是指向父类的prototype属性。