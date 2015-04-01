# 关于 Objective-C 

Objective-C 是你在为 OS X 和 IOS 系统编写应用程序时使用的主要编程语言。它是C语言的超集并具备面对对象的能力和动态运行的特性。 Objective-C 继承了 C 语言的语法，基本类型和控制流语句并且添加了定义类和方法的语法。并且 Objective-C 语言在提供了动态类型和延迟到运行时的绑定的同时，为对象图形管理和对象字面提供了语言层面上的支持。

## 概览
本文介绍了 Objective-C 语言并且就其使用提供了广泛的例子。你将会学习到如何创造你自己描述自定义对象的类别以及如何使用由 Cocoa 和 Cocoa Touch 所提供的框架类。尽管框架类是与语言本身所分开的，但是他们的使用与处理和 Objective-C 语言紧密相关，并且许多其他语言层面的特点依靠这些类别所提供的行为。

## 一个应用程序由一个网络对象创建
在为 OS X 或者 IOS 系统创建应用程序时，你会将大把时间花费在使用对象上。那些对象是 Objective-C 类的实体。其中的一些是由 Cocoa 或者 Cocoa Touch 提供的而其他的一些是由你自己编写的。

如果你在编写你自己的类，开始的时候先提供一个对于类的描述，要细化预期的公开接口到类的实体。这个公共接口的公共特性就是来封装相关数据以及一系列的方法。方法声明表明一个对象可以接受到的信息，也包括任何时候方法被调用时所需要的参数的信息。你也会提供一个类的实现，包括实现在接口声明的每一个方法的代码。

相关章节：[类别定义](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW1)，[对象使用](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW1)，[数据封存](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW1)

## 类的继承

去定义一个类在已有类上增加自定义行为，而不是创造一个全新的类来提供微小的附加能力。你可以用一个类为任何类添加方法，包括那些你没有初始实现源代码的类，比如框架类 `NSString` 。

如果你的确有初始源代码，你可以使用一个类的继承来增加新的特性，或者修饰已有特性的属性。类扩展被广泛用于隐藏私人行为在一个单个源代码文件中使用，或者在一个自定义框架的私有实现中使用。

相关章节： [现有类的定制](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW1)

## 协议定义消息规范

在 Objective-C 的应用程序中大部分的工作是由对象之间互相传递信息形成的。通常，这些信息是由在一个类接口中明确声明的方法来定义的。然而某些时候定义非直接相关一个特定类的一系列方法是有用处的。

Objective-C 使用协议来定义一组相关方法，例如一个对象请求代理的方法，是可选还是需要的。任何类可以表明它采用了协议，也就是说它必须提供所需要的所有方法的实现。

相关章节：[使用协议](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithProtocols/WorkingwithProtocols.html#//apple_ref/doc/uid/TP40011210-CH11-SW1)

## 值和集合通常作为 Objective-C 的对象

在 Objective-C 中使用 Cocoa 和 Cocoa Touch 类来声明值是很常见的。 `NSString` 类用于字符串的字符，`NSNumber` 类用于不同类型的数字，例如整数或浮点数, `NSValue` 类用于等其他值，例如 C 中的结构。你也可以使用任何由 C 语言定义的初始类型，例如 `int` , `float` 或者 `char` 。

集合经常代表某一集合类的实体，例如 `NSArray` , `NSSet`, 或者 `NSDictionary` ，这其中的每一个都被用于集合其他 Objective-C 对象。

相关章节：[值和集合](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/FoundationTypesandCollections/FoundationTypesandCollections.html#//apple_ref/doc/uid/TP40011210-CH7-SW1)

## Blocks 简化常规任务
Blocks 是引进到 C ，Objective-C 和 C++ 语言的一种语言功能，来代表工作的一个单元；它们把一块代码以捕获的状态封锁，这就使它们在其他程序语言中类似于关闭状态。Blocks常常被用于简化常规任务,例如集合枚举，分类和测试。同样它们使用多线程优化技术（GCD）也使得并发或异步执行规划任务更加简单。

相关章节：[使用Blocks](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithBlocks/WorkingwithBlocks.html#//apple_ref/doc/uid/TP40011210-CH8-SW1)

## 错误对象在运行时错误问题中的使用
尽管 Objective-C 包括异常处理的语法，仅在程序错误（如界外数组访问）时使用的 Cocoa 和 Cocoa Touch 出现异常，这些都需要在一个应用程序运行前确定。

尽管其他的错误—包括运行时错误,例如硬盘空间不足或者网络功能不可用，都由 `NSError` 类来体现。你的应用程序要为错误做好计划并且确定如何做到最佳处理，以达到在问题出现时提供尽可能最佳的用户体验。

相关章节：[错误处理](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/ErrorHandling/ErrorHandling.html#//apple_ref/doc/uid/TP40011210-CH9-SW1)

## Objective-C 代码遵守已有的规则
写 Objective-C 代码的时候，你应该记住大量已有的代码规则。例如，方法名称以小写字母开始，用大小写混合的方式来区分多个单词，就像 `doSomething` 或者 `doSomethingElse` 而且，重要的不仅仅是要关注大写，你还应该确定你的代码尽可能的能被读懂。也就是说，方法名称要能够传词达意，而不是特别冗长。
除此以外，如果你想利用语言或者结构特征，你还需要注意一些规则。例如，属性访问方法必须严格遵守命名规则，以便能够和 Key-Value Coding(KVC) 或者 Key-Value Observing (KVO)这些技术共同使用。


相关章节：[规则](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Conventions/Conventions.html#//apple_ref/doc/uid/TP40011210-CH10-SW1)

## 先决条件
如果你对 OS X 或者 iOS 的发展还不熟悉，你可以在读这分文档前阅读 Start Developing Mac Apps Today 或者 [Start Developing Mac Apps Today](https://developer.apple.com/library/mac/referencelibrary/GettingStarted/RoadMapOSX/index.html#//apple_ref/doc/uid/TP40012262)，以便对 iOS 和 OS X 的应用发展过程有一个大概的了解。除此以外，在继续这篇文档大部分章节后面的练习前，先熟悉 Xcode 。 Xcode 是一种用来给 iOS 和 OS X 建立应用程序的集成开发环境。你将用它编写代码，设计应用程序的用户界面，测试你的应用软件，以及调试任何问题。
虽然有一些与 C 语言或者基于 C 语言的一种语言会更好，比如：Java 或者 C# ，这份文档包括基本 C 语言特征的内联例子，比如：流控制声明。如果你还掌握其他更高级的程序语言，比如 Ruby 或者 Python，你应该能够理解这些内容。
合理的通用性被用于一般面向对象编程原则，尤其是当它们用于 Objective-C 环境中。但是它假定你至少有与基本面向对象概念最小的相似点。如果你不熟悉这些概念，你应该读读 [Concepts in Objective-C Programming](https://developer.apple.com/library/mac/documentation/General/Conceptual/CocoaEncyclopedia/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010810) 中的相关章节。

## 相关文献
这份文档中的内容使用 Xcode 4.4 以及更高版本，并且假定你的目标是 OS X v10.7 及更高版本，或者 iOS 5 及更高版本。更多有关Xcode的信息，请关注 [Xcode Overview](https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/index.html#//apple_ref/doc/uid/TP40010215) 。有关语言特征可利用性，请关注 [Objective-C Feature Availability Index](https://developer.apple.com/library/mac/releasenotes/ObjectiveC/ObjCAvailabilityIndex/index.html#//apple_ref/doc/uid/TP40012243)。

Objective-C 应用程序使用引用计数来决定对象的使用寿命。大多情况下，编译程序的自动引用计数特征会为你注意这个问题。如果你不能利用 ARC ,或者需要转换或保留手动管理对象记忆的遗留代码，你应该阅读 [Advanced Memory Management Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)。


除了编译程序，Objective-C 语言使用一种 runtime system 来保证它的动态和面向对象特征。虽然你通常不需要担心 Objective-C 怎样“工作”，直接和这种 runtime system 互动是可以实现的。就像 [Objective-C Runtime Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048) 和 [Objective-C Runtime Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html#//apple_ref/doc/uid/TP40001418) 里描述的一样。
