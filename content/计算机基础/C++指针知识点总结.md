---
slug: computer-basics/cppstar
title: C++指针知识点
---
# C++ 指针知识点总结

## 1. 指针基础

指针是一个变量，存储的是**内存地址**。

```cpp
int x = 10;
int* p = &x;   // p 存放 x 的地址，& 取地址符
cout << *p;    // 解引用，输出 10
```

| 符号 | 含义 |
|---|---|
| `&x` | 取变量 x 的地址 |
| `*p` | 解引用：访问 p 指向的值 |
| `int* p` | 声明一个指向 int 的指针 |

## 2. 指针的大小

- 指针本身大小只与平台有关：32 位系统 4 字节，64 位系统 8 字节
- 与指向的类型无关（`char*` 和 `double*` 大小相同）

## 3. 空指针与野指针

```cpp
int* p1 = nullptr;   // C++11 推荐写法（代替 NULL）
int* p2 = NULL;      // C 风格，本质是 0
```

**野指针**：指向已释放或未初始化内存的指针，使用是未定义行为。

```cpp
int* p;        // 未初始化，危险！
delete p;      // p 成为悬空/野指针，应置空：
p = nullptr;
```

**规则**：定义时初始化；delete 后立即置 `nullptr`；解引用前判空。

## 4. 指针与 const

```cpp
const int* p;        // 指向常量的指针：不能通过 p 改值，可以改指向
int* const p;        // 常量指针：指向不能改，值可以改
const int* const p;  // 都不能改
```

口诀：`const` 在 `*` 左边修饰"值"，在 `*` 右边修饰"指针本身"。

## 5. 指针与数组

数组名在多数表达式中退化为首元素指针。

```cpp
int arr[5] = {1,2,3,4,5};
int* p = arr;         // 等价于 &arr[0]
*(p + 2);             // 等于 arr[2]，指针运算按类型大小偏移
p++;                  // 移动到下一个元素
```

- `arr[i]` 本质就是 `*(arr + i)`
- 注意：`sizeof(arr)` 是整个数组的字节数，而 `sizeof(p)` 只是指针大小
- 二维数组传参要指定列数：`void f(int (*p)[5])`

## 6. 指针与函数

### 6.1 指针作参数（可实现"传引用"效果）

```cpp
void swap(int* a, int* b) {
    int t = *a; *a = *b; *b = t;
}
swap(&x, &y);
```

### 6.2 返回指针

```cpp
int* create() {
    return new int(42);   // 合法：返回堆内存地址
}
// 不要返回局部变量的地址（函数结束即失效）
```

### 6.3 函数指针

```cpp
int add(int a, int b) { return a + b; }
int (*fp)(int, int) = add;
int r = fp(1, 2);       // 调用
```

常用于回调函数：

```cpp
void process(int x, int (*callback)(int));
```

## 7. 动态内存管理

```cpp
int* p = new int(10);      // 单个对象
delete p;

int* arr = new int[100];   // 数组
delete[] arr;              // 必须用 delete[]
```

常见错误：
- **内存泄漏**：new 后忘记 delete
- **重复释放**：同一块内存 delete 两次
- **delete/new 不匹配**：`new[]` 配对 `delete[]`
- **访问已释放内存**

## 8. 智能指针（C++11，推荐）

头文件 `<memory>`，自动管理生命周期，避免手动 delete。

```cpp
#include <memory>

std::unique_ptr<int> u = std::make_unique<int>(42);
// 独占所有权，不可复制，只能移动

std::shared_ptr<int> s1 = std::make_shared<int>(42);
std::shared_ptr<int> s2 = s1;   // 引用计数 +1，归零时自动释放

std::weak_ptr<int> w = s1;      // 弱引用：不增加计数，解决循环引用
```

| 类型 | 所有权 | 适用场景 |
|---|---|---|
| `unique_ptr` | 独占 | 默认首选 |
| `shared_ptr` | 共享（引用计数） | 多处共享同一对象 |
| `weak_ptr` | 观察 | 打破循环引用 |

## 9. 特殊指针

### this 指针

成员函数内部隐含的指针，指向当前对象。

```cpp
class A {
    int x;
public:
    A& set(int x) { this->x = x; return *this; }
};
```

### void 指针

可指向任意类型，但解引用前必须转换。

```cpp
void* vp = &x;
int* ip = static_cast<int*>(vp);
```

### 指向指针的指针（二级指针）

```cpp
int x = 5;
int* p = &x;
int** pp = &p;
**pp;   // 值为 5
```

常用于：函数内修改指针本身、动态二维数组。

## 10. 指针 vs 引用

| 对比项 | 指针 | 引用 |
|---|---|---|
| 可否为空 | 可以 | 不可以 |
| 可否重新绑定 | 可以 | 不可以 |
| 是否需要解引用 | 需要 | 直接用 |
| 可否做算术运算 | 可以 | 不可以 |
| 占用内存 | 占（存地址） | 概念上不占 |

优先使用引用；需要"可空、可改指向、遍历内存"时才用指针。

## 11. 常见陷阱清单

1. 解引用未初始化/空指针 → 崩溃
2. 使用悬空指针（指向已释放内存）
3. 内存泄漏 / 忘记释放
4. `new[]` 与 `delete[]` 不配对
5. 数组越界访问
6. 返回局部变量地址
7. 多态场景忘记虚析构函数（`virtual ~Base()`），导致 `delete basePtr` 时派生类未正确析构
