---
title: "面向过程、面向对象、面向切面"
date: 2021-03-16T20:00:00+08:00
draft: false
authors: ["zhyoch"]
tags: [Concept,Java]
featuredImagePreview: ""
summary: 关于面向过程OPP、面向对象OOP、面向切面AOP的简单理解。
---

{{< admonition type=quote title="原文地址" open=true >}}
本文**部分**转载自: 
1. [使用AOP](https://www.liaoxuefeng.com/wiki/1252599548343744/1266265125480448)
2. [理解OPP, OOP, AOP](https://www.cnblogs.com/minigrasshopper/p/10271758.html)
{{< /admonition >}}

## 概念

**面向过程编程OPP** (Procedure Oriented Programming) , 是一种以事物为中心的编程思想。主要关注“怎么做”, 即完成任务的具体细节。

**面向对象编程OOP** (Object Oriented Programming) , 是一种以对象为基础的编程思想。主要关注“谁来做”, 即完成任务的对象。

**面向切面编程AOP** (Aspect Oriented Programming) , 基于OOP延伸出来的编程思想。AOP把系统分解为不同的关注点, 或者称之为切面 (Aspect) 。主要实现的目的是针对业务处理过程中的切面进行提取, 它所面对的是处理过程中的某个步骤或阶段, 以获得逻辑过程中各部分之间低耦合性的隔离效果。

每种编程思想都有各自的优点, 它们适用在不同的情况下: 

面向过程性能很高, 面向对象比较易于管理和维护, 面向切面使软件变得更灵活。

> 新的编程范式, 并不一定完全各方面都优于旧的编程范式, 它们只是在某一特定领域或特殊场景下有着独到的优势。
>
> 编程范式只有适合不适合项目特性, 没有绝对的好坏。

## OPP和OOP

### 面向过程OPP

是最为实际的一种思考方式, 就算是面向对象的方法也是含有面向过程的思想。可以说面向过程是一种基础的方法。它考虑的是实际地实现。一般的面向过程是从上往下步步求精, 所以面向过程最重要的是模块化的思想方法。当程序规模不是很大时, 面向过程的方法还会体现出一种优势。因为程序的流程很清楚, 按着模块与函数的方法可以很好的组织。

### 面向对象OOP

是基于对象概念, 以对象为中心, 以类和继承为构造机制, 来认识、理解、刻画客观世界和设计、构建相应的软件系统。类和继承是是适应人们一般思维方式的描述范式。方法是允许作用于该类对象上的各种操作。这种对象、类、消息和方法的程序设计范式的基本点在于对象的封装性和类的继承性。通过封装能将对象的定义和对象的实现分开, 通过继承能体现类与类之间的关系, 以及由此带来的动态联编和实体的多态性。

### 例子

比如完成“吃饭”这个任务。

#### 面向过程的写法

需要封装一个`eat()`函数: 

- 如果是狗啃骨头, 则`eat(狗,骨头)`
- 如果是人吃饭菜, 则`eat(人,饭菜)`

eat是人和狗共用的吃饭进食本能。

那如果之后要处理猫吃鱼、鱼吃虾呢? eat函数中就会存在大量的if…else的判断, 这段代码, 无疑是不合适的。

#### 面向对象思想

我们发现人, 狗, 猫, 鱼等动物, 都有一个**吃**的共性。我们抽象出每个受体的类, 然后继承, 这样都具有“吃”的方法。

当我们想要执行狗啃骨头时, 那就`狗->eat(骨头)`, 这样, 我们从面向过程维护eat()的焦点, 转移到了面向对象维护角色的焦点上来。我们只需要维护好不同的角色 (类) 就好了, 并且狗的eat不会影响到猫的eat, 猫的eat也不会影响到人的eat。

所以, oop思想非常贴近软件工程高内聚的思想: 自己管好自己的东西, 自己做好自己的事情。

大多数支持面向对象的语言, 同时也支持面向过程, 它们都还无法完全面向对象, 因为面向过程是必然的, 面向过程代表着必要的程序流程, 调动对象进行组合或对象内部能力的实现, 都一定会存在“过程”, 它最终还是需要通过拆分步骤来指导最具体的执行细节。

在此, 我们也能得到一些感悟, 许多事情并非完全非黑即白, 非OOP就必然是OPP, 特别是思想层面的东西, 它们呈现出互相结合的形态, 从OPP到OOP, 这是一个思想进步的过程。 

## AOP

面向切面编程主要实现的目的是针对业务处理过程中的切面进行提取, 它所面对的是处理过程中的某个步骤或阶段, 以获得逻辑过程中各部分之间低耦合性的隔离效果。

那么, AOP如何体现? 

这里可以联想一下Laravel的中间件、JavaWeb的拦截器、Vue.js的Decorator等等, 它们都是AOP思想的实践。装饰器模式、代理模式, 它们也是基于AOP思想的设计模式。

AOP思想, 指导我们通过找到平整切面的形式, 插入新的代码, 使新插入的代码对切面上下原有流程的伤害降到最低。

### 例子

#### Laravel中间件

我们拿Laravel中间件做什么? 

权限、日志、请求过滤、请求频率限制、csrf过滤……

我们知道, 中间件对于controller的业务逻辑, 不会有任何伤害。

如果没有这个切面, 我们想要记录请求日志, 可能需要在每个controller的具体方法中写日志记录的代码, 或者调用日志记录的函数、方法。

这会使一段记录日志的代码, 或调用记录日志的调用语句出现在许多controller中, 这与controller原本要关注的逻辑无关, 使controller职责不单一, 提高维护成本。

当然, 我们可能会写一个父类, 让许多controller来继承这个父类, 然后统一在父类的__construct方法中记录日志, 以此来解决耦合问题。

但实际上, 这个父类的construct方法, 不正是一个切面吗? 它在原有流程中截取了一个切面, 在切面中植入代码, 以达到承上启下的作用, 并且不对上下文产生伤害。

{{< admonition type=tip title="得到一个思考" open=true >}}
AOP指导我们寻找切面, 但找到合适的切面, 也尤为重要。

就像上文, 父类构造函数的切面和中间件的切面比起来, 显然中间件这个切面更利于维护, 你可以灵活选择中间件, 但你无法灵活选择父类, 因为决定你的controller继承什么父类的, 不是切面中的代码, 而是controller本身处理什么逻辑。
{{< /admonition >}}

许多项目, OPP、OOP、AOP是同时存在的, 它们是编程范式, 是一种指导编程的思想, 并非不能互相配合。

#### Spring中的AOP

在Java平台上, 对于AOP的织入, 有3种方式: 

1. 编译期: 在编译时, 由编译器把切面调用编译进字节码, 这种方式需要定义新的关键字并扩展编译器, AspectJ就扩展了Java编译器, 使用关键字aspect来实现织入。
2. 类加载器: 在目标类被装载到JVM时, 通过一个特殊的类加载器, 对目标类的字节码重新“增强”。
3. 运行期: 目标对象和切面都是普通Java类, 通过JVM的动态代理功能或者第三方库实现运行期动态织入。

最简单的方式是第三种, Spring的AOP实现就是基于JVM的动态代理。由于JVM的动态代理要求必须实现接口, 如果一个普通类没有业务接口, 就需要通过CGLIB或者Javassist这些第三方库实现。

AOP技术看上去比较神秘, 但实际上, 它本质就是一个**动态代理**, 让我们把一些常用功能如权限检查、日志、事务等, 从每个业务方法中剥离出来。

需要特别指出的是, AOP对于解决特定问题, 例如事务管理非常有用, 这是因为分散在各处的事务代码几乎是完全相同的, 并且它们需要的参数 (JDBC的Connection) 也是固定的。另一些特定问题, 如日志, 就不那么容易实现, 因为日志虽然简单, 但打印日志的时候, 经常需要捕获局部变量, 如果使用AOP实现日志, 我们只能输出固定格式的日志, 因此, 使用AOP时, 必须适合特定的场景。
