# IO 输入输出

[toc]

## 面向控制台

#### 基于函数库
```cpp
// output
int putchar(int ch);
int puts(const char *p);
int printf(const char *format [, <参数表>]);

// input
int getchar();
char *gets(char *p);
int scanf(const char *format [, <参数表>]);
```

#### 基于类库
- 4个预定义I/O对象：`cin` `cout` `cerr` `clog`
- 使用操作符`<<` `>>` 配合*操纵符*（如`endl`、`setprecision`等）进行输入输出，可以重载

#### 重载`<<`和`>>`操作符
```cpp
// 对类A重载 <<
class A{
public:
    friend ostream& operater <<(ostream& out, const A &a);
};
ostream& operater <<(ostream& os, const A &a){
    os << ...;
    return os;
}
```

## 面向文件
- 数据存储有两种方式：
*文本方式*：存储有行结构的文字数据
*二进制方式*：无显式结构的数据

#### 基于函数库 
```cpp
#include <cstdio>

// open file
FILE *fopen(const char *filename, const char *mode);

// output
int fputc(int c, FILE *stream);
int fputs(const char *string, FILE *stream);
int fprintf(FILE *stream, const char *format [, argument]...);
// 按字节输出
size_t fwrite(const void *buffer, size_t size, size_t count, FILE *stream);

// input
int fgetc(FILE *stream);
char *fgets(char *string, int n; FILE *stream);
int fscanf(FILE *stream, const char *format [, argument]...);
// 按字节读入
size_t fread(const void *buffer, size_t size, size_t count, FILE *stream);
// 判断文件结束
int feof(FILE *stream);

// close file
int fclose(FILE *stream);
```

#### 基于类库
```cpp
#include <iostream>
#include <fstream>

// output
ofstream out(<文件名>, <打开方式>);// 打开方式为ios::out / ios::app
if (!out){
    ...// fail
}
out << ...;
out.wirte(...);// 二进制方式输出
out.close();

// input
ifstream in(<文件名>, <打开方式>);// 打开方式为ios::in / ios::in|ios::binary
if (!in){
    ...// fail
}
in >> ...;
int ios::eof();// 判断文件结束
in.read(...);// 二进制方式读入
in.close();
```

## 面向字符串
- 可实现字符串和数字的相互转换
#### 基于函数库
```cpp
int sprintf(char *buffer, const char *format [, argument]...);
int sscanf(const char *buffer, const char *format [, argument]...);
```

#### 基于类库
```cpp
#include <iostream>
#include <sstream>

sstream ss;
sstream ss(s);// 创建一个保存s的拷贝的sstream
ss.str();// 返回保存的字符串拷贝
ss.str(s);// 将string s拷贝到ss中
```