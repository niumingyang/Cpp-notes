# object and class 对象与类

[toc]

## 初级特性
#### 类与对象
- 类是现实世界或思维世界中的实体在计算机中的反映，它将数据以及这些数据上的操作封装在一起。 
- 对象是具有类类型的变量。 
- 类是对象的抽象，而对象是类的具体实例。类是抽象的，不占用内存，而对象是具体的，占用存储空间。

#### 成员的访问控制
- `public`：访问不受限
- `private`：只有本类和友元可访问
- `protected`：只有本类、友元和子类可访问
- 另外，类定义构成一个作用于，在外部访问要通过对象名受限或类名受限访问

#### 创建对象
- 直接创建
```cpp
class A;

A a1(arg, ...);// 调用构造函数A(arg, ...)
A a2[num];// 创建对象数组
A a3[2] = {A(arg, ...), A(arg, ...)};// 创建对象数组并调用构造函数A(arg, ...)
```
- 间接创建
创建动态对象数组时只能调用默认构造函数
使用`malloc`不会调用构造函数
```cpp
class A;

A *p1, *p2;
p1 = new A(arg, ...);// 调用构造函数A(arg, ...)
delete p1;

p2 = new A[num];// 创建对象数组
delete []p2;
```

#### `this`指针
- 每个对象都有一个`this`指针，指向该对象本身；
- 可通过在每个成员函数的参数加上这个指针实现（类似于`Python`的`self`）

#### 构造函数
```cpp
class A{
public:
    A();
    A(arg, ...);
};
```
- 创建对象时自动调用
- 会自动生成默认构造函数
- 可自定义构造函数
- 自动根据创建对象时的参数选择适当的构造函数。
- 类D既有基类B、又有成员对象类M，则在创建D类对象时，构造函数的执行次序为：B--&gt;M--&gt;D；
- 可通过成员初始化表初始化数据成员，`const`数据成员和引用类型数据成员必须使用成员初始化表
```cpp
// 四类数据成员使用成员初始化表初始化的方式
class B{
public:
    B(int t);
};

class A{
private:
    const int x;
    int &y;
    int z;
    B b;
public:
    A(): x(0), y(z), b(0){
        z = 0;
    }
};
```

#### 析构函数
```cpp
class A{
public:
    ~A();
};
```
- 对象消亡时自动调用
- 会自动生成默认析构函数，仅负责调用成员对象类和基类的析构函数
- 可自定义析构函数，当该类（不包括成员对象类和基类）申请了资源，必须自定义
- 如类D既有基类B、又有成员对象类M，当D类的对象消亡时，析构函数的执行次序为：D--&gt;M--&gt;B；

#### 拷贝构造函数
```cpp
class A{
public:
    A(const A& a);
};
```
- 一种特殊的构造函数
- 调用的时机：
用一个`A`类对象初始化另一个`A`类对象
将对象传参给函数时
将对象作为返回值
- 会提供隐式拷贝构造函数（简单复制）
- 存在指针数据成员时要自定义拷贝构造函数
- 在自定义拷贝构造函数中，若存在成员对象，要在成员初始化表中显式调用成员对象的拷贝构造函数（否则会调用隐式拷贝构造函数）

#### 类作为模块
- 一个`C++`模块由一个`.h`文件和一个`.cpp`文件构成，前者存放类的定义，后者存放类外定义的成员函数

## 进一步讨论

#### 常成员函数
```cpp
class A{
public:
    void f() const{
        ...
    }
};
```
- 常成员函数无法修改成员的值
- 可以修改指针成员指向的值

#### 静态成员
- 静态数据成员
静态数据成员为该类的所有实例所共有，每类仅存在一个拷贝
```cpp
class A{
    static int shared;
};
int A::shared = 0;// 初始化

A a1;
A a2;// a1和a2共享shared变量
```
- 静态成员函数
```cpp
class A{
    static int shared;
public:
    static void set_shared(int i){
        shared = i;
    }
    static int get_shared(){
        return shared;
    }
};
int A::shared = 0;// 初始化

A a;
a.set_shared(10);
A::get_shared();// 两种访问方式
```
- 静态成员函数只能访问`static`变量
- 遵循类的访问控制
- 没有隐藏的`this`指针

#### 友元
```cpp
class A{
    friend void f();
    friend class B;
    friend C::f();
};
```
- 将函数或类设为类`A`的友元，可以访问类`A`的`private`和`protected`变量

#### 转移构造函数
- 当用一个即将消亡的对象去初始化另一个同类对象时，构造函数效率不高，要使用转移构造函数
```cpp
class A{
    int *p;
public:
    // 相当于转移资源的所有权而不是重新申请再归还
    A(A&& a){
        p = a.p;
        a.p = NULL;
    }
};
```
- `g++`编译会对返回值进行优化，要使得转移构造函数生效，要加上编译选项`-fno-elide-constructors`

#### 操作符重载
##### 重载方法
- 可以将重载函数定义成全局函数并设为类的友元
- 也可以在类内重载（会少一个参数）
##### 重载双目操作符(以`+`为例)
```cpp
class A{
public:
    A operator +(const A& a){
        ...
    }
};
```
```cpp
// 第二种重载方法
class A{
public:
    friend A operator +(const A& a, const A& b);
};

A operator +(const A& a, const A& b){
    ...
}
```
##### 重载单目操作符(以`++`为例)
```cpp
// 前置++
class A{
public:
    A& operator ++(){
        ...
        return *this;
    }
};
// 后置++，要加上int
class A{
public:
    const A operator ++(int){
        A tmp = *this;
        ...
        return tmp;
    }
};
```
##### 重载`=`操作符
- 自动生成隐式`=`操作符，存在指针成员时需重载
- 与拷贝构造函数适用范围不同，`=`操作符只在有`=`的地方生效
而用一个`A`类对象初始化另一个`A`类对象，将对象传参给函数时或将对象作为返回值时调用拷贝构造函数
```cpp
class A{
    char *p;
public:
    // 重载赋值操作符
    A& operator =(const A& a){
        p = new char[strlen(a.p)+1];
        strcpy(p, a.p);
        return *this;
    }
    // 重载转移赋值操作符
    A& operator =(const A& a){
        if (&a == this) return *this;
        delete []p;
        p = a.p;
        x.p = NULL;
        return *this;
    }
};
```
##### 重载`[]`操作符
```cpp
class A{
public:
    // B和C可以为任意类型
    B& operator [](C c){
        ...
    }
};
```
##### 重载`new`和`delete`操作符
- 只能作为静态成员重载（可以不写`static`）
```cpp
// 重载new和delete操作符以实现多次小内存的申请转化为一次大内存的申请再统一分配
class A{
    ...
public:
    void* operator new(size_t size);
    void operator delete(void *p);
private:
    static A *pfree;
    A *next;
};
A *A::p_free = NULL;

void* A::operator new(size_t size){
    A *p;
    if (p_free == NULL){
        p_free = (A *)malloc(size*NUM)；
        for (p = p_free; p != p_free+NUM-1; p++)
            p->next = p+1;;
        p->next = NULL;
    }

    p = p_free;
    p_free = p_free->next;
    memset(p, 0, size);
    return p;
}

void A::operator delete(void *p){
    ((A *)p)->next = p_free;
    p_free = (A *)p;
}
```
##### 重载`()`操作符
- 将类当做函数使用——函数对象
- 该对象除了有函数的作用外，还可以拥有状态，如`random`函数对象，拥有状态`seed`
- 匿名函数($\lambda$表达式)即如此实现
```cpp
class A{
public:
    // B和C可以为任意类型
    B& operator ()(C c){
        ...
    }
};
```
##### 重载`->`操作符
- 可以实现一种“智能指针”，用该“指针”去访问另一个对象的成员时，能做一些额外的事
```cpp
// A是一个智能指针类
class B;

class A{
    B *p;
public:
    A(B *b){
        p = b;
    }
    B& operator ->(){
        ...
        return p;
    }
};

B b;
A a(&b);// a是一个指向b的智能指针
a->f();// 访问的是b.f()
```

##### 重载类型转换操作符
- 带一个参数的构造函数可用作类型转换
加`explicit`关键字可禁止用于隐式类型转换
- 自定义类型转换
```cpp
class A{

public:
    A(int i){
        ...//类型转换int -> A
    }
    operator int(){
        ...//类型转换A => int
    }
};
```
- 歧义问题
```cpp
A a;
int i = 0;
a + i // 将a转为int还是将i转为a?

// 解决1：显式类型转换
(int)a + i
a + (A)i

// 解决2：explicit关键字禁止隐式转换
explicit A::A(int i){
    ...//显式类型转换int -> A
}
```
