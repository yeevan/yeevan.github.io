## Go语言结构体
结构体是由一系列具有相同类型或不同类型的数据构成的数据集合。

### 定义：
	
	结构体定义需要使用type和struct语句。
	struct语句定义一个新的数据类型，
	结构体中有一个或多个成员。
	type定义结构体的名称
	type Books struct {
		title string
		id int
	}
	
### 使用:
	定义一个变量属于这个类型，再使用.的方式访问成员
	
	1.变量的声明
	a := Books{"Go 语言" ,"www.runoob.com", "Go 语言教程", 6495407}	
	fmt.Println(a.title) //打印出Go 语言
	fmt.Println(a)   //会打印出整个大括号里的内容
	
	2.当做函数参数
	func printBook(book Books) {
		...
	}
	
### 结构体指针：
格式：
	
	var struct_pointer *Books
	如：var a *Books
	a = &Book1
	访问成员 a.title
	
	
## 语言切片
语言切片是对数组的抽象，Go的数组长度不可变，和数组相比，切片的长度是不固定的，可以追加元素

### 定义
	
	var 变量名 []type  	