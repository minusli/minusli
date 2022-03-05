# golang 内存逃逸

### 一、什么是内存逃逸
预期是栈上分配的临时变量，逃逸到了堆上

### 二、内存逃逸的危害
- 分配上的开销会加重
- 回收上会加重 GC 压力

### 三、有哪些情况会产生内存逃逸
1. channel 传入指针
```golang
package main
func main() {
    x := 1
    channel := make(chan *int, 10)
    px := &x
    channel <- px  // x 发生逃逸，因为 channel 可能在其他 goroutine 中被读取，在本栈结束后，不能被销毁，所以逃逸到堆上
}
```

2. slice/map 中使用指针
```golang
package main
func main() {
    x := 10
    list := make([]*int, 10)
    list = append(list, &x)  // x 发生逃逸，list 本身在栈上，但是里面的值需要在堆上；因为 list 可能在后续流程中交给其他栈使用，传入其中的指针 x，不能在本次栈结束后就销毁掉；所以需要逃逸到堆上
}
```

3. 栈空间容纳不下
```golang
package main
func main() {
    list := make([]int, 10000, 10000)  // 超过栈的大小，逃逸到堆上
}

func main() {
    size := 10
    list := make([]int, size)  // size 是变量，不确定 list 是否超过栈大小，所以直接逃逸到堆上
}
```

4. 局部变量闭包问题
```golang
package main
func main() {
    x := 10
    go func() {
        x = 5   // x 逃逸到堆上，闭包
    }()
}
```

5. 局部变量返回引用
```golang
package main
func main() {
    go func() *int {
        x = 5   
        return &x  // x 逃逸到堆上，因为返回了局部变量引用
    }()
}
```

6. interface 传参
```golang
package main
func main() {
    fmt.Println(123)  // 123 逃逸到堆上，因为print传参是 interface 类型，实际上有个 123 转 interface 的过程，interface 中的 data 就指向的 123；然后在 println 内部打印；那么 println 的栈需要访问 123，而且存的是 123 指针，所以 123 需要逃逸到堆上，这样才能访问的到；
}
```

### 四、总结逃逸场景：
1. 栈空间不够，局部变量直接在堆上分配内存
2. 局部变量的指针，可能泄露到本栈外使用
   - channel 传入局部变量指针
   - slice/map 传入局部变量指针
   - 函数返回局部变量指针
   - 函数有 interface 变量
   - 局部变量闭包问题

### 扩展：
1. interface 背后的原理
2. 闭包背后的原理