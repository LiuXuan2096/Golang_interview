# Go语言基础二

## Go语言中哪些类型可以使用len？那些类型可以使用cap？

* cap: 数组，切片，通道
* len：数组，切片，映射，通道，字符串
* 数组的长度和容量固定且相等，在编译器确定
* 切片的容量长度和容量不能直接修改，可通过反射修改
* 字符串的长度是字符串的字节数，可用rune统计字符个数。

## Go语言中哪些类型的值可以用组合字面量表示？

### 类型值

* 一个类型的实例称为此类型的一个值
* 一个类型可以有很多不同的值，其中一个为它的零值
* 每个类型都会有自己的零值，零值可以看做是这个类型的默认值。

### 类型值的呈现方式

#### 字面量

* 一个值的字面形式称为一个字面量
* 整型值的字面量形式：十进制、八进制、十六进制、和二进制形式
* 浮点数：1.11,1.11e2， 100.e+2
* 虚部字面量形式：1.12i，1.12E2i， 0x16i（0+22i）
* rune的字面量：‘a’， ‘\141', '\x61', '\u0061', '\U00000061'
* 字符串的字面量：“go”， “Helloween”
* 函数字面量：func(x int, y string, z string) (s string, e error)

#### 组合字面量

* 用来表示结构体类型值和容器类型（数组、切片、映射）值
* 表示形式为T{...}

```go
// 数组的组合字面量表示
[4]int{1,2,3,4}
[4]int{1:1, 2:2,3:3}
[...]int{1,2,3,4}

// 切片的组合字面量表示
[]int{1,2,3,4}

// 映射的组合字面量表示
map[string]int{"k1":1, "k2":2}
```

* 由于切片和映射的零值是nil，所以切片和映射的零值是不能用组合字面量形式表示的。

#### 具名常量

```go
// 具名常量 
const π = 3.1416
const Pi = π
```

#### 表达式

```go
ch := make(chan int, 10)
a += 5
a > 0
f // 一个类型为func()的表达式
f() // 函数调用
```

## Go语言的指针有哪些限制

### Go语言的指针？

* 一个指针变量本身存储的只是一个内存地址
* 一个内存地址在32位系统上占4个字节，在64位系统上占8个字节
* 内存地址一般用整数的16进制表示，比如:0xc000012450
* 当一个变量被声明的时候，Go运行时将为此变量开辟一段内存此内存的起始地址即为此变量的地址。

### 理解指针

```go
x := new(int) // 声明指针x
y := x  // 将x的值赋值给y
tmp := 5
x = &tmp  // 将tmp变量的地址值赋值给x
fmt.Println("x=", x, *x, "y=", y, *y)
```

### 多维指针

```go
var ptr *int  // 一维指针
var pptr **int  // 二维指针
var ppptr ***int // 三维指针
```

* 一般不建议刻意使用多维指针，

### Go语言中哪些值不可被寻址

* 映射的元素
* 字符串的字节元素
* 常量包级别的函数以及用作函数值的方法等

### 如何获取一个指针值

* 内置函数new
* 直接使用&操作符

### 指针有什么用？

* 提供了一种间接的途径来访问和修改一些值

### Go指针有哪些限制

* Go指针不支持直接进行算术运算
* 一个指针类型的值不能被随意转换为另一个指针类型
* 一个指针值不能随意更其他类型的值进行比较
* 一个指针值不能随意被赋值给其他任意类型的指针值
* 上述限制可以通过unsafe标准库包中的非类型安全指针（unsafe.Pointer)来打破（不推荐）

### 指针比较需要满足的条件（任意一个）

* 这两个指针类型相同
* 这两个指针之间可以隐式转换

### 指针类型转换的条件（满足一个即可转换）

* 底层类型一致且有一个是无名类型
* 都是无名类型，且它们的基类型的底层类型一致

### 以下类型不属于无名类型

* 预声明类型
* 自定义类型（自定义泛型除外）
* 泛型的实例化类型
* 用在自定义泛型中的类型参数类型

## Go语言中哪些类型的零值可以用nil来表示？

### Go语言中的nil

* Go语言的nil并不是一个关键字
* 不同数据类型的nil值的尺寸是不同的
* nil值不一定是可以相互比较的，主要取决于该类型是否可以比较
* 可以比较的两个nil值也不一定就相等。

## Go语言中如何实现任意值转换？

* 通过switch value.(type)对interface的具体类型进行断言
* 在数值转化的过程中需要考虑溢出问题

## float或切片可以作为map类型的key么？

### 映射的key

* 在Go语言中，只有可比较的类型才能作为map的key
* 浮点型可比较，所以浮点型可以作为map的key。 但是浮点型作为map的key时，会因为精度问题出现一些 不符合预期的结果。所以不推荐使用浮点型作为map的key。

### Go语言中，哪些类型是不可比较的？

* slice
* map
* function
* 含有不可比较类型的数组
* 含有不可比较类型的结构体

## Go语言中怎么使用变长参数函数？

### 变长参数的使用需要注意的问题

* 函数只能有一个变长参数，且只能是参数列表中最后一个形参。
* 变长参数在函数体内是切片类型，也就是说...T等同于[]T
* 形参的类型和实参必须一致，包括接口类型

### 关于函数的一些细节

* unsafe标准库包中的函数和一些内置函数在编译时刻被估值
* 定义了函数的返回值就必须在函数中返回，但也存在一些特例

```go
func f3() int {
    a:
    	goto a
}

func f4() bool {
    for {}
}
```

* 函数的零值为nil，当我们调用一个nil函数将会产生panic

## 调用函数传入结构体时，是传值还是传指针？

### Go语言函数传值

* 函数的参数传递只有值传递，且传递的实参都是原始数据的一份拷贝。
* 切片，映射，通道和函数包含底层间接值部，在Go语言中赋值操作和函数调用传参都是将原始值的直接部分复制给了目标值。
* 如果原始值含有间接部分，则在赋值后，目标值和原始值的直接部分将引用着相同的间接部分。也就是说，两个值共享了底层的间接值部。

### 字符串的底层结构

```go
type _string struct {
    elements *byte  // 引用着底层的byte元素
    len int  // 字符串的长度
}
```

### 结构体的值传递

* 当结构体的字段中包含间接值部的类型时，实参和原值字段的间接值部共享同一个内存地址。
* 在函数内对整个结构体赋值时，实参的间接值部指向会改变。
* 结构体赋值过程中也会先复制结构体以及结构体字段值的直接值部。

## interface可以比较么？

### go语言中哪些类型可以比较？

* 除了slice，map，function这几种类型外
* 元素类型为可比较类型的数组，字段为可比较类型的切片
* 映射，切片，函数可以与nil比较。

### 接口的运行时结构

```go
// $GOROOT/src/runtime/runtime2.go
type eface struct {
    _type *_type
    data unsafe.Pointer
}

type iface struct {
    tab *itab
    data unsafe.Pointer
}
```

* 判断两个接口类型变量是否相同，只需要判断_type/tab是否相同，已经data指针指向的内存空间所存储的数据值是否相同就可以了。
* 对于空接口类型变量，只有_type和data所指数据内容一致的情况下，两个空接口类型变量才相等。
* Go在创建eface时一般会为data重新分配新内存空间，将动态类型变量的值复制到这块内存空间，并将data指针指向这块内存空间。

## 如何使一个结构体不能被比较？

### 不可比较类型的用途

* 避免作为map的key

### 不可比较的结构体

* 给结构体一个非导出的零尺寸的不可比较类型的字段

```go
type Person1 struct {
	Name string
	age  int
}

type Person2 struct {
	Name string
}

type Person3 struct {
	uncompare [0]func()
	Name      string
}
type Person4 struct {
	Name string
	age  int
}

func main() {
	p1 := Person1{Name: "test"}
	p2 := Person2{Name: "test"}
	p3 := Person3{Name: "test"}
	fmt.Println("p1.size:", unsafe.Sizeof(p1), "p2.size:", unsafe.Sizeof(p2), "p3.size:", unsafe.Sizeof(p3)) //p1.size: 24 p2.size: 16 p3.size: 16

	//m := map[Person2]int{}
	//m1 := map[Person3]int{} //编译错误

	//set1 := map[int]struct-demo{}{1: {}}
	//set2 := map[int]bool{1: false}
	//fmt.Println("set1.size:", unsafe.Sizeof(set1[1]), "set2.size:", unsafe.Sizeof(set2[1])) //set1.size: 0 set2.size: 1

	//fmt.Println("p1==p2?", p1 == p2)

	//p4 := Person4{Name: "test"}
	//fmt.Println("p1==p4?", p1 == p4)
	p5 := struct {
		Name string
		age  int
	}{}

	p6 := struct {
		Name string
		age  int
	}{}
	p5 = p6
	fmt.Println("p5==p6?", p5 == p6)
}
```

### 结构体比较的前提

* 他们的字段都只能包含可比较类型
* 他们都是匿名结构体
* 这两个结构体需要能隐式转换（字段类型及声明顺序相同）

## DeepEqual()与bytes.Equal()比较切片时有何区别？

* DeepEqual不能比较值为nil的两个byte切片
* 值为nil的两个byte切片用go语言reflec标准库中的DeepEqual()函数比较时，会发现其返回结果为false。

```go
func main() {
	var s1 []struct{}
	s2 := []struct{}{}
	s3 := make([]struct{}, 0)
	s4 := new([]struct{})
	fmt.Println(s1 == nil, s2 == nil, s3 == nil, s4 == nil)
	s5 := make([]struct{}, 2)
	s6 := make([]struct{}, 0, 2)
	fmt.Println(len(s1), len(s5), len(s6))

	return
	var b1 []byte
	b2 := []byte{}
	//fmt.Println(reflect.DeepEqual(b1, b2)) //false
	//
	//v1 := reflect.ValueOf(b1)
	//v2 := reflect.ValueOf(b2)
	//fmt.Println(v1.IsNil(), v2.IsNil())
	//
	fmt.Println(bytes.Equal(b1, b2)) //true
	//
	var b3 []byte
	var b4 []byte
	fmt.Println(reflect.DeepEqual(b3, b4))
	fmt.Println(bytes.Equal(b3, b4)) //true
	//
	b5 := []byte{}                         //true
	b6 := []byte{}                         //true
	fmt.Println(reflect.DeepEqual(b5, b6)) //true
	fmt.Println(bytes.Equal(b5, b6))       //true
}
```

## 空struct{}有什么用？

### 空结构体的特点

* 空结构体是不占空间的，在实际工作中通常使用空结构体和map配合实现集合。

### map作为集合有两种用法

* 完全将这个空结构体当做map来用
* 将map做一些封装

## 如何判断两个对象是否完全相同

### DeepEqual()

```go
func DeepEqual(x, y any) bool {
    if x == nil || y == nil {
        return x == y
    }
    v1 := ValueOf(x)
    v2 := ValueOf(y)
    if v1.Type() != v2.Type() {
        return false
    }
    return deepValueEqual(v1, v2, make(map[visit]bool))
}
```

### ==号比较与DeepEqual()函数之间的区别

* 如果表达式x和y的类型不相同，则函数调用DeepEqual(x,y)的结果总为false，但x==y的结果有可能为true。

```go
type person struct {
    name string
}
p1 := person{"test"}
p2 := struct {
    name string
}{"test"}
fmt.Println(p1 == p2)  // true
fmt.Println(reflect.DeepEqual(p1, p2))  // false
```

* 如果同类型的x和y是两个引用着不同的其他值的指针值，则x==y的估值结果总为false，但函数调用DeepEqual(x,y)的可能结果为true。

```go
x := new(int)
y := new(int)
fmt.Println("x==y", x==y)  // false
fmt.println("DeepEqual(x,y):", reflect.DeepEqual(x,y))  // true
```

* 当x和y均处于某个循环引用链中时，为了防止死循环，DeepEqual调用的结果可能为true。

```go
type link struct {
    data interface{}
    next *link
}
var a,b,c link
a.next = &b
b.next = &c
c.next = &a
fmt.Println(a == b)  // false
fmt.Println(reflect.DeepEqual(a,b))  // true
```

## 使用两种方式判断一个对象是否拥有某个方法

* 直接通过类型转换的结果来判断
* 借助标准库中的reflect包来实现

```go
type person struct {
    Name string
}
func (p person) Say(str string) string {
    return p.Name + str
}
func main() {
    // 方法1： 通过类型转换判断
    p := &person{}
    fmt.Println(HasMethodSay(p))  // true
    
    // 方法2： 通过反射判断
    fmt.Println(HasMethod(p, "Say"))  // true
}

func HasMethodSay(v interface{}) (has bool) {
    _, has = v.(interface{Say(str string) string})
    return
}

func HasMethod(obj interface{}, methodName string) bool {
    if methodName == "" {
        return false
    }
    object := reflect.ValueOf(obj)
    // 获取到方法
    method := object.MethodByName(methodName)
    if !method.IsValid {
        return false
    }
    return true
}
```

## for range 避坑秘笈

### for range误区

* for range循环的时候会创建每个元素的副本，而不是元素的引用。
* 每次遍历的元素都指向同一个地址（基于性能考虑）
* 直接对遍历的元素取地址，最终只会拿到最后一个元素的地址。

### 既然for range会对元素进行拷贝，那遍历大内存的元素时，如何最大限度的节省内存，提高运行效率呢？

* 直接使用元素的index遍历，避免值的拷贝。
* 对于一个大数组，我们也可以直接使用数组指针来遍历。

