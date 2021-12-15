# new 原理
- 基本用法
    ```javascript
    function Person(name){
        this.name = name;
    }
    
    zhangsan = new Person("张三");
    console.log(zhangsan.name)  // "张三"
    ```
- new 经历的具体操作：
    1. 创建对象：`var obj = new object()`
    2. 设置原型链：`obj.__proto__ = Constructor.ptototype`
    3. 在obj空间中执行构造函数：`var result = Constructor.call(obj, ...)`
    4. 根据result类型判断最终返回的类型：
        ```javascript
        if (typeof(result) == "object"){  
            final_obj = result;  
        } else {  
            final_obj = obj;
        }  
        ```