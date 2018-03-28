---
title: C++中的虚函数和多态
date: 2018-01-28 16:28:26
tags:
- C++
- 虚函数
- 多态
categories:
- 技术
---
<br>  面向对象程序设计(object-oriented programming)的核心思想是数据抽象、继承和动态绑定。    通过使用数据抽象，可以将类的接口与实现分离；使用继承，可以定义相似的类型并对其相似关系建模；使用动态绑定，可以在一定程度上忽略相似类型的区别，而已统一的方式使用他们的对象。<!--more--><br>
OOP的核心思想是多态性(Ploymorphism)。多态这个词源于希腊语，其含义是“多种形式”。我们把具有继承关系的多个类型称为多态类型，因为我们能够使用这些类型的“多种类型”而无须在意他们的差异。引用或指针的静态类型与动态类型不同这一事实正是支持C++语言支持多态性的根本所在。<br>
当我们使用基类的引用或者指针调用基类中定义的一个函数时，我们并不知道该函数真正的作用的对象是什么类型，因为它可能是一个基类的对象也可能是一个派生类的对象。如果该函数是虚函数，则直到运行时彩会决定到底执行哪个版本的函数，判断的依据是引用或者指针所绑定的对象的真实类型。<br>
另一方面，对非虚函数的调用在编译时进行绑定。类似的，通过对象进行的函数(虚函数或者非虚函数)调用也在编译时绑定。对象的类型是确定不变的，我们无论如何都不可能令对象的动态类型与静态类型不一致。因此，通过对象进行的函数调用将在编译时绑定到该对象所属类中的函数版本上。<br>
**当且仅当对通过指针或引用调用虚函数时，才会在运行时解析该调用，也只有在这种情况下对象的动态类型才有可能与静态类型不同。**
# 虚函数
虚函数通过`virtual`关键字来声明。
{% codeblock lang: C++ %}
class CPerson{
    public:
        virtual vlid hello(){
            cout<<"I'm a person."<<endl;
        }
};
class CMan:public CPerson{
    public:
        //子类中不用声明virtual
        void hello(){
            cout<<"I'm a man."<<endl;
        }
};

int main(){
    CPerson *p = new CMan();
    p->hello();
    //I'm a man.
    return 0;
}
{% endcodeblock %}
上述代码中，通过基类指针调用虚函数时，子类的同名函数得到了执行。多态在C++中有三种形态：
1.通过基类指针调用基类和子类的同名函数时，会调用对象的实际类型中的虚函数。
2.通过基类引用调用基类和子类的同名函数时，会调用对象的实际类型中的虚函数。
3.基类或子类的成员函数中调用基类和子类的同名虚函数时，会调用对象的实际类型中的虚函数。

# 纯虚函数
我们可以在函数体的位置(即在声明语句的分号之前)书写`=0`就可以将一个虚函数说明为纯虚函数。其中`=0`只能出现在类内部的虚函数声明语句处。包含纯虚函数的类不允许实例化，称为**抽象类**。事实上纯虚函数提供了面向对象中接口的功能。当然，这样的接口是以继承的方式实现的。
{% codeblock lang: C++ %}
class CPerson{
    public:
        virtual void hello() = 0;
};

CPerson p; //compile error
{% endcodeblock %}
注意空方法、纯虚函数、方法声明的区别。类声明中的空方法给出了方法声明+方法定义。只声明但没有定义的方法将会产生链接错误，无论是否被调用过。
# 访问级别
虚函数的调用会在运行时动态匹配当前类型，然而成员函数的访问性检查是语法检查的一部分，在编译期完成。如果虚函数在父类中是`private`，即使在子类中是`public`，也不可以通过父类指针调用它：
{% codeblock lang: C++ %}
class CPerson{
    virtual void hello();
};
class CMan:public CPerson{
    public:
        virtual void hello();
};

CPerson *p = new CMan();
p->hello(); //编译出错
{% endcodeblock %}
# 虚析构函数
虚函数的机制使得我们可以通过更加通用的基类指针来操作对象。然而使用基类指针来`delete`对象则面临着问题。

{% codeblock lang: C++ %}
CPerson *p = new CMan();
delete p;
{% endcodeblock %}

上述代码只会回收`CMan`中`CPerson`部分所占用的内存，执行了`CPerson`的析构函数，却没有执行`CMan`的虚构函数。解决方法很直观，将析构函数设为`virtual`。
>构造函数不允许是虚函数，编译错。

{% codeblock lang: C++ %}
class CPerson{
    public:
        virtual ~CPerson(){};
};
class CMan:public CPerson{
    public:
        ~CMan(){};
};

CPerson *p = new CMan();
delete p;
{% endcodeblock %}
这样，`delete`时会先调用`~CMan()`再调用`~CPerson()`。
# 构造函数调用虚函数
当执行构造函数时，当前对象的类型为构造函数所属的类。所以在构造函数中调用虚函数和调用普通函数是一样的，不会动态联编，被调用的函数来自自己活着基类。
{% codeblock lang: C++ %}
class CPerson{
    public:
        virtual void hello(){
            cout<<"I'm a person."<<endl;
        }
        virtual void bye(){
            cout<<"Bye, person."<<endl;
        }
};
class CMan:public CPerson{
    public:
        CMan(){
            hello();
            bye();
        }
        void hello(){
            cout<<"I'm a man."<<endl;
        }
};
class CReek:public CMan{
    public:
        void hello(){
            cout<<"I'm a reek."<<endl;
        }
        void bye(){
            cout<<"Bye, reek."<<endl;
        }
};

int main(){
    CReek r;
    return 0;
}
{% endcodeblock %}
上述的调用结果是：
{% codeblock lang: C++ %}
I'm a man.
Bye, Person.
{% endcodeblock %}

`hello`和`bye`都是虚函数，其中`hello`三个层级都有定义，但被执行的是当前类`CMan`中的定义；`bye`在上下两个层级中有定义，被执行的是上一级`CPerson`中的定义。可见，构造函数执行时当前对象的类型是定义构造函数的类。  