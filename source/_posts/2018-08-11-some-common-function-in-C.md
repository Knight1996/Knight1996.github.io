---
title: C语言一些常用函数总结
date: 2018-08-11 10:20:53
tags:
- C++/C
categories:
- 技术
toc: true
---

最近刚刚入职，公司的开发语言是C，入职以来每天就是做做OJ，以前没有专门学过C语言，也碰到了不少很常用但是我不知道的库函数。
总结才能更好的提升，趁着周末总结一下常用的C语言函数吧。<!--more-->

# 标准头文件

首先总结一下C语言的标准头文件，主要包括下面的这些

<asset.h>   <ctype.h>   <errno.h>   <float.h>
<limits.h>  <locale.h>  <math.h>    <setjmp.h> 
<signal.h>  <stdarg.h>  <stddef.h>  <stdlib.h>
<stdio.h>   <string.h>  <time.h>

个人比较常用的就是 <stdlib.h> <stdio.h> <string.h>

# 输入输出函数<stdio.h>

## 格式化输入输出

经常使用的有 `scanf`  `printf` `sscanf` `sprintf`这几个函数，下面分别介绍一下。

### scanf 和 sscanf

`scanf` 从 `stdin` 中接受输入，将数据以指定的格式(format)输出到变量，简单的来说就是从控制台输入。 函数原型如下：

{% codeblock lang:c %}
int scanf (const char *format, ...)
{% endcodeblock %}

其中 `...` 是变长的变量参数，比如 `scanf ("%d %f", &i, &f)` 表示将标准输入输入的参数赋值给变量 `i` 和 `f` 。

`sscanf` 从字符串中接受输入，将数据以指定格式输出到变量，常用做将字符串转化为其它变量。利用它可以从字符串中取出整数、浮点数和字符串等等。它的使用方法简单，特别对于整数和浮点数来说。函数原型如下：

{% codeblock lang:c %}
int sscanf (const char *buffer, const char *format, ...)
{% endcodeblock %}

两个函数类似，只是 `scanf` 从控制台中获取输入，而 `sscanf` 以固定的字符串为输入源。

常见用法：

1.从字符串中输入到另外的字符串

{% codeblock lang:c %}
char str[512] = {0};
sscanf("123456", "%s", str);
printf("str=%s\n", str);        //输出str=123456
{% endcodeblock %}

2.取指定长度的字符串。如在下例中，取最大长度为4字节的字符串。

{% codeblock lang:c %}
sscanf("123456 ", "%4s", str);
printf("str=%s\n", str);        //str的值为1234
{% endcodeblock %}

3.取到指定字符为止的字符串。如在下例中，取遇到空格为止字符串。

{% codeblock lang:c %}
sscanf("123456 abcdedf", "%[^ ]", str);     //注意^后面有一空格
printf("str=%s\n", str);                    //str的值为123456
{% endcodeblock %}

4.取仅包含指定字符集的字符串。如在下例中，取仅包含1到9和小写字母的字符串。

{% codeblock lang:c %}
sscanf("123456abcdedfBCDEF", "%[1-9a-z]", str);
printf("str=%s\n", str);                            // str的值为123456abcdedf
{% endcodeblock %}

5.取到指定字符集为止的字符串。如在下例中，取遇到大写字母为止的字符串。

{% codeblock lang:c %}
sscanf("123456abcdedfBCDEF", "%[^A-Z]", str);
printf("str=%s\n", str);                            // str的值为123456abcdedf
{% endcodeblock %}

下面具体讲解一些例子 `%[]` 的用法：

`%[]` 表示要读入一个字符集合, 如果 `[` 后面第一个字符是 `”^”` ，则表示反意思。 `[]` 内的字符串可以是1或更多字符组成。空字符集（`%[]`）是违反规定的，可导致不可预知的结果。`%[^]` 也是违反规定的。

+ `%[a-z]` 读取在 `a-z` 之间的字符串，如果不在此区间则停止，如

{% codeblock lang:c %}
char s[] = "hello, my friend”;              // 注意: ,逗号在不在a-z之间
sscanf(s, “%[a-z]”, string);                // string=hello
{% endcodeblock %}

+ `%[^a-z]` 读取不在 `a-z` 之间的字符串，如果碰到 `a-z` 之间的字符则停止，如

{% codeblock lang:c %}
char s[] = "HELLOkitty”;             
sscanf(s, “%[^a-z]”, string);           // string=HELLO
{% endcodeblock %}               

+ `%*[^=]` 前面带 `*` 号表示不保存变量。跳过符合条件的字符串。

{% codeblock lang:c %}
char s[] = "notepad=1.0.0.1001";
char szfilename [32] = "";
int i = sscanf(s, "%*[^=]", szfilename);        // szfilename=NULL,因为没保存
int j = sscanf(s, "%*[^=]=%s", szfilename);     // szfilename=1.0.0.1001
{% endcodeblock %}


+ `%[^=]` 读取字符串直到碰到 `=` 号，`^` 后面可以带更多字符,如：

{% codeblock lang:c %}
char s[]="notepad=1.0.0.1001";
char szfilename [32] = "";
int i = sscanf(s, "%[^=]", szfilename);         // szfilename=notepad 
{% endcodeblock %}


### printf 和 sprintf

`printf` 将变量以指定格式输出到 `stdout`，函数原型如下：

{% codeblock lang:c %}
int printf (const char *format, ...)
{% endcodeblock %}

例如 `printf ("%d", i)` 表示将变量 `i` 的值送到标准输出,返回值是输出的字符串数量。


`sprintf` 函数原型如下：

{% codeblock lang:c %}
int sprintf (char *buffer, const char *format, ...)
{% endcodeblock %}

`sprintf()` 函数和 `printf()` 类似，格式控制完全一样，只要是 `printf` 使用的格式化字符串，在 `sprintf` 都可以使用，只是把输出发送到buffer(缓冲区)中。返回值是写入的字符数量。

下面列举一些常用的用法：

1.格式化数字字符串

{% codeblock lang:c %}
sprintf(s, "%-8X", 12345);          //s变成：“12345”
{% endcodeblock %}

大写的X代表，16进制的大写形式。宽度占8个位置， `-` 代表左对齐。

2.控制浮点数的打印格式

浮点数使用格式符 `”%f”` 控制，默认保留小数点后 6 位数字 `%m.nf` 格式，其中 m 表示打印的宽度， n 表示小数点后的位数

{% codeblock lang:c %}
sprintf(s, "%10.3f", 3.1415626);            //s变成："3.142" 
{% endcodeblock %}

3.连接两个字符串

直接连接：

{% codeblock lang:c %}
char dest[256];

char src1[] = {'a','b','c','d','e'};
char src2[] = {'1','2','3','4'};

sprintf(dest, "%.5s%.4s", src1, src2);          //output:“abcde1234”
{% endcodeblock %}

截取字符串的某一些字符进行连接：

{% codeblock lang:c %}
char dest[256];

char src1[] = {'a','b','c','d','e'};
char src2[] = {'1','2','3','4'};

sprintf(dest, "%.*s%.*s", 2, src1, 3, src2);         //output:“ab123”
{% endcodeblock %}

4.利用返回值

`printf` 与 `sprintf` 的返回值是写入的字符数量。

也就是说每当一次 `sprinf` 调用结束以后，你无须再调用一次 `strlen` 便已经知道了结果字符串的长度。如：

{% codeblock lang:c %}
int len = sprintf(s, "%d", i); 
{% endcodeblock %}

格式化参量表如下：

+ `%d` 十进制有符号整数

+ `%u` 十进制无符号整数

+ `%f` 浮点数

+ `%s` 字符串

+ `%c` 单个字符

+ `%p` 指针的值

+ `%e` 指数形式的浮点数

+ `%x`, `%X` 无符号以十六进制表示的整数

+ `%0` 无符号以八进制表示的整数

+ `%g` 自动选择合适的表示法

可以在 `%` 和字母之间插进数字表示最大场宽。 例如: `%3d` 表示输出3位整型数, 不够3位右对齐。 `%9.2f` 表示输出场宽为9的浮点数, 其中小数位为2, 整数位为6, 小数点占一位, 不够9位右对齐。`%8s` 表示输出8个字符的字符串, 不够8个字符右对齐。 如果字符串的长度、或整型数位数超过说明的场宽, 将按其实际长度输出。 但对浮点数, 若整数部分位数超过了说明的整数位宽度, 将按实际整数位输出; 若小数部分位数超过了说明的小数位宽度, 则按说明的宽度以四舍五入输出。另外, 若想在输出值前加一些0, 就应在场宽项前加个0。 例如: `%04d` 表示在输出一个小于4位的数值时, 将在前面补0使其总宽度为4位。如果用浮点数表示字符或整型量的输出格式, 小数点后的数字代表最大宽度, 小数点前的数字代表最小宽度。 例如: `%6.9s` 表示显示一个长度不小于6且不大于9的字符串。若大于9, 则第9个字符以后的内容将被删除。

可以在 `%` 和字母之间加小写字母 `l` , 表示输出的是长型数。例如: `%ld` 表示输出 `long` 整数, `%lf` 表示输出 `double` 浮点数。

可以控制输出左对齐或右对齐, 即在 `%` 和字母之间加入一个 `-` 号可说明输出为左对齐, 否则为右对齐。例如: `%-7d` 表示输出7位整数左对齐，`%-10s` 表示输出10个字符左对齐。



## 字符/行式输入输出<stdio.h>

### 字符输入输出 fgetc fputc

`fgetc` 的作用就是将一个字符读出，比较简单，函数原型如下：

{% codeblock lang:c %}
fgetc(ch, fp)
{% endcodeblock %}

其中fp是文件指针，指向要读出的文件，ch是预先定义好的字符型常量，用以接收从fp所指文件独处的字符

`fputc` 作用就是实现将一个字符写入文件中，函数原型如下：

{% codeblock lang:c %}
fputc(ch，wfp)
{% endcodeblock %}

其中fp是文件指针,指向要写入的文件，ch是预先定义好的字符型常量

> 这两个函数用fclose打开后，一定要判断是否成功打开

### fgets/fputs gets/puts

`fgets/fputs` 和 `gets/puts` 是两对用于流的单行读写函数。下面先讲 `fgets/fputs` 函数原型如下：

{% codeblock lang:c %}
char *fgets(chat *s, int size, FILE *stream)
char *fputs(const *char s, FILE *stream)
{% endcodeblock %}

`fgets` 是从文件中读取字符串，从参数 `stream` 所指的文件读入字符并存到参数 `s` 所指向的内存空间，直到读取到换行符 `\n` 或者是读到 `size - 1` 个字符为止，最后会加入 `NULL` 作为文件结束。在成功的时候返回值返回 `s` 的指针，失败的时候直接返回 `NULL` 。

`fputs` 则是将指定的字符串写入文件内。用来将 `s` 所指的字符串写入到参数 `stream` 所指向的文件中。函数成功时返回值为写入字符串的个数，失败则返回 `EOF` 。

用法可以如下：

{% codeblock lang:c %}
int str[100];
fputs(fgets(str, 100, strin), strout); 	//输入 hello， 输出 hello
{% endcodeblock %}

`gets/puts` 则是从控制台读取或者输入。 `gets` 从键盘读取一个字符串，直到遇到换行符 `\n` 为止。该函数丢弃换行符，添加一个空字符，然后将字符串返回给调用程序。字符串被存储传递给 `gets` 的 `char` 指针指向的位置。`puts` 一般用来在屏幕上显示文本消息，但是不能显示数值变量。接受一个字符串函数，显示该参数并且自动换行。函数原型如下：

{% codeblock lang:c %}
char *gets(char *str)
puts(str)

puts("Hello World."); == printf("Hello World.\n");
{% endcodeblock %}

gets函数基本与scanf相同，但是存放完毕后，会自动在末尾加上一个结束标记 `\0` 。此外`gets` 一次只能读取一个字符串， `scanf` 则可以同时读取多个字符串。 而且 `gets` 可以读入包含空格、tab的字符串，直到遇到回车(`\n`)为止， `scanf` 不能用来读取空格、tab。

# 数学函数<math.h>

## ceil floor round函数

这几个函数都比较简单，都是取整函数，简单说一下，`ceil` 是取一个大于等于参数值的最小整数， `floor` 是取小于等于参数值的最大整数。而 `round` 函数则是四舍五入。函数原型如下：

{% codeblock lang:c %}
double ceil(double x)
double floor(double x)
double round(double x)
{% endcodeblock %}

# 字符处理函数<ctype.h>

## isupper/islower toupper/tolower

`isupper/islower` 作用是用来检测参数是否为大写/小写字母，是则返回TRUE，否则返回NULL(0)。函数原型如下：

{% codeblock lang:c %}
int isupper(int c)
int islower(int c)
{% endcodeblock %}

需要注意的是，这两个函数都是宏定义，非真正的函数。

`toupper/tolower` 是分别用来将小写/大写字母换成大写/小写字母的。使用方法也很简单，返回值为转化后的字母。函数原型如下：

{% codeblock lang:c %}
char toupper(char c)
char tolower(char c)
{% endcodeblock %}

# 字符串函数<string.h>

## strlen

`strlen` 函数非常常用，目的就是计算字符串的长度，以 `\0` 为止。求出的长度不包含 `\0` 。

{% codeblock lang:c %}
size_t strlen(cs)
{% endcodeblock %}

## strcpy/strncpy

{% codeblock lang:c %}
char *strcpy(char *dest, char *src)
char *strncpy(char *dest, char *src, size_t n)
{% endcodeblock %}

`strcpy` 把 `src` 复制到 `dest` ，要求 `dest` 指定足够大的字符数组。当 `src` 串长度 > `dest` 串长度时，程序仍会将整个 `src` 串复制到 `dest` 区域，可是 `dest` 数组已发生溢出。因此会导致 `dest` 栈空间溢出以致产生崩溃异常。如果不考虑 `src` 串的完整性，可以把 `dest` 数组最后一元素置为 `NULL` ，从 `dest` 串长度处插入 `NULL` 截取字串。
`strncpy` 把 `src` 里的至多n个字符复制到 `dest` 。要求 `dest` 指定一个足够大的字符数组。如果 `src` 里的字符不够n个，就在 `dest` 里填充空字符。它并不像 `strcpy` 一样遇到 `NULL` 才停止复制，而是等凑够n个字符才开始复制。如果n > `dest`串长度，`dest` 栈空间溢出产生崩溃异常。一般情况下，使用 `strncpy` 时，建议将n置为 `dest` 串长度（除非你将多个 `src` 串都复制到 `dest` 数组，并且从 `dest` 尾部反向操作)，复制完毕后，为保险起见，将 `dest` 串最后一字符置NULL。当然，无论是 `strcpy` 还是 `strncpy`，保证 `src` 串长度 < `dest`串长度才是最重要的。

# 功能函数<stdlib.h>

## malloc

`malloc` 函数是一种分配长度为 `num_bytes` 字节的内存块的函数，可以向系统申请分配指定 `size` 个字节的内存空间。`malloc`的全称是`memory allocation`，中文叫动态内存分配，当无法知道内存具体位置的时候，想要绑定真正的内存空间，就需要用到动态的分配内存。
返回类型是 `void*` 类型。`void*` 表示未确定类型的指针。C,C++规定，`void*` 类型可以通过类型转换强制转换为任何其它类型的指针。

{% codeblock lang:c %}
extern void *malloc(unsigned int num_bytes)
{% endcodeblock %}

`malloc`分配的内存大小至少为`size`参数所指定的字节数

`malloc`的返回值是一个指针，指向一段可用内存的起始地址

多次调用`malloc`所分配的地址不能有重叠部分，除非某次`malloc`所分配的地址被释放掉

`malloc`和`free`函数是配对的，如果申请后不释放就是内存泄露;如果无故释放那就是什么都没有做，释放只能释放一次，如果释放两次及两次以上会出现错误（但是释放空指针例外，释放空指针其实也等于什么都没有做，所以，释放多少次都是可以的）

## atof/atoi/atol

atof将字符串转换成浮点型数，atoi函数将字符串转换成整型数，atol将字符串转换成长整型数。函数比较简单，原型如下： 
{% codeblock lang:c %}
double atof(const char *nptr)
int atoi(const  char *  nptr)
long atol(const char *nptr)
{% endcodeblock %}

这三个函数都是会跳过前面的空格字符，直到遇上数字或正负符号才开始做转换，而再遇到非数字或字符串结束时 `'\0'` 才结束转换，并将结果返回。
