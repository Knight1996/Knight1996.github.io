---
title: C++ string使用
date: 2018-04-30 15:59:20
tags:
- C++/C
categories:
- 技术
toc: true
---

string 是C++ 标准库的一个重要的部分，主要用于字符串处理。可以使用输入输出流方式直接进行操作，也可以通过文件等手段进行操作。同时 C++ 的算法库对string也有着很好的支持，而且string还和c语言的字符串之间有着良好的接口。虽然也有一些弊端，但是瑕不掩瑜。<!--more-->

# 声明和初始化

想使用string首先要在头文件中加入`<string>` 
声明方式也很简单

## 声明

{% codeblock lang:cpp %}
string a;       //声明一个string对象
string ss[10];  //声明一个string对象的数组
{% endcodeblock %}

## 初始化

使用等号的初始化叫做拷贝初始化，不用等号的初始化叫做直接初始化。

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main(){
    string s;                   //默认初始化，一个空字符串
    string s1("ssss");          //s1是字面值"ssss"的副本
    string s2(s1);              //s2是s1的副本
    string s3 = s2;             //s3是s2的副本
    string s4(10, 'c');         //把s4初始化，十个'c' 
    string s5 = "hiya";         //拷贝初始化
    string s6 = string(10, 'c');//拷贝初始化，生成一个初始化好的对象，拷贝给s6

    //string s(cp, n)
    char cs[] = "12345";
    string s7(cs, 3);           //复制字符串cs的前三个字符到s当中

    //string s(s2, pos2)
    string s8 = "asac";
    string s9(s8, 2);           //从s2的第二个字符开始拷贝，不能超过s2的zise

    //string s(s2, pos2, len2)
    string s10 = "qerqfdsdfa";
    string s11(s10, 3, 4);      //s4是s3从下标3开始4个字符的拷贝，超过s3.size出现未定义

    return 0;
}
{% endcodeblock %}

# 字符串处理

## substr操作

注意substr没有迭代器作为参数的操作

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main(){
    string s = "abcdefg";

    //s.substr(pos, n) 返回字符串位置味pos1后面的n个字符组成的串，pos从0开始
    string s2 = s.substr(1, 5);     //bcdef

    //s.substr(pos) 得到一个pos到结尾的串 pos从0开始
    string s3 = s.substr(4)         //efg

    return 0;
}
{% endcodeblock %}

如果输入的位置超过字符的长度，会抛出一个 out_of_range 的异常

## insert操作

注意用迭代器当参数和无符号数当参数的区别

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string str = "to be question";
    string str2 = "the ";
    string str3 = "or not to be";
    string::iterator it;

    //s.insert(pos, str) 在s的pos位置插入str
    str.insert(6, str2);                 // to be the question

    //s.insert(pos, str, a, n) 在s的pos位置插入str中插入位置a到后面的n个字符
    str.insert(6, str3, 3, 4);             // to be not the question

    //s.insert(pos, cstr, n)//在pos位置插入cstr字符串从开始到后面的n个字符
    str.insert(10, "that is cool", 8);    // to be not that is the question

    //s.insert(pos, cstr)在s的pos位置插入cstr
    str.insert(10, "to be ");            // to be not to be that is the question

    //s.insert(pos, n, ch)在s.pos位置上面插入n个ch
    str.insert(15, 1, ':');               // to be not to be: that is the question

    //s.insert(s.it, ch)在s的it指向位置前面插入一个字符ch，返回新插入的位置的迭代器
    it = str.insert(str.begin() + 5, ','); // to be, not to be: that is the question

    //s.insert(s.it, n, ch)//在s的it所指向位置的前面插入n个ch
    str.insert (str.end(), 3, '.');       // to be, not to be: that is the question...

    //s.insert(it, str.ita, str.itb)在it所指向的位置的前面插入[ita,itb)的字符串
    str.insert (it+2, str3.begin(), str3.begin()+3); // to be, or not to be: that is the question...

    return 0;
}
{% endcodeblock %}

## erase操作

用来执行删除操作
删除操作共有三种

+ 指定pos和len，其中pos为起始位置，pos以及后面len - 1个字符都删除
+ 迭代器，删除迭代器指向的字符
+ 迭代器范围，删除这一范围内的字符串，范围左闭右开

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main ()
{
    string str ("This is an example sentence.");
    cout << str << endl;        // "This is an example sentence."
                                //            ^^^^^^^^
    str.erase (10, 8);
    //直接指定删除的字符串位置第十个后面的8个字符
    cout << str << endl;        // "This is an sentence."
                                //           ^
    str.erase (str.begin() + 9);
    //删除迭代器指向的字符
    cout << str << endl;        // "This is a sentence."
                                //       ^^^^^
    str.erase (str.begin() + 5, str.end() - 9);
    //删除迭代器范围的字符
    cout << str << endl;        // "This sentence."
    return 0;
}
{% endcodeblock %}

## append和replace操作

append函数可以用来在字符串末尾追加字符和字符串。由于string重载了运算符，也可以用 += 操作实现
replace顾名思义，就是替换的意思，先删除，后增加。

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;
int main ()
{
    string str;
    string str2 = "Writing ";
    string str3 = "print 10 and then 5 more";

    //直接追加一个str2的字符串
    str.append(str2);                       // "Writing "
    //后面追加str3第6个字符开始的3个字符串
    str.append(str3, 6, 3);                   // "10 "
    //追加字符串形参的前5个字符
    str.append("dots are cool", 5);          // "dots "
    //直接添加
    str.append("here: ");                   // "here: "
    //添加10个'.'
    str.append(10, '.');                    // ".........."
    //添加str3迭代器范围的字符串
    str.append(str3.begin() + 8, str3.end());  // " and then 5 more"
    //最后这个比较特殊，意思是添加5个'A'，实际上参数里面的65对应的asc码就是65
    str.append<int>(5, 65);                // "....."
    //字符串追加也可以用重载运算符实现
    str += "lalala";
    cout << str << endl;
    return 0;
}
{% endcodeblock %}

replace的使用方法，replace支持使用无符号整数寻找位置，也支持用迭代器寻找位置

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main ()
{
    string base = "this is a test string.";
    string str2 = "n example";
    string str3 = "sample phrase";
    string str4 = "useful.";

    // replace signatures used in the same order as described above:

    // Using positions:                 0123456789*123456789*12345
    string str = base;                  // "this is a test string."
    //第9个字符以及后面的4个字符被str2代替
    str.replace(9, 5, str2);            // "this is an example string." (1)
    //第19个字符串以及后面的5个字符用str的第7个字符以及后面的5个字符代替
    str.replace(19, 6, str3, 7, 6);     // "this is an example phrase." (2)
    //第8个字符以及后面的9个字符用字符串参数代替
    str.replace(8, 10, "just a");       // "this is just a phrase."     (3)
    //第8个字符以及后面的5个字符用字符串参数的前7个字符替换
    str.replace(8, 6, "a shorty", 7);   // "this is a short phrase."    (4)
    //第22以及后面的0个字符用3个叹号替换
    str.replace(22, 1,  3, '!');        // "this is a short phrase!!!"  (5)
    //迭代器的原理同上
    // Using iterators:                                               0123456789*123456789*
    str.replace(str.begin(), str.end()-3, str3);                        // "sample phrase!!!"      (1)
    str.replace(str.begin(), str.begin() + 6,"replace");                // "replace phrase!!!"     (3)
    str.replace(str.begin() + 8, str.begin() + 14, "is coolness", 7);   // "replace is cool!!!"    (4)
    str.replace(str.begin() + 12, str.end() - 4, 4, 'o');               // "replace is cooool!!!"  (5)
    str.replace(str.begin() + 11, str.end(), str4.begin(), str4.end()); // "replace is useful."    (6)
    cout << str << endl;   
    return 0;
}
{% endcodeblock %}

## assign操作

assign操作在一起列容器当中都存在，比如vector等等。是一个很基本的操作函数，string使用assign可以灵活的对其进行赋值。 

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main ()
{
    string str;
    string base = "The quick brown fox jumps over a lazy dog.";

    // used in the same order as described above:
    //直接把base赋值给str
    str.assign(base);
    std::cout << str << endl;
    //把base第10个字符以及后面的8个字符赋给str
    str.assign(base, 10, 9);
    cout << str << endl;            // "brown fox"
    //把参数中的0到6个字符串赋给str
    str.assign("pangrams are cool", 7);
    cout << str << endl;            // "pangram"
    //直接使用参数赋值
    str.assign("c-string");
    cout << str << endl;            // "c-string"
    //给str赋值10个'*'字符
    str.assign(10, '*');
    cout << str << endl;            // "**********"
    //赋值是10个'-'
    str.assign<int>(10, 0x2D);
    cout << str << endl;            // "----------"
    //指定base迭代器范围的字符串
    str.assign(base.begin() + 16, base.end() - 12);
    cout << str << endl;            // "fox jumps over"

    return 0;
}
{% endcodeblock %}


# string的搜索操作

string类中提供了很多性能优秀，使用方便的成员方法。而且在泛型算法当中也有很多实用的技巧。

## find和rfind函数

find函数主要是查找一个字符串是否在调用的字符串中出现过，大小写敏感。

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string str ("There are two needles in this haystack with needles.");
    string str2 ("needle");

    // different member versions of find in the same order as above:
    //在str当中查找第一个出现的needle，找到则返回出现的位置，否则返回结尾
    size_t found = str.find(str2);      //注意size_t
    if (found! = string::npos)          //string::npos指string的结束位置
    cout << "first 'needle' found at: " << found << endl;
    //在str当中，从第found+1的位置开始查找参数字符串的前6个字符
    found = str.find("needles are small", found + 1, 6);
    if (found != string::npos)
    cout << "second 'needle' found at: " << found << endl;
    //在str当中查找参数中的字符串
    found = str.find("haystack");
    if (found != string::npos)
    cout << "'haystack' also found at: " << found << endl;
    //查找一个字符
    found = str.find('.');
    if (found != string::npos)
    cout << "Period found at: " << found << endl;
    //组合使用，把str2用参数表中的字符串代替
    // let's replace the first needle:
    str.replace(str.find(str2), str2.length(), "preposition");
    cout << str << endl;
    return 0;
}
{% endcodeblock %}

rfind函数就是找最后一个出现的匹配字符串，返回的位置仍然是从前往后数的。

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string str ("The sixth sick sheik's sixth sheep's sick.");
    string key ("sixth");//                    ^
    //rfind是找最后一个出现的匹配字符串
    size_t found = str.rfind(key);
    if(found != string::npos)
    {
        cout<< found << endl;//输出23
        str.replace(found, key.length(), "seventh");//找到的sixth替换成seventh
    }

    cout << str << endl;
    return 0;
}
{% endcodeblock %}

## find_..._of函数

+ find_first_of(args) 查找args中任何一个字符第一次出现的位置
+ find_last_of(args) 最后一个出现的位置
+ find_fist_not_of(args) 查找第一个不在args中的字符
+ find_last_not_of 查找最后一个不在args中出现的字符

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string str1 ("Please, replace the vowels in this sentence by asterisks.");
    size_t found1 = str1.find_first_of("aeiou");
    //把所有元音找出来用*代替
    while (found1 != string::npos)
    {
        str1[found1] = '*';
        found1 = str1.find_first_of("aeiou",found1 + 1);
    }
    cout << str1 << endl;

    //在str2中找到第一个不是消协英文字母和空格的字符
    string str2 ("look for non-alphabetic characters...");
    size_t found2 = str2.find_first_not_of("abcdefghijklmnopqrstuvwxyz ");
    if (found2 != string::npos)
    {
        cout << "The first non-alphabetic character is " << str2[found2];
        cout << " at position " << found2 << endl;
    }
    return 0;
}
{% endcodeblock %}

find_last_of和find_last_not_of与first基本相同。


# 比较与转换

类似c语言的字符串比较函数strcmp函数一样，支持字符串比较操作，同时也类似python、C#语言中的函数一样，支持把数字和字符串转换。有些特性是C++11当中才有。

## compare函数

和strcmp函数一样，如果两个字符串相等，那么返回0，调用对象大于参数返回1，小于返回-1。 
在compare当中还支持部分比较，里面有6个参数可以设置。

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string s1 = "123", s2 = "123";
    cout << s1.compare(s2) << endl;             //0

    s1 = "123", s2 = "1234";
    cout << s1.compare(s2) << endl;             //-1

    s1 = "1234", s2 = "123";
    cout << s1.compare(s2) << endl;             //1

    string str1 ("green apple");
    string str2 ("red apple");

    if (str1.compare(str2) != 0)
    cout << str1 << " is not " << str2 << endl;
    //str1的第6个字符以及后面的4个字符和参数比较
    if (str1.compare(6,5,"apple") == 0)
    cout << "still, " << str1 << " is an apple" << endl;

    if (str2.compare(str2.size() - 5, 5, "apple") == 0)
    cout << "and " << str2 << " is also an apple" << endl;
    //str1的第6个字符以及后面的4个字符和str2的第4个字符以及后面的4个字符比较
    if (str1.compare(6, 5, str2, 4, 5) == 0)
    cout << "therefore, both are apples" << endl;
    return 0;
}
{% endcodeblock %}

由于string重载了运算符，可以直接用>,<，==来进行比较，也很方便。


# 数值转换

在IO的部分有过数值和字符串相互转换的例子，使用的是stringstream函数，在c++11当中有定义好的现成的函数取调用，非常方便。

| string和数值转换   |                                       |
|:------------------|--------------------------------------:|
|to_string(val)     |把val转换成string                       |
|stoi(s, p, b)      |把字符串s从p开始转换成b进制的int           |
|stol(s, p, b)      |把字符串s从p开始转换成b进制的long          |
|stoul(s, p, b)     |把字符串s从p开始转换成b进制的unsigned long |
|stoll(s, p, b)     |long long                              |
|stoull(s, p, b)    |unsigned long long                     |
|stof(s, p)         |float                                  |
|stod(s, p)         |double                                 |
|stold(s, p)        |long double                            |

注意，下段代码在MinGw中会报错！即使使用c++11编译也一样，无法识别to_string！

{% codeblock lang:cpp %}
#include <iostream>
#include <string>

using namespace std;

int main()
{
    string s1;
    s1 = to_string(100);
    cout << s1 << endl;
    int a = stoi(s1, 0, 10) + 1;
    cout << a << endl;

    return 0;
}
{% endcodeblock %}

转载自[C++ string的用法和例子](https://blog.csdn.net/tengfei461807914/article/details/52203202)