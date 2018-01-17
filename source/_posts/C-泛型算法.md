title: C++ primer-泛型算法
date: 2017-02-21 21:23:06
categories: C++
tags:
---
# 第十章 泛型算法

## 10.1. 概述

标准库已经为容器类型定义了一些基本操作，但是并没有为各种容器定义诸如排序，查找特定元素等算法，但是标准库定义了一些泛型算法用来解决诸如容器的排序查找等问题。之所以称之为泛型算法是因为，这些算法针对多种容器的多种数据类型均有效。
大多数算法都定义在头文件algorithm 中，一般情况下，__泛型算法并不直接操作容器 __，而是遍历由两个迭代器指定的元素的范围进行操作。
迭代器令算法不依赖于容器，但是算法依赖于元素操作的类型，因为算法常常需要比较容器中两个元素的大小，这就要求元素类型支持“<”或"=="运算符，不过大多数算法都可以使用自定义操作来代替运算符。
	
<!--more-->
	
练习 10.1
	
```cpp
	#include<iostream>
    #include<algorithm>
    #include<vector>
    int main()
    {
        std::vector<int> a = { 1, 2, 3, 12, 2 };//列表初始化
        int count_times = std::count(a.begin(), a.end(), 2);
        std::cout << count_times << std::endl;
        system("pause");
        return 0;
    }
```
    
    练习 10.2
    
```cpp
    	std::list<std::string> a = { "a", "bb" ,"a"};
	    std::string val = "a";
	    int count_times = std::count(a.begin(), a.end(), val);
	    std::cout << count_times << std::endl;
```
    
    
## 10.2 初识泛型算法
### 10.2.1 只读算法

顾名思义，只读算法只读取迭代器指定范围内的元素，而不改变元素，如 find、count等算法，还有accumulate（求和）算法。
练习10.3:
```cpp
    std::vector<int> a = { 1, 2, 3, 4, 5 };
	int sum = std::accumulate(a.cbegin(), a.cend(), 0);//accumulate定义在头文件 numeric中
	std::cout << sum << std::endl;
```
练习 10.4
会导致精度损失，相当于将double类型强制转换为int类型，会损失小数点后面的数。

### 10.2.2 写容器算法
一些算法将新值赋予容器中的元素。当我们使用此类算法时，应当注意容器的大小一定不能小于我们要写入的元素的个数。记住因为算法不会执行容器操作，因此它们自身不能改变容器的大小。
#### 算法不检查写操作
因此，对一个空容器进行写操作是危险的，如：
```cpp
vector<int> vec; //空容器
fill(vec.begin(),10,0); //错误
fill(vec.begin(),vec.end(),0);//安全
```

#### 介绍back_inserter
一种保证算法有足够的空间来容纳输入数据的方法是使用 __插入迭代器__ .插入迭代器是一种向容器中插入元素的迭代器。
back_inserter 接受一个指向容器的引用，返回一个与该容器绑定的插入迭代器，通过对该迭代器赋值，赋值操作符会调用push_back 将一个具有给定值的元素添加到容器中。
```cpp
vector<int> vec;
it = back_inserter(vec);
*it = 42;
```
我们常常通过back_inserter创建一个迭代器，作为算法的目标位置使用：
```cpp
vector<int> vec;
fill_n(back_inserter(vec),10,0);
```

#### 拷贝算法
用来将一个容器中的内容拷贝到另一个容器。传递给copy的目标序列至少要包含与输入序列一样多的元素。
```cpp
int a[] = {0,1,2,3,4,5};
int a2[sizeof(a)/sizeof(*a)];
copy(begin(a),end(a),a2);//把a的内容拷贝给a2，copy的范围值为指向拷贝到a2尾元素之后的位置
```
上面的代码中值得注意的是sizeof(a)的值等于24，但是sizeof(a+0)却等于4，a是个数组名，它是一个地址常量，但是在某些情况下，比如sizeof(a)，a代表整个数组，而不是这个常量本身，所以数组名并不仅仅是一个指针，而(a+0)相当于指向第零个元素的指针，其大小为4。
replace函数能替换指定范围迭代器内的元素值
```cpp
replace(a.begin(),a.end(),0,42);//将0替换为42，原地替换
```
replace还有一个copy版本
```cpp
replace_copy(a.begin(),a.end(),back_inserter(vec),0,42); //vec包含容器a的一份拷贝，不过vec中0换成了42
```
练习10.6
```cpp
	std::vector<int>vec = { 0, 1, 2, 3, 4, 5 };
	std::fill_n(vec.begin(), vec.size(), 0);
```

练习10.7
(a)
copy(lst.cbegin(),lst.cend(),vec)中，vec是空容器，无法copy,可以使用
copy(lst.cbegin(),lst.cend(),back_inserter(vec))
或者vec.resize(lst.size())
(b)
vec.reserve(n)只是预分配内存，但是实际不可访问，只是告诉程序我需要这么大的空间，当n>capacity时，才会重新分配空间，当n<= capacity时，则不会重新分配。 size与capacity的区别: size是容器中的元素数量，capacity指在这段连续的空间中，容器最多可以存储的数量。
可以用resize()代替reserve()
(c)算法并没有改变容器的大小，而是通过插入迭代器改变的。

### 10.2.3 重排容器元素的算法

#### 消除重复单词
习题10.9
```cpp
#include<iostream>
#include<algorithm>
#include<vector>
#include<list>
#include<numeric>
#include<string>
using namespace std;

void elimDup(vector<string> &words)
{
	sort(words.begin(), words.end());
	auto end_pos = unique(words.begin(), words.end());//unique将不重复的元素排在前面，返回第一个被重复的元素的位置
	words.erase(end_pos, words.end());
}
int main()
{
	vector<string> words = { "the", "quick", "red", "fox", "jumps", "over", "the", "slow", "red", "turtle" };
	elimDup(words);
	for (vector<string>::iterator iter = words.begin(); iter != words.end(); iter++)
	{
		cout<< (*iter) << endl;//cout<<string，需要包含头文件<string>
	}
	system("pause");
	return 0;
}
```
练习10.10,为什么算法不改变容器大小？
因为要将算法操作与容器操作分离，便于设计。
算法通过迭代器改变容器大小。

### 10.3 定制操作

很多算法需要比较元素，标准库允许我们使用自定义的比较函数等替代运算符<,==等。
#### 10.3.1向算法传递函数
希望将words按照长度排序，长度相同的情况下按字典序排序。为了按长度重排vector,我们将使用sort的第二个版本，此版本是重载过的，它还有第三个参数，这个参数是一个 __谓词__ 
##### 谓词
谓词是一个可调用的表达式，其返回结果是一个能用作条件的值。标准库算法所用的谓词分为两类：一元谓词和二元谓词，分别代表只接受一个参数和两个参数。接受谓词参数的算法对输入序列中的元素调用谓词。因此，元素类型必须能转换为谓词的参数类型。
自定义的谓词表达式：
```cpp
bool isShorter(const string & s1,const string &s2)
{
    return s1.size() < s2.size();
}
//由短到长重新排列
sort(words.begin(),words.end(),isShorter);
```
##### 排序算法
当我们将words按长短大小重新排列时，还希望具有相同长度的元素按照字典排序。为了满足上述要求，我们采用stable_sort()，这个稳定排序算法能保证当两个元素相等时不改变它们的相对顺序。
新的代码
```cpp
elimDup(words);
stable_sort(words.begin(),words.end(),isShorter);
for (const auto &s : words)
{
	cout << s << endl;
}

```

练习10.13

#### 10.3.2 lambda表达式

根据算法接受一元谓词或二元谓词，我们传递给算法的谓词必须严格接受一个或两个参数，但是有时候我们需要传递更多的参数，超出了算法对谓词参数个数的限制。比如在上例中，我们希望输出长度大于等于5的单词的个数，并且只打印这些长度满足条件的单词。

lambda 表达式:
[捕获列表] (参数列表)-> 返回值类型 {函数体}
捕获列表中的变量表示要传递到函数体的局部变量，lambda表达式相当于匿名的函数。其也是通过函数调用符调用的。
我们可以忽视参数列表和返回值类型，但必须永远包含捕获列表和函数体。
```cpp
auto f  = []{return 42};
cout <<f()<<endl;
```
lambda表达式的一个简单的用法如上所示。当返回值类型为空时，lambda根据代码的返回值推断返回类型，如果最后一句是return时，返回值类型与要return的值的类型相同，否则类型为空。

##### 向lambda传递参数
isShorter的lambda版本
```cpp
[ ] (const string &s1,const string &s2){return s1.size()<s2.size();}
```
##### 使用捕获列表
判断一个string类型长度是否大于5
```cpp
int sz = 5;
[sz] (const string&s){return s.size()>=sz;}
```

##### 调用find_if()函数
查找第一个程度大于等于5的字符串
```cpp
int sz = 5;
auto wc = find_if(words.begin(),words.end(),[sz] (const string &s){return s.size() >= sz;});//find_if 返回第一个满足条件的迭代器,
```

##### 调用for_each函数
打印所有长度大于等于5的字符串
```cpp
int sz = 5;
for_each(words.begin(),words.end(),[sz] (const string &s1){if (s1.size()>=5) cout << s1<<endl;})
或者
for_each(wc,words.end(),[](const string &s1){cout << s1<<endl;})
```

#### 10.3.3 lambda的捕获与返回
##### 值捕获
##### 引用捕获
##### 隐式捕获
##### 可变lambda
lambda表达式可以通过mutable关键字改变传入lambda表达式中的参数值，也可以用引用的方式，只要引用不是const类型的就可以。
##### 指定lambda返回值类型
当lambda表达式函数体包含return之外的任何语句时，lambda都会推导出返回值类型为void
```cpp
transform(vec.begin(),vec.end(),vec.begin(),[](const int & i){ return i<0?-i:i;})//正确
transform(vec.begin(),vec.edn(),vec.begin(),[](const int & i){if(i<0) return -i;else return i;}) //错误,没有显示指定返回值类型，且有多行语句，lamdba推断出返回值类型为void，与期望值不符
```
当我们需要为一个lambda定义返回值类型时，必须使用尾置返回类型。尾置返回类型跟在形参列表后面并以->符号开头
```cpp
transform(vec.begin(),vec.end(),vec.begin(),[](int i)->int {if(i<0) return -i;else return i;})
```

练习 10.20
```cpp
	vector<string> words = { "the", "quick", "red", "fox", "jumps", "over", "the", "slow", "red", "turtle" };
	int sz = 6;
	int ret = count_if(words.begin(), words.end(), [=](const string &s1){return s1.size() >= sz; });
	cout << ret << endl;
	int ret2 = count_if(words.begin(), words.end(), [=](const string &s1)->bool{ if (s1.size() >= sz) return true; else return false; });
	cout << ret2 << endl;
```
##### 标准库bind函数
待看....

### 10.4 再探迭代器
除了为每个容器定义的迭代器之外，标准库在头文件iterator中还定义了额外几种迭代器。这些迭代器包括以下几种:
1. 插入迭代器
2. 流迭代器
3. 反向迭代器
4. 移动迭代器

#### 10.4.1 插入迭代器
插入器是一种迭代器适配器，它接受一个容器，生成一个迭代器，能实现向给定容器添加元素。当我们通过插入迭代器进行赋值时，该迭代器调用容器操作向给定容器的指定位置插入元素。

练习 10.26，解释三种插入迭代器的不同之处？
1. back_inserter ，调用容器的push_back()方法
2. front_inserter, 调用 push_front()方法
3. inserter, 调用 c.insert(iter,val)方法，c为迭代器绑定的容器，iter为指定要插入的迭代器位置，val为要插入的值，该方法将val插入到迭代器iter所在位置之前。

```cpp
it = inserter(c,iter);
*it = val;
//等价于
it = c.insert(iter,val);
++it;
```

练习 10.27
```cpp
vector<string> words = { "the", "quick", "red", "fox", "jumps", "over", "the", "slow", "red", "turtle" };
list<string> lis;
sort(words.begin(), words.end());
unique_copy(words.begin(), words.end(), back_inserter(lis));
```

#### 10.4.2 iostream 迭代器
虽然iostream 不是容器，但标准库定义了可以用于这些IO类型对象的迭代器。 istream_iterator 读取输入流，ostream_iterator向一个输出流写数据，这些迭代器将它们对应的流当作一个特定类型的元素序列来处理。通过使用流迭代器，我们可以用泛型算法从流对象读取数据以及向其写入数据。

##### istream_iterator
当创建一个流迭代器时，必须指定迭代器将要读写的对象类型。一个istream_iterator使用>>来读取流。因此，istream_iterator要读取的类型必须定义了输入运算符。当创建一个istream_iterator时，将它绑定到一个输入流上。当然，我们还可以默认初始化迭代器，这样就创建了一个可以当做尾后值使用的迭代器
```cpp
istream_iterator<int> init_iter(cin);
istream_iterator<int> eof;
vector<int> vec;
while(init_iter!=eof)
{
    vec.push_back(*init_iter++);
}
//直到遇见文件结尾或非法字符时终止循环
//将流中的数据赋值给vector的另一个写法是
vector<int>vec(init_iter,eof);
```