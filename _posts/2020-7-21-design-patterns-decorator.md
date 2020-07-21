---
layout: post
title: "面向对象设计模式——装饰模式"
subtitle: "Decorator"
author: "Haye"
header-img: "img/post-bg-2020-07-19.jpg"
header-mask: 0.3
tags:
  - 设计模式
  - Decorator
  - c++
---

### “单一职责”模式

在软件组件的设计中，如果**责任划分的不清晰**，使用继承得到的结果往往是随着需求的变化，**子类急剧膨胀，**同时充斥着重复的代码，这个时候的关键是划分责任。

典型模式，责任上表现突出

- Decorator
- Bridge



### 装饰模式Decorator

#### 动机

- 在某些情况下我们可能会“**过度地使用继承来扩展对象的功能**”，由于继承为类型引入的**静态特质**，使得这种扩展方式缺乏灵活性； 并且随着子类的增多（扩展功能的增多），**各种子类的组合（扩展功能的组合）会导致更多子类的膨胀。**
- 如何使“对象功能的扩展”能够根据需要来动态地实现？同时避免“扩展功能的增多”带来的子类膨胀问题？从而使得任何“功能扩展变化”所导致的影响将为最低？

#### 例子

test1

```c++
//业务操作
class Stream{
public：
    virtual char Read(int number)=0;
    virtual void Seek(int position)=0;
    virtual void Write(char data)=0;
    
    virtual ~Stream(){}
};

//主体类
class FileStream: public Stream{
public:
    virtual char Read(int number){
        //读文件流
    }
    virtual void Seek(int position){
        //定位文件流
    }
    virtual void Write(char data){
        //写文件流
    }

};

class NetworkStream :public Stream{
public:
    virtual char Read(int number){
        //读网络流
    }
    virtual void Seek(int position){
        //定位网络流
    }
    virtual void Write(char data){
        //写网络流
    }
    
};

class MemoryStream :public Stream{
public:
    virtual char Read(int number){
        //读内存流
    }
    virtual void Seek(int position){
        //定位内存流
    }
    virtual void Write(char data){
        //写内存流
    }
    
};

//扩展操作
class CryptoFileStream :public FileStream{
public:
    virtual char Read(int number){
       
        //额外的加密操作...
        FileStream::Read(number);//读文件流，静态特质
        
    }
    virtual void Seek(int position){
        //额外的加密操作...
        FileStream::Seek(position);//定位文件流
        //额外的加密操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        FileStream::Write(data);//写文件流
        //额外的加密操作...
    }
};

class CryptoNetworkStream : :public NetworkStream{
public:
    virtual char Read(int number){
        
        //额外的加密操作...
        NetworkStream::Read(number);//读网络流
    }
    virtual void Seek(int position){
        //额外的加密操作...
        NetworkStream::Seek(position);//定位网络流
        //额外的加密操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        NetworkStream::Write(data);//写网络流
        //额外的加密操作...
    }
};

class CryptoMemoryStream : public MemoryStream{
public:
    virtual char Read(int number){
        
        //额外的加密操作...
        MemoryStream::Read(number);//读内存流
    }
    virtual void Seek(int position){
        //额外的加密操作...
        MemoryStream::Seek(position);//定位内存流
        //额外的加密操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        MemoryStream::Write(data);//写内存流
        //额外的加密操作...
    }
};

class BufferedFileStream : public FileStream{
    //...
};

class BufferedNetworkStream : public NetworkStream{
    //...
};

class BufferedMemoryStream : public MemoryStream{
    //...
}




class CryptoBufferedFileStream :public FileStream{
public:
    virtual char Read(int number){
        
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Read(number);//读文件流
    }
    virtual void Seek(int position){
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Seek(position);//定位文件流
        //额外的加密操作...
        //额外的缓冲操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        //额外的缓冲操作...
        FileStream::Write(data);//写文件流
        //额外的加密操作...
        //额外的缓冲操作...
    }
};
void Process(){

        //编译时装配
    CryptoFileStream *fs1 = new CryptoFileStream();

    BufferedFileStream *fs2 = new BufferedFileStream();

    CryptoBufferedFileStream *fs3 =new CryptoBufferedFileStream();

}
```

这个的缺陷我觉得不用说就很明显了，尼玛写了这么多类最后只用了最后的几个？？？然后一直重复使用同一段写文件/读文件之类的代码。

这时候又要引出设计模式原则：

>优先使用对象组合，而不是类继承
>
>- 类继承通常为“白箱复用”，对象组合通常为“黑箱复用” 。
>- 继承在某种程度上破坏了封装性，子类父类耦合度高。
>- 而对象组合则只要求被组合的对象具有良好定义的接口，耦合度低。

于是我们不再继承FileStream,而是将它们变成成员。

test2

```c++
//扩展操作
// 三个子类变为一个子类，用组合代替继承
//继承Stream为了接口的规范，定义了基类的接口规范
class CryptoStream: public Stream {    
    Stream* stream;//编译时确定变为运行时确定
public:
    CryptoStream(Stream* stm):stream(stm){//构造器
   
    }    
    
    virtual char Read(int number){
       
        //额外的加密操作...
        stream->Read(number);//读文件流
    }
    virtual void Seek(int position){
        //额外的加密操作...
        stream->Seek(position);//定位文件流
        //额外的加密操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        stream->Write(data);//写文件流
        //额外的加密操作...
    }
};

class BufferedStream : public Stream{
    
    Stream* stream;//...
    
public:
    BufferedStream(Stream* stm):stream(stm){
        
    }
    //...
};

void Process(){

    //运行时装配
    FileStream* s1=new FileStream();
    CryptoStream* s2=new CryptoStream(s1);//加密
    
    BufferedStream* s3=new BufferedStream(s1);//缓存
    
    BufferedStream* s4=new BufferedStream(s2);//既加密又缓存
       
}
```

同时我们发现，不论是文件流，内存流还是网络流都是Stream的子类，于是我们很容易想到**多态**。于是我们把三类合一CryptoStream，同时让他继承Stream，重写read()、seek()和write()方法。

*~~据说Java的IO流就是用的这个模式。~~*

还有没有更好的做法呢？当然有，我们发现衍生功能的加密类和缓存类都有Stream对象指针，那么我们是否可以把它提取出来？问题是提取到哪里呢？Stream基类里吗？但是不管是基类还是FileStream等子类都没有需要用到Stream指针对象的情况，所以显然是不合适的，于是我们在FileStream等子类和CryptoStream这种功能类之间再加一个继承基类的DecoratorStream类作为中间层。

于是CryptoStream类就变成了这样：

test3

```c++
//扩展操作
//写一个中间类
DecoratorStream: public Stream{
protected:
    Stream* stream;//精髓所在
    
    DecoratorStream(Stream * stm):stream(stm){
    
    }
    
};

class CryptoStream: public DecoratorStream {
 

public:
    CryptoStream(Stream* stm):DecoratorStream(stm){
    
    }
    
    
    virtual char Read(int number){
       
        //额外的加密操作...
        stream->Read(number);//读文件流
    }
    virtual void Seek(int position){
        //额外的加密操作...
        stream::Seek(position);//定位文件流
        //额外的加密操作...
    }
    virtual void Write(byte data){
        //额外的加密操作...
        stream::Write(data);//写文件流
        //额外的加密操作...
    }
};
```

我们把test1中**FileStream::Read(number)**这段代码称为有**静态特质**，而**stream->Read(number)**则具有动态特质，前者由**继承**实现，后者由**组合**实现。

test2**用组合代替继承**，运行时编译；test3将共同的部分提出，子类大大减少。



#### 模式定义

**动态（组合）地给一个对象增加一些额外的职责**。就增加功能而言，Decorator模式比生成子类（继承）更为灵活（消除重复代码&减少子类的个数）。

#### 要点总结

- 通过采用组合而非继承的手法，Decorator模式实现了在**运行时**动态扩展对象功能的能力，而且可以根据需要扩展多个功能。避免了使用继承带来“灵活性差”和“多子类衍生问题”。
- decorator类在接口上表现为is a Component的**继承**关系，即Decorator类**继承了Component类所具有的接口**。但在实现上又表现为has a Component的**组合**关系，即Decorate类**又使用了另外一个Component类**。
- Decorator模式的目的并非解决“多子类衍生的多继承”问题，**Decorator模式应用的要点在于解决“主体类在多个方向上的扩展功能”**——是为“装饰”的含义。

> 个人理解：Decorator模式解决功能类的扩展性问题，以及实现功能类和主体类的分开分离操作，表现上多为继承一个基类然后又有基类的成员指针对象。