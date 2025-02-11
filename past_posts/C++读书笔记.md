---
title: C++读书笔记
date: 2023-02-09 07:04:00
tags: [read-notes/读书笔记,C++]
categories: [阅读笔记,C++]
---
> Include 《A Tool of C++》、《C++ programming language》

<!--more-->

#### new

new（动态存储分配），C++的一种创建对象的方式

```c++
int a=new int{7};//一个int变量
int b=new int[10]//一个数量为10的int数组
```

####  引用
引用与指针类似，但语法规则有很多不同

```c++
int a1=2;
int a2=3
int & r1=a1; //T&: T的引用
int & r2=a2;
r1=r2;
```
在赋值语句`r1=r2`中，引用会提取r2引用的值，改变r1引用的值，而指针只会改变指针的地址，不会改变元素。


<!-- more -->

#### 用{}赋值
使用赋值=有时会出现类型转换导致信息丢失，例如：

```c++
int a=7.2 //a为7
```

而使用{}能发现这一错误

![image.png](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b0c4349711484e068ed399a04ec8172e~tplv-k3u1fbpfcp-watermark.image?)

函数的返回值也可以是一类对象{}：

```c++
struct Entry{
	string name;
	int value;
};

Entry read_entry(istream& is){
	string s;
	int i;
	is>>s>>i;
	return {s,i};
}

auto e = read_entry(cin);//auto:自动判断返回值

cout<<"{"<<e.name<<","<<e.value<<"}\n";
```

在这个例子中，auto自动判断返回值为{s,i};

#### 结构化绑定

续上条的代码：

```c++
auto [n,v] = read_entry(is);
cout<<"{"<<n<<","<<v<<"\n";
```
`n,v` 的类型是从函数`read_entry`中推出的，这种给类设定局部名字的方式称为结构化绑定，例：

```c++
map<string,int>m;
//...input m....
for(const auto [key,value]:m)
	cout<<"{"<<key<<","<<value<<"\n";
```

### 类

class建议先阅读private的变量名，在阅读public的构造函数（构造函数用{}赋值）

#### 具体类型

##### 简易复数类的实现

```c++
class complex{
	double re im;
public:
	complex(double r,double i):re{r},im{i}{}
	complex(double r):re{r},im{0}{}
	conplex():re{0},im{0}{}

	double real()const{return re;}
	void real(double d){re=d;}
	double imag()const{return im;}
	void imag(double d){im=d;}

	complex & operator+=(complex z){
		re+=z.re;
		im+=z.im;
		return *this;
	}

	complex & operator-=(complex z){
		re-=z.re;
		im-=z.im;
		return *this;
	}

	complex & operator*=(complex);//类外定义
	complex & operator/=(complex);//别处定义

	complex operator+(complex a,complex b){return a+=b;}
	complex operator-(complex a,complex b){return a-=b;}
	complex operator-(complex a){return {-a.real(),-a.imag()};}
	complex operator*(complex a,complex b){return a*=b;}
	complex operator/(complex a,complex b){return a/=b;}

	bool operator==(complex a,complex b){
		return a.real()==b.real()&&a.imag()==b.imag();
	}
	bool operator!=(complex a,complex b){
		return !(a==b);
	}

	complex sqrt(complex);//别处定义
}
```

拷贝相当废内存和时间，所以经常用返回值和`operator`来替代拷贝

##### 构造+析构 函数

以vector为例子：

```c++
class vector{
public:
	vector(int s):elem{new double[s]},sz{s}{//构造函数
		for(int i=0;i!=s;++i)    //初始化
  			elem[i]=0;
	}

	vector(){delete[] elem;}//析构函数，释放资源

	double&operator[](int i);
	int size()const;//类外定义
private:
	double* elem;
	int sz;
};
```

构造函数+析构函数可以让使用者无需设计释放函数，避免内存泄漏

##### 初始化方法

C++主要是两种：初始值列表构造函数、push_back()；

```c++
class Vector{
public:
	Vector(std::initializer_list<double>);//初始值列表狗制造函数
	//...
	void push_back(couble);//添加一个元素
	//...
};

//push_back可用于添加任意元素
	Vector read(istream& is){
		Vector v;
		for(double d ;is>>d;)//适用for而不是while，使得d的作用域限制在了循环体内
			v.push_back(d);
		return v;
	}

	Vector v=read(cin);//使用

	Vector::Vector(std::initializer_list<double>lst):elem{new double[lst.size()]},sz{static_cast<int>(lst.size())}
	{
		copy(lst.begin(),lst.end(),elem);
	}
```

#### 抽象类（继承、多态）

C++的**继承**通过包含纯虚函数的`抽象类`（基类/base）与所派生的`子类/subcalss`（或超类/superclass）

```c++
class Container{//只作为接口的抽象类
public:
	virtual double& operator[](int)=0;	//纯虚函数
	virtual int size() const=0;			//常量成员函数
	virtual ~Container(){}				//析构函数
}

//抽象类不能定义对象
Container c;//错误
Container* p=new Vector_container(10)//正确，Container是个接口

void use(Container& c){//忽略具体类型
	const int sz=c.size();
}
```
