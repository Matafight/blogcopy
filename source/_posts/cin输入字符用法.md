title: "cin输入字符用法"
date: 2015-03-11 15:49:39
categories: C++
tags:
---
程序的数据输入有输入缓冲区，cin都是从输入缓冲区中取数据，当一次键盘输入结束后，数据存到缓冲区。cin从里面取。当输入缓冲区有**残留数据**时，cin不会重新请求键盘输入，而是直接从缓冲区里面取。

cin 常用的输入方式有：

1. cin 遇到空白字符就结束  
2. cin.get(arr,20,'/') 可以接受空白字符，表示读入19个字符或者是遇到'/'停止，如果没有第三个参数默认是'\n', cin.get() 与cin.getline() 不同的是cin.getline（）的指针遇到终止字符时读取终止字符之前的字符，然后指针跳过终止字符，这样下一次读取是从终止字符后面的第一个字符开始的，而cin.get()则不跳,下一次直接冲终止字符开始读取。
cin.get()也可以用来跳过一个字符。
3. cin.getline(arr,20,'/')
4. getline(cin,arr) 在头文件<string>中定义，功能是读取一行字符串，读到换行符结束并且抛弃换行符


```C++
#include <iostream>
using namespace std;
int main()
{
	char ch[20];
	cout << "enter a sentence:" << endl;
	cin >> ch;
	cout << "The string read with cin is:" << ch << endl;
	cin.getline(ch, 20, '/');  //读个字符或遇'/'结束
	cout << "The second part is:" << ch << endl;
	cin.getline(ch, 20);  //读个字符或遇'/n'结束
	cout << "The third part is:" << ch << endl;
	system("pause");
	return 0;
}
```
```bash
 enter a sentence:
I like C++./I study C++./I am happy.
The string read with cin is:I
The second part is: like C++.
The third part is:I study C++./I am h
请按任意键继续. . .
```
cin>>ch 遇到空格 停止输入，此时ch[0]='I',ch[1]='\0',指针移到字符串中的空格处。


cin.get()读取输入时，回车被输入到了缓冲区。
```c++
#include<iostream>
using namespace std;
int main()
{
	char str[8];
	char ch;
	char ch2;
	cin.get(ch);
	cin.get(ch2);
	cout << ch<<" "<<ch2;
	system("pause");
	return 0;
}
```

result:
```bash
a
a
请按任意键继续. . .
```
回车符被送到缓冲区赋给了ch2

cin.getline()的回车没有送到缓冲区

