---
slug: programming/stl
title: STL
---

## 一、**STL标准库**
**STL （Standard Template Library）** 是C++标准库的一部分，它提供了许多有用的容器、算法和迭代器。STL的设计目标是提供高效、通用、可复用的数据结构和算法，以及一致的接口和编程风格，使得C++程序员能够更加方便地编写高质量的代码。

STL主要包含以下三个组件：
* **容器（Containers）**：容器是用于存储和组织数据的类模板，提供了多种数据结构，如向量、列表、映射、集合等。容器可以存储任意类型的数据，并提供了丰富的操作接口，如插入、删除、访问等。STL的容器类模板具有高效的内存管理和数据操作，适用于各种不同的应用场景。
* **迭代器（Iterators）**：迭代器是一种泛型指针，用于遍历容器中的元素。STL的迭代器提供了统一的访问容器元素的接口，使得算法和容器之间的解耦成为可能。通过使用迭代器，可以实现容器的遍历、查找、排序等操作，同时提高了代码的可读性和可维护性。
* **算法（Algorithms）**：算法是用于对数据进行操作和处理的函数模板，STL提供了大量的常用算法，如排序、搜索、计算等。这些算法具有通用性，可以应用于不同类型的容器和数据，同时具有高效的实现和优化。通过使用STL的算法，可以简化代码、提高开发效率，并且减少错误的可能性。

### **1.容器** 

### 1.1 string（字符串）
`string` 是 C++ 标准库提供的字符串类，用于处理动态大小的字符串。它提供了丰富的成员函数来处理字符串的操作，如连接、比较、查找等。

| 函数名称              | 作用         |
| ----------------- | ---------- |
| size() 或 length() | 返回字符串的长度   |
| empty()           | 检查字符串是否为空  |
| append()          | 在字符串末尾追加内容 |
| substr()          | 提取子字符串     |
| find()            | 查找子字符串的位置  |
| replace()         | 替换子字符串     |
| c_str()           | 返回 C 风格字符串 |

#### 1.2 vector（动态数组）

vector与数组array类似，其使用更加灵活的动态空间来进行配置。在空间不足的时候，vector可以自动扩展空间容纳新元素，做到按需供给。在扩充空间的过程中仍然需要的是，重新分配空间、移动数据、释放原空间。  

 **at( )（会报错）**
```C++
	// 构造
	std::vector<int> vec;
	// 修改元素
	vec.at(1) = 20;
```

  **operator[]（不会报错）**
  ```c++
	  //修改
	  vec[3]=4;
```

  **push_back( )**
```C++
	// 添加元素到末尾
	vec.push_back(6);
```

 **pop_back( )**
```C++
	// 添加元素到末尾
	vec.pop_back();
```

  **insert()**
 ```C++
	 // 插入到相应的位置上
	 vec.insert(vec.begin()+2,3);
```

  **erase**
 ```C++
	 // 插入到相应的位置上
	 vec.erase(vec.begin()+2);
```

ps：不要用`vector<bool>`，由于c++神秘优化，不要使用


#### 1.3 stack（栈）

stack 是一个容器适配器，它基于其他容器（如 deque 或 vector）实现了栈的功能，栈遵循后进先出（LIFO）原则。

```C++
	//构造stack
	std::stack<int> s;
	//将元素压入栈中。
	s.push(10);
```

 **pop( )**
```C++
	//弹出栈顶元素
	s.pop();
```

  **top( )**
```C++
	//访问栈顶元素
	int topElement = s.top();
```

 **empty( )**
```C++
	//检查栈是否为空
	bool isEmpty = s.empty();
```

 **size( )**
```C++
	//返回栈中的元素个数
	size_t stackSize = s.size();
```

### 1.4 queue（队列）

queue 是一个容器适配器，它基于其他容器（如 deque）实现了队列的功能，队列遵循先进先出（FIFO）原则。常用函数如下：

 **push( )**
```C++
	//构造
	std::queue<int> q;
	//将元素添加到队列尾部
	q.push(10);
```

 **pop( )**
```C++
	//从队列头部移除元素
	q.pop();
```

 **front( )**
```C++
	//访问队列头部元素。
	int frontElement = q.front();
```

 **back( )**
```C++
	//访问队列尾部元素。
	int backElement = q.back();
```

 **empty( )**
```C++
	//检查队列是否为空
	bool isEmpty = q.empty();
```

 **size( )**
```C++
	//返回队列中的元素个数
	size_t queueSize = q.size();
```

#### 1.4.2 priority_queue

- 优先队列是一种容器适配器，根据严格的弱排序标准，它的第一个元素总是它所包含的元素中最大的。
- 此上下文类似于堆，在堆中可以随时插入元素，并且只能检索最大堆元素(优先队列中位于顶部的元素)。
- 优先队列被实现为容器适配器，容器适配器即将特定容器类封装作为其底层容器类，queue提供一组特定的成员函数来访问其元素。元素从特定容器的“尾部”弹出，其称为优先队列的顶部。
- 底层容器可以是任何标准容器类模板，也可以是其他特定设计的容器类。容器应该可以通过随机访问迭代器访问，并支持以下操作：
	empty()：检测容器是否为空
	size()：返回容器中有效元素个数
	front()：返回容器中第一个元素的引用
	push_back()：在容器尾部插入元素
- 标准容器类vector和deque满足这些需求。默认情况下，如果没有为特定的priority_queue类实例化指定容器类，则使用vector。
- 需要支持随机访问迭代器，以便始终在内部保持堆结构。容器适配器通过在需要时自动调用算法函数make_heap、push_heap和pop_heap来自动完成此操作。

```C++
int main()
{
	priority_queue<int> q;
	q.push(1);
	q.push(0);
	q.push(5);
	q.push(2);
	q.push(1);
	q.push(7);
	while (!q.empty())
	{
		cout << q.top() << " ";
		q.pop();
	}
	cout << endl;

	return 0;
}
```



### 1.5 map（映射）

映射类似于函数的对应关系，每个``x``对应一个``y``，而`map`是每个键对应一个值。这和python的字典类型非常相似。

容器中的每个存储对为一个`键值对`，包含两个元素（键和值）。

| 代码                      | 含义                                                       | 复杂度               |
| ----------------------- | -------------------------------------------------------- | ----------------- |
| `mp.find(key)`          | 返回键为`key`的映射的迭代器。当数据存在时，返回数据所在位置的迭代器；数据不存在时，返回`mp.end()` | `O(logN)`         |
| `mp.end()`              | 返回指向map尾部的迭代器（最后一个元素的下一个地址）                              | `O(1)`            |
| `mp.erase(it)`          | 删除迭代器对应的键和值                                              | `O(logN)`         |
| `mp.erase(key)`         | 根据映射的键删除键和值                                              | `O(logN)`         |
| `mp.erase(first, last)` | 删除左闭右开区间迭代器对应的键和值                                        | `O(last - first)` |
| `mp.size()`             | 返回映射的对数                                                  | `O(1)`            |
| `mp.clear()`            | 清空map中的所有元素                                              | `O(N)`            |
| `mp.insert()`           | 插入元素，插入时要构造键值对                                           | `O(N)`            |
| `mp.empty()`            | 如果map为空，返回`true`，否则返回`false`                             | `O(1)`            |
| `mp.begin()`            | 返回指向map第一个元素的迭代器（地址）                                     | `O(1)`            |
| `mp.end()`              | 返回指向map尾部的迭代器（最后一个元素的下一个地址）                              | `O(1)`            |
| `mp.rbegin()`           | 返回指向map最后一个元素的迭代器（地址）                                    | `O(1)`            |
| `mp.rend()`             | 返回指向map第一个元素前面（上一个）的逆向迭代器（地址）                            | `O(1)`            |
| `mp.count(key)`         | 查看元素是否存在，因为map中键是唯一的，所以存在返回1，不存在返回0                      | `O(logN)`         |
| `mp.lower_bound()`      | 返回一个迭代器，指向键值>= `key`的第一个元素                               |                   |
| `mp.upper_bound()`      | 返回一个迭代器，指向键值> `key`的第一个元素                                |                   |

### 1.6 set（集合）

**set容器特点:**

- 所有元素插入时候会被自动排序
- set容器不允许插入重复值
- 出入数据 只能用insert

**set容器本质:**  
　　set/multiset属于**关联式容器**，底层结构是用**二叉树**实现

| 方法                | 功能描述                          |
| ----------------- | ----------------------------- |
| `insert(elem)`    | 在容器中插入元素，常用                   |
| `clear()`         | 清空容器（删除所有元素）                  |
| `erase(pos)`      | 删除pos迭代器所指的元素，返回下一个元素的迭代器     |
| `erase(beg, end)` | 删除区间beg,end)的所有元素，返回下一个元素的迭代器 |
| `erase(elem)`     | 删除容器中值为elem的元素                |

## 二、常用函数
### 2.1 sort( )
  `sort `用于对容器或数组中的元素进行排序。它位于 头文件中。
```C++
	// 升序排列
	/* 如果是字符串的组合，则会根据其前缀的公共程度排序，例如：14.最长公共前缀 */
	sort(起始迭代器, 结束迭代器);
	// 降序排列
	sort(v.begin(), v.end(), std::greater<int>());
```

### 2.2 reverse( )
  `reverse` 用于反转容器或数组中元素的顺序，同样位于 头文件中。
```C++
	reverse(起始迭代器, 结束迭代器);
```

### 2.3 max( ) 和 min( )
  `max `和 `min` 用于获取两个值中的最大值和最小值，定义在 头文件中。
```C++
	// 取较大者
	max(a, b);
	// 取较小者
	min(a, b);
```

### 2.4 accumulate( )
  `accumulate` 用于计算容器或数组中元素的累计和，它定义在 头文件中。
```C++
	accumulate(起始迭代器, 结束迭代器, 初始值);
```

### 2.5 find( )
  `find` 用于在容器或数组中查找指定的元素，返回一个指向该元素的迭代器，它位于 头文件中。
```C++
	/* 这里类似与容器中的.find，如果找到了会返回.end */
	find(起始迭代器, 结束迭代器, 要查找的值);
```

### 2.6 copy( )
  `copy` 用于将一个范围内的元素复制到另一个范围，位于 头文件中。
```C++
	copy(起始迭代器, 结束迭代器, 目标起始迭代器（可选）);
```

### 2.7 to_string( )
  `to_string` 是 C++ 标准库中的一个函数，用于将数值类型转换为其对应的字符串表示形式。
```C++
	/* 将基本的数值类型（如整数、浮点数）转换为 string 类型的字符串。 */
	/*  返回值是一个 std::string，其中包含了数值的字符串表示形式。   */
	string to_string(int val);
	string to_string(long val);
	string to_string(long long val);
	string to_string(unsigned val);
	string to_string(unsigned long val);
	string to_string(unsigned long long val);
	string to_string(float val);
	string to_string(double val);
	string to_string(long double val);
```

### 2.8 stoi( )
  `stoi`用于将string 类型的字符串转换为 int 类型的整数。stoi 是 string to integer 的缩写。
```C++
	int stoi(const std::string& str, std::size_t* pos = 0, int base = 10);
```
参数说明
- str：要转换的字符串。
- pos（可选）：指向 size_t 的指针，函数执行完毕后，这个指针会指向第一个未处理字符的下标。如果不需要，可以传递 nullptr 或使用默认值。
- base（可选）：表示进制，默认为 10，表示将字符串解释为十进制数。如果需要转换其他进制的数，比如二进制、八进制或十六进制，可以传递相应的值（2、8、16）。
- 返回值：返回转换后的整数值。

### 2.9 getline( )
   `getline` 函数在 C++ 中用于从输入流中读取一行文本，直到遇到换行符 \n（或者其他指定的分隔符），并将读取的内容存储到一个字符串中。
```C++
getline(std::istream& is, std::string& str, char delim);

//示例
#include <iostream>
		...
   	vector<std::string> words;
    string word;
    stringstream ss(s);

    while (getline(ss, word, ',')) {  // 以逗号为分隔符提取单词
        words.push_back(word);
    }
```
参数说明
is：输入流对象，可以是 std::cin、std::ifstream、std::istringstream 等。
str：字符串对象，getline 将读取的内容存储到该字符串中。
delim（可选）：自定义分隔符，如果不指定，默认使用换行符 \n 作为分隔符。

### 2.10 字符判断

| 函数名        | 功能          |
| ---------- | ----------- |
| isalpha(c) | 判断是否是字母     |
| islower(c) | 判断是否是小写字母   |
| isupper(c) | 判断是否是大写字母   |
| isdigit(c) | 判断是否是数字     |
| isalnum(c) | 判断是否是字母或者数字 |
| tolower(c) | 将c转换为小写字母   |
| toupper(c) | 将c转换为大写字母   |

## 三、迭代器

基本使用方法
首先要定义一个迭代器类型变量（这里以vector容器为例）
定义方法如下：`容器类名::iterator 迭代器名`;
如要定义vector容器的迭代器：`vector<int>::iterator iter;`(这里的iter是变量名，可以自定义)
接下来要利用迭代器访问容器数据

先要了解几个成员函数

| 成员函数       | 功能描述                                                                             |
| ---------- | -------------------------------------------------------------------------------- |
| `begin()`  | 返回指向容器中第一个元素的正向迭代器；如果是 `const` 类型容器，在该函数返回的是常量正向迭代器。                             |
| `end()`    | 返回指向容器最后一个元素之后一个位置的正向迭代器，如果是 `const` 类型容器，在该函数返回的是常量正向迭代器。此函数通常和 `begin()` 搭配使用。 |
| `rbegin()` | 返回指向最后一个元素的反向迭代器，如果是 `const` 类型容器，在该函数返回的是常量反向迭代器。                               |
| `rend()`   | 返回指向第一个元素之前一个位置的反向迭代器。如果是 `const` 类型容器，在该函数返回的是常量反向迭代器。此函数通常和 `rbegin()` 搭配使用。   |

```C++
	int arr[] = {10, 20, 30, 40, 50};
    vector<int> v1;
    v1.assign(arr, arr + 5);//以上为创建一个含有5个元素的vector容器
    vector<int>::iterator iter;//定义迭代器类型变量
    iter = v1.begin();//变量被赋值为指向第一个元素的迭代器
    cout << *iter << endl;
    iter++;//可以用自增操作，让iter指向下一个元素
    cout << *iter << endl;
    iter = v1.begin() + 2;//让iter指向容器中第三个位置
    cout << *iter << endl;
    **结果：10
           20
           30
```

### 四、题目链接

[P1059 [NOIP2006 普及组] 明明的随机数](https://www.luogu.com.cn/problem/P1059)  
[P1739 表达式括号匹配](https://www.luogu.com.cn/problem/P1739)