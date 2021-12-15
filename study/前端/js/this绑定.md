# this 绑定
1. 默认绑定
    ```javascript
    function f(){console.log("in window")}  
    window.f();  // "in window"
    f();  // "in window"
    
    function f(){
        msg = "123";
        console.log(window.msg)  // 123
    }
    
    function f(){
        var msg;
        msg = "123";
        console.log(window.msg)  // undefined
    }
    ```
2. 隐式绑定
    ```javascript
    // 举例 1：
    var obj = {
        msg: "in obj",
        f: function () {
            console.log(this.msg)
        }
    }
    obj.f(); // "in obj"
 
    // 举例 2：
    function f() {
        console.log(this.msg)
    }
    var obj = {
        msg: "in obj",
        f: f
    }
    obj.f(); // "in obj"
 
    // 举例 3：
    msg = "in window"
    var obj = {
        msg: "in obj",
        f: function () {
            console.log(this.msg)
        }
    }
    f = obj.f(); 
    f()  // "in window"；原因是fire是默认绑定在window中的
    ```
3. 显式绑定
    ```javascript
    var obj = {
        msg: "in obj",
        f: function () {
            console.log(this.msg)
        }
    }
     
    var msg = "in window";  
    var f = obj.f;
    f();  // "in window"
    f.call(obj);  // "in obj"；call(module, param1, param2, ...)
    
    f.bind(obj)  // bind(module, param1, param2, ...)
    f()  // "in obj"
    
    f.apply(obj)  // apply(module, [param1, param2, ...])
    ```
4. new绑定
    ```javascript
    function Init(msg){
        this.msg = msg;
    }
    
    var obj1 = new Init("obj1");
    console.log(obj1.msg);  // "obj1"
    var obj2 = new Init("obj2");
    console.log(obj2.msg);  // "obj2"
    // 4. 根据构造函数结果判断最终生成的obj
    //    if (typeof(result) == "object"){  
    //        final_obj = result;  
    //    } else {  
    //        final_obj = obj;
    //    }
    ```
    
5. 绑定优先级：new绑定>显式绑定>隐式绑定>默认绑定