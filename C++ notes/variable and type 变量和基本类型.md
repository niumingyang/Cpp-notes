# variable and type 变量和基本类型

[toc]

## 引用和指针

#### 引用
- 引用即别名
```cpp
int x = 0;
int &y = x; //正确，y是x的别名
int &z; //错误，引用必须初始化
int &w = 0; //错误，引用必须使用对象初始化
```

#### 空指针`nullptr`
尽量使用`nullptr`，同时少用`NULL`

## 类型别名
给某个类型起一个别名
#### `typedef`关键字
```cpp
typedef long long ll;
```

#### `using`关键字
```cpp
using ll = long long;
```

## `const`限定符

#### `const`限定符
加了`const`修饰的变量无法被修改
```cpp
const int i = 0; //正确，编译时初始化
const int j = f(); //正确，运行时初始化
const int k; //错误，必须初始化
```

#### `const`与引用
- `const`引用不能修改绑定的对象
- `const`引用可能引用一个非`const`对象
```cpp
int val = 0;
const int &p1 = val;
int &p2 = val;
p1++; // 错误，无法修改
p2++; // 正确，j不是const引用
```

#### `const`与指针
- *指向常量的指针*不能用于修改指向对象的值
- *常量指针*必须初始化，且不能修改指针指向的地址
- *指向常量的常量指针*兼具两者特点
```cpp
int val = 0;
int val2 = 1;

/* 三种const指针 */

const int *p1 = &val;
*p1 = 1; // 错误，无法修改对象的值
p1 = *val2; // 正确，可以修改指针指向的地址

int *const p2 = &val;
*p2 = 1; // 正确，可以修改对象的值
p2 = *val2; // 错误，无法修改指针指向的地址

const int *const p3 = &val;
*p3 = 1; // 错误，无法修改对象的值
p3 = *val2; // 错误，无法修改指针指向的地址
```

#### 顶层`const`
- 顶层`const`：指针本身是常量，或任意数据类型是常量
- 底层`const`：指针和引用等符合类型的基本类型部分是常量
- 执行对象的拷贝操作时，拷入拷出对象要具有相同的底层const资格，或非const转换为const
```cpp
int val = 0;
int *const p1 = &val; // 顶层const
const int val2 = 0; // 顶层const
const int *p2 = &val; // 底层const
```

## `constexpr`和常量表达式

#### 常量表达式
- 常量表达式指值不会改变，且在编译期就能得到计算结果的表达式，包括字面值和用常量表达式初始化的`const`对象
```cpp
int a = 0; // 不是
const int x = 0; // 是
const int y = x+1; // 是
const int z = get_num(); // 不是
```

#### `constexpr`变量
- 可将变量声明为`constexpr`变量以便于编译器检查变量的值是否是一个常量表达式
```cpp
constexpr int x = 0; // 是常量表达式
constexpr int y = x+1; // 是常量表达式
constexpr int z = get_num(); // 只有当get_num()是constexpr函数时才是常量表达式
```

#### 指针和`constexpr`
若在`constexpr`声明中定义了一个指针，仅对指针有效，与所指对象无关
```cpp
const int *p = nullptr; // p是一个指向常量的指针
constexpr int *q = nullptr; // q是一个常量指针
```

## 自动类型推导

#### `auto`类型说明符
- 编译器自动根据初始值推导变量的类型
```cpp
auto res = x + y; // res类型为int
auto i = 0, *p = &i; // 在一条语句中声明多个变量，其基本类型需相同
```
- 编译器将引用对象的类型推导为原类型
```cpp
int i = 0, &r = i;
auto a = r; // a类型为int
```
- `auto`会忽略顶层`const`（可手动添加为`const auto`），保留底层`const`

#### `decltype`类型指示符
- 选择并返回操作数的数据类型
```cpp
decltype(f()) sum = x; // sum类型为f()的返回值类型
```
- 不同于`auto`，`decltype`类型指示符继承 *引用* 以及 *所有`const`* 
- 如果`decltype`的参数是一个表达式，返回表达式结果的类型
- `decltype`参数为解引用时，返回引用类型
```cpp
int i = 0, *r = &i;
decltype(*r) a = r; // a的类型为int&
```
- `decltype((variable))`结果为引用类型，而`decltype(variable)`结果只有当`variable`的类型是引用时才是引用