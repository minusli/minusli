# for of VS for in.md
- for of：是借助object[Symbol.iterator]的实现遍历结果。不是所有对象都有Symbol.iterator。所以不是所有对象都能通过for of来遍历。
- for in：是遍历object的属性key（该key需要是可枚举的才行）
    - 举例：
        ```javascript
        let list = [4, 5, 6];
        for (let i in list) {
           console.log(i); // "0", "1", "2"，遍历的是列表的下标值，list在js中就是一个{index: value}这样实现的。当然他还有其他key，比如length，这里之所以没有打印"length"，是因为这个属性不可枚举
        }
        for (let i of list) {
           console.log(i); // "4", "5", "6", list的Symbol.iterator实现就是遍历数组中的值
        }
        ```
