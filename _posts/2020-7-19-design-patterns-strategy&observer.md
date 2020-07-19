---
layout: post
title: "面向对象设计模式——策略模式&观察者模式"
subtitle: "Strategy&Observer"
author: "Haye"
header-img: "img/post-bg-2020-07-19.jpg"
header-mask: 0.3
tags:
  - 设计模式
  - Observer
  - Strategy
  - 观察者模式
  - 策略模式
  - C++
---

### “组件协作”模式

现代软件专业分工之后的第一个结果是“框架与应用程序之间的划分”，“组件协作”通过**晚绑定**，实现框架与应用程序之间的松耦合，是二者协作时常用的模式。



### 策略模式Strategy

#### 动机

- 在软件构建过程中，**某些对象使用的算法可能多种多样**，经常改变，如果将这些算法都编码到对象中，会将这些对象变得异常复杂;而且**有时候支持不使用的算法也是一个性能负担。**（不必要的代码占用代码段）
- 如何在**运行时根据需要透明地更改对象的算法**？将算法与对象本身解耦，从而避免上述问题。

#### 例子

strategy1

```c++
enum TaxBase{
	CN_Tax;
    US_Tax;
    DE_Tax;
};
class SalesOrder{
    TaxBase tax;
public:
    double CalculateTax(){
        //...
        if(tax==CN_Tax){
            //CN
        }
        else if(tax==US_Tax){
            //US
        }
        else if(tax==DE_Tax){
            //DE
        }
        //...
    }
};
```

我们考虑代码的好坏的时候，应该从时间轴上去看问题，这里的枚举税收，暂时只有德国，中国和美国，可是随着时间的推进，业务的拓展，枚举里的内容越来越多，同时还要新增else if分支，这显然是违背了设计模式原则中的“开闭”原则。

>开放封闭原则（OCP）
>
>对扩展开放，对更改封闭。
>类模块应该是可扩展的，但是不可修改。

且这么多if-else分支，大部分情况下只会其中一个分支，而**其他的分支的算法往往不会被使用**。

那么应该怎么改进比较好呢？

考虑到开闭原则，加上要实现**晚绑定**，我们很容易想到构造一个Tax基类，里面做一个计算税收的纯虚函数，然后每次要计算一种具体的税收的时候，就继承Tax基类去实现计算税收的方法。这样就可以做到，只有当我们需要用的时候，才去实现它。



strategy2

```c++
class TaxStrategy(){
public:
    virtual double Calculate(const Context& context)=0;
    virtual ~TaxStrategy(){}
};
class CNTax:public TaxStrategy(){
public: 
    virtual double Calculate(const Context& context){
        //...
    }
};
class USTax:public TaxStrategy(){
public: 
    virtual double Calculate(const Context& context){
        //...
    }
};
class DETax:public TaxStrategy(){
public: 
    virtual double Calculate(const Context& context){
        //...
    }
};

class SalesOrder{
private:
    TaxStrategy *strategy;
public:
    SalesOrder(StrategyFactory* strategyFactory){
        this->stratrgy=strategyFactory->NewStrategy();//工厂模式
    }
    ~SalesOrder(){
        delete strategy;
    }
    double CalculateTax(){
        //...
        Context context;
        doublt val=strategy->Calculate(context);//多态调用
        //...
    }
}
```

这样就可以在运行时方便地根据需要在各个算法之间进行切换，不再需要冗长的if-else。

> if-else语句结构是一种面向过程的，应用分而治之的思维的产物。



#### 模式定义

定义一系列算法，把它们一个个封装起来，并且使它们可互相替换（**变化**）。该模式使得算法可**独立于**使用它们的客户程序（**稳定**）而变化（**拓展，子类化**）。用扩展的方式面对未来需求的变化。



#### 要点总结

- Strategy及其子类为组件**提供了一系列可重用的算法,**从而可以使得类型在**运行时**方便地根据需要在各个算法之间进行切换。
- Strategy模式提供了用条件判断语句以外的另一种选择,**消除条件判断语句,就是在解耦合**。含有许多条件判断语句的代码**通常都需要Strategy模式。**
- 如果Strategy对象没有实例变量,那么各个上下文可以共享同一个Strategy对象,**从而节省对象开销。**

>个人理解：strategy是一种针对条件判断语句（if-else、switch)的，应对未来可能的变化拓展所采取的一种模式。当然也不是所有的条件判断语句都需要策略模式，如果你能保证条件判断语句是稳定的 ，永远不变的，就也不需要策略模式。例如，性别的判断，月份的判断等。

------



### 观察者模式Observer

#### 动机

- 在软件构建过程中，我们需要为某些对象建立一种“通知依赖关系” ——一个对象（目标对象）的状态发生改变，所有的依赖对 象（观察者对象）都将得到通知。如果这样的**依赖关系过于紧密，将使软件不能很好地抵御变化。**
- 使用面向对象技术，可以将这种依赖关系弱化，并形成一种稳定的依赖关系。从而实现软件体系结构的松耦合。



#### 例子

给项目加一个进度条功能，即根据FileSplitter的文件分批功能的实现进度做到进度条值的更改。

test1

```c++
//MainForm1
class MainForm : public Form
{
	TextBox* txtFilePath;
	TextBox* txtFileNumber;
	ProgressBar* progressBar;

public:
	void Button1_Click(){

		string filePath = txtFilePath->getText();
		int number = atoi(txtFileNumber->getText().c_str());

		FileSplitter splitter(filePath, number, progressBar);

		splitter.split();

	}
};
```

```c++
//FileSplitter1
class FileSplitter
{
	string m_filePath; //文件路径
	int m_fileNumber;  //分割为的文件个数
	ProgressBar* m_progressBar; //具体通知控件

public:
	FileSplitter(const string& filePath, int fileNumber, ProgressBar* progressBar) :
		m_filePath(filePath), 
		m_fileNumber(fileNumber),
		m_progressBar(progressBar){

	}

	void split(){

		//1.读取大文件

		//2.分批次向小文件中写入
		for (int i = 0; i < m_fileNumber; i++){
			//...
			float progressValue = m_fileNumber;
			progressValue = (i + 1) / progressValue; //更新进度条
			m_progressBar->setValue(progressValue);
		}

	}
};
```

这个test违反了依赖倒置原则，**进度通知依赖一个具体控件（变化）**。而且我们要做的进度通知并不一定会是以进度条控件的形式展现~~，也有可能哪天产品经理一拍脑袋说我觉得还是数字显示比较好，就不要进度条了~~。这种依赖具体控件的做法会使得做更改很难，抑或是我想每次进度更新的时候不止是更新进度条还要做别的操作12345，那会使得这个FileSplitter越来越臃肿。

>依赖倒置原则（DIP）
>
>- 高层模块(稳定)不应该依赖于低层模块(变化)，二者都应该依赖于抽象(稳定) 。
>- 抽象(稳定)不应该依赖于实现细节(变化) ，实现细节应该依赖于抽象(稳定)。

test2，应用了观察者模式，甚至加了一个观察者。

```c++
//观察者1
class MainForm : public Form, public IProgress
{
	TextBox* txtFilePath;
	TextBox* txtFileNumber;

	ProgressBar* progressBar; //更改，加进度条展示

public:
	void Button1_Click(){

		string filePath = txtFilePath->getText();
		int number = atoi(txtFileNumber->getText().c_str());

		ConsoleNotifier cn;

		FileSplitter splitter(filePath, number);
		// 自己决定订阅通知
		splitter.addIProgress(this); //订阅通知
		splitter.addIProgress(&cn)； //订阅通知

		splitter.split();

		splitter.removeIProgress(this);

	}

	virtual void DoProgress(float value){
		progressBar->setValue(value);
	}
};
//观察者2
class ConsoleNotifier : public IProgress {
public:
	virtual void DoProgress(float value){
		cout << ".";
	}
};
```

```c++
class IProgress{ //通知，相当于observer
public:
	virtual void DoProgress(float value)=0;
	virtual ~IProgress(){}
};


class FileSplitter
{
	string m_filePath;
	int m_fileNumber;
	//ProgressBar* m_progressBar; //具体通知控件
	List<IProgress*>  m_iprogressList; // 抽象通知机制，支持多个观察者
	
public:
	FileSplitter(const string& filePath, int fileNumber) :
		m_filePath(filePath), 
		m_fileNumber(fileNumber){

	}


	void split(){

		//1.读取大文件

		//2.分批次向小文件中写入
		for (int i = 0; i < m_fileNumber; i++){
			//...

			float progressValue = m_fileNumber;
			progressValue = (i + 1) / progressValue;
			onProgress(progressValue);//发送通知
		}

	}

//稳定不变
	void addIProgress(IProgress* iprogress){
		m_iprogressList.push_back(iprogress);
	}

	void removeIProgress(IProgress* iprogress){
		m_iprogressList.remove(iprogress);
	}


protected:
	virtual void onProgress(float value){
		
		List<IProgress*>::iterator itor=m_iprogressList.begin();

		while (itor != m_iprogressList.end() ){
			(*itor)->DoProgress(value); //更新进度条
			itor++;
		}
	}
};
```

我个人感觉说，观察者模式，不如说是订阅报刊模式，就ConsoleNotifier和MainForm向FileSplitter订阅了进度通知，这样进度变更的时候，FileSplitter就会调用onProgress方法进行通知，onProgress通过遍历订阅列表里的订阅者IProgress*来挨家挨户DoProgress。



#### 模式定义

定义对象间的**一种一对多（变化）**的依赖关系，一遍当一个对象（Subject）的**状态发生改变**的时候，所有依赖它的对象都得到通知并自动更新。



#### 要点总结

- 使用面向对象的抽象，Observer模式使得我们可以**独立地改变目标与观察者**，从而使得二者之间的依赖关系达致松耦合。
- 目标发送通知时，无需指定观察者，通知（可以学带通知信息作为参数）会自动传播。
- **观察者自己决定是否需要订阅通知**，目标对象对此一无所知。
- Observer模式是基于事件的UI框架中非常常用的设计模式，也是MVC模式的一个重要组成部分。

> 个人理解：观察者模式的核心思路应该是：抽象的通知依赖关系~~，感觉List<IProgress*>有点消息队列的意思~~。即被观察者发生改变就发消息给它的观察者们去做相应的处理，很适合应用在UI交互，感觉这个模式的代码结构并不是很明显，更偏重于思路。本来想QT的信号槽其实也是观察者模式，但QT的信号只能被捕获一次，可能比较像职责链？这个模式学的有点似懂非懂，希望做项目重构的时候能思考一下怎么用。