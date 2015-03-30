#定义类 - Defining Classes 
当你为 OS X 或 iOS 系统编写软件时，你的大部分时间都花在了对象上。objective - c中的对象就像其他面向对象编程语言中的对象一样:它们把数据与相关的行为打包。  

一个应用程序构成了一个巨大的联通对象的生态系统，他们之间相互通信以解决具体问题，如显示一个可视化界面,响应用户的输入,或存储信息。 OS X 或 iOS 开发中,您不需要从头开始创建对象来解决每个问题； Cocoa (for OS X) 和 Cocoa Touch (for iOS) 为你提供一个包含可供你使用的现有对象库。 

其中一些对象是立即可用的,比如字符串和数字等基本数据类型,或像按钮和表视图这样的用户界面元素。一些是专为你以你需要的方式定制代码。软件开发过程涉及决定如何最好地定制和合并底层框架提供的对象和自己的对象,让你的应用具有其独特的特性和功能。  

在面向对象的编程术语中,对象类的实例。本章示范了如何在 objective – c 中通过声明一个用来描述你打算使用的类及其实例的接口来定义类。这个接口包含此类可以接收到的消息列表,所以你还需要提供类的实现,其中包含要执行的代码以响应每个消息。  

##类是对象的蓝图 - Classes Are Blueprints for Objects 
类描述了特定类型的对象的常见行为和属性。字符串对象(在 objective – c 中,这是类 NSString 的一个实例),该类提供了各种方法来检查和转换内部的字符。同样,此类过去常用于描述一个数字对象( NSNumber )提供围绕内部的数值,如将该值转换为不同数字类型的功能。 

同样的,多个由相同的蓝图构成的建筑在结构上是相同的,类的每个实例共享相同的属性和行为来作为该类的所有其他实例。每个 NSString 实例也一样,无论它的内部字符串如何。  

任何特定的对象是用于以特定的方式而设计的。你可能知道一个字符串对象表示某些字符的字符串，但你不需要知道确切的内部机制，用来存储这些字符。你不懂的内部行为对象本身用于直接处理的特点，但你要知道如何你预期的交互作用与对象，也许是为了索取特定字符或请求一个新的对象，在其中所有原始字符转换为大写。  

在 Objective-C 中，类接口指定一个给定的类型的对象是如何被其他对象使用的。换句话说，它定义了类的实例与外部世界之间的公共接口。

###可变性决定了一个代表值能否被取代 - Mutability Determines Whether a Represented Value Can Be Changed 
一些类定义对象是不可变的。这意味着当一个对象创建，并且随后不能由其他对象更改时，必须设置内部内容。在 Objective-C 中，所有基本的  NSString  和  NSNumber  对象都是不可变的。如果您需要代表一个不同的数字，则必须使用一个新的  NSNumber  实例。  

不可变的类还提供了一个可变的版本。如果您一定需要更改在运行时的字符串内容，例如他们在收到通过网络连接时填加字符，您可以使用 NSMutableString 类的一个例子。这个例子像 NSString 对象一样，除此之外他们还提供更改此对象表示的字符。  

虽然  NSString  和  NSMutableString  是不同类，但他们有许多相似之处。你并不需要从零开始的编写两个独立类，只是有时候碰巧有一些类似的行为要写两个完全独立的类，它完全可以利用现有的东西完成。  

###类的继承 - Classes Inherit from Other Classes
在自然的世界里，将动物分为像种、 属、族这样的各个门类。这些群体是分层的，许多的物种可能都属于同一属，许多的属可能同是一个族。  

举个例子，猩猩、狒狒和人类有明显的相似性。虽然他们都属于不同的物种，和甚至不同的属，部落和亚科，但是它们分类学相关，因为它们都属于同一个族 （称为"人科"），如图 1-1 所示。   

![图1-1  物种之间的分类关系]()  
在全世界的面向对象的编程中，对象则是也分成为各层次的组。对象被简单的分成许多类，并不会为了像 genus 、 species 这样不同的层次使用不一样的术语。同样的，人类作为人科家族成员继承着某些特征，那么就可以在从父母类功能性的传承中建立某一类。 

当一个类从另一个类有所继承时，新的类就会继承旧类的全部行为和属性。它也有机会来定义它自己的其他的行为和属性，或也可以重写继承来的行为属性。  

在 Objective-C 字符串类的情况下， NSMutableString 描述指定的类继承于 NSString ，如图 1-2 所示。所有由 NSString 提供的功能都是可用于 NSMutableString 的，如查询特定的字符或要求更改新的大写字符串，但 NSMutableString 添加了可以让您附加、 插入、 替换或删除子字符串和单个字符的方式。  

!图 1-2  NSMutableString 的继承
###The Root Class提供基本的功能 - The Root Class Provides Base Functionality
所有生物体都拥有着一些基本的" life "特点，有些功能对于 Objective-C 中的对象是很常见的。

当 Objective-C 对象需要使用另一个类的一个实例时，它需要其他类提供一定的基本特征和行为。为此， Objective-C 定义根类从中绝大多数的其他类继承，称为 NSObject 。当一个对象遇到另一个对象时，它将至少能够使用  NSObject  类描述所定义的基本行为进行交互。  

当您定义您自己的类时，你应该至少从 NSObject 中继承。一般情况下，你应该找一个提供你所需最接近的功能的 Cocoa or Cocoa Touch 对象，然后从其中继承。  

如果您想要在 iOS 应用程序中使用自定义的按钮，提供的 UIButton 类不能提供足够可自定义的属性以满足您的需要，从 NSObject 中创建一个新类比从 UIButton 中创建更有意义。如果你只是从 NSObject 简单的继承，你将需要由 UIButton 类定义的所有复杂的视觉交互和通信，这只是为了让你的行为方式按照用户的期望的按钮定义来实现。此外，通过从 UIButton 继承，你的子类会自动地获得任何未来的功能增强或可能应用于内部 UIButton 行为的 bug 修复。  

UIButton 类本身定义继承 UIControl ，描述了在 iOS 上所有用户界面控件的常见基本行为。反过来， UIControl 类继承 UIView ，给在屏幕显示的对象提供常用功能。UIView 继承于UIResponder，允许它响应用户输入，如水龙头、 手势。最后，也是最重要的，UIResponder 继承 NSObject ，图 1-3 所示。

!图 1-3  UIButton 类的继承

这一连串的继承是指 UIButton 任何自定义子类将不仅是继承声明了 UIButton 本身的功能，也依次从每个超类继承功能。你会最终以一个像按钮的对象结束，它可以在屏幕上自我显示、 对用户输入作出响应以及与所有基本的 Cocoa Touch 对象进行通信。 

对于你需要使用的类，要把它的继承链牢记于心，以便它可以把它所有能做的做好。类参考文档提供的 Cocoa and Cocoa Touch 允许从任何类可以轻松导航到每个超类。如果你在一个类接口或引用中找不到你需要的，它可能很好的在进一步的超类中定义了。  

##类接口定义预期交互 - The Interface for a Class Defines Expected Interactions
面向对象编程的诸多好处之一就是前面提到的想法 — —要使用一个类，那么你所需要知道的就是如何与它的实例进行交互。更具体地说，应该设计一个让对象隐藏其内部实现的细节。

如果你在 iOS 应用程序中使用标准的 UIButton ，你不需要担心像素在屏幕上如何操纵按钮来显示。你需要知道的就是您可以更改某些属性，比如按钮的标题和颜色，并当你将它添加到您可视化界面时表示信任，它将会以你期望的方式正确显示。

当您定义您自己的类时，您需要先搞清楚这些类的公共属性和行为。你想访问哪些公开属性？你应该让这些属性改变吗？其他对象与您的类的实例如何沟通？

此信息进入您的类的接口 — — 它定义了你打算与你的类的实例中的其他对象进行交互的方式。公共界面由您的类的内部行为进行独立描述，它组成了类。在 Objective-C 中，接口和安装通常放置在独立的文件中，这样你只需要使接口开放。

###基本语法 - Basic Syntax
在 Objective-C 中描述一个类接口的语法如下所示：
```
@interface SimpleClass : NSObject
 
@end
```
本例声明了一个从 NSObject 中 继承来的名为  SimpleClass 的类。

@interface  声明内部定义的公共属性和行为。在此示例中，没有指定属性或行为超出超类，所以唯一的可在 SimpleClass  上实现的实例预计功能是从 NSObject 继承来的。
###属性控制对对象值的访问 - Properties Control Access to an Object’s Values
对象通常具有属性供公众查阅。例如，如果您在一款记录保存软件中定义了一个类来表示人，那你可能需要一个决定字符串来表示一个人的姓和名的属性。

这些属性的声明应添加内部接口，像这样：
```
@interface Person : NSObject
 
@property NSString *firstName;
@property NSString *lastName;
 
@end

```
在此示例中，Person  类声明了两个公共属性，这两个属性是  NSString  类的实例。

这两个属性都是对 Objective-C  的对象而言的，所以他们使用星号以表明它们是  C  指针。他们也像在  C 语言中其他变量的声明语句一样，需要以分号结尾。

您可能会决定添加一个属性来表示一个人出生日期，以便可以按年龄给人分组，而不是只按名称进行排序。你可以对一个数字对象使用这样的属性：
```
@property NSNumber *yearOfBirth;
```
但这只是为了存储一个简单的数字值，可能算是矫枉过正。那么可以使用另一种由  C 语言提供替代方法，持一个整数标量值：
```
@property int yearOfBirth;
```
####属性的特性表明数据可存取性和存储的注意事项 - Property Attributes Indicate Data Accessibility and Storage Considerations

这个示例展示了到目前为止所有为了完善公共访问所声明的属性。这意味着其他对象可以同时读取和更改属性的值。

在某些情况下，您可能希望声明一个属性不能被改变。在现实世界中，一个人必须填写大量的文书工作来更改其记录的第一个或最后一个名称。如果你正在写官方记录的应用程序，您可以选择指定一个人名字的公共属性为只读，任何更改都需要通过中介对象来负责验证请求并选择通过或拒绝它。

Objective-C 属性声明可以包含属性的特性，用于指示是否一个属性应设置为只读模式。在官方记录应用程序中，人名的类接口可能如下所示：
```
@interface Person : NSObject
@property (readonly) NSString *firstName;
@property (readonly) NSString *lastName;
@end
```
属性的特性在括号内的  @property  关键字后指定，完整地在声明的公共属性公开的数据中描述。

###方法声明对象可接收消息 - Method Declarations Indicate the Messages an Object Can Receive

到目前为止的例子涉及到描述一个典型的模型对象或主要用于封装数据的对象的类。在  Person  类中，很可能有不需要能够访问这两个声明属性之外的任何功能。然而，大部分的类包括除了定义属性以外的行为。

既然 Objective-C  软件是由对象的大型网络所组成，标记那些能够通过发送信息相互作用的对象就非常重要了。在 Objective-C 术语中，一个对象发送消息到另一个对象是通过对该对象调用的方法实现的。

尽管语法有很大不同， Objective-C 在概念上还是类似于 C 和其他编程语言中的标准函数的。C 函数声明如下所示：
```
void SomeFunction();
```

与之相同的用 Objective-C 来定义如下所示：

```
- (void)someMethod;
```

在这种情况下，该方法不具有参数。Void 在声明之初写在小括号中来表示这个方式是不会再程序结束时返回任何值的。

在方法名称前面的减号 (-) 表示它是实例方法，可以在类的任何实例调用。这将它从类的方式中区别出来，可以调用类本身，[Objective-C Classes Are also Objects](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW18). 
中有所叙述。

正如 C 函数的原型，一个在 Objective-C 类的接口声明就像其他的  C 语句一样，需要分号终止。


#### Methods 可以使用参数 - Methods Can Take Parameters

如果您需要声明一个方法来带一个或多个参数，语法则是与一个典型的 C 函数非常不同的。
对于 C 函数的参数被指定在括号里，像这样：

```
void SomeFunction(SomeType value);
```

Objective-C 方式声明包括参数的名字，这里用到冒号，像这样：

```
- (void)someMethodWithValue:(SomeType)value;
```

正如返回类型，参数的类型被指定在括号内，就像标准的 C 类型转换一样。
如果您需要提供多个参数，语法又迥异于 C。将这些 C 函数参数依旧是指定在括号内，用逗号分开来彼此分开 ；在 Objective-C 中，方式声明像这样采取了两种参数： 

```
- (void)someMethodWithFirstValue:(SomeType)value1 secondValue:(AnotherType)value2;
```

在此示例中， value1  和  value2  是用在当方式被调用时实现访问值时的名字，它们就像变量。
一些编程语言允许函数用所谓的命名参数来定义 ；要特别注意这不是 Objective-C。
方式中的变量被调用的顺序必须与方式的声明相匹配，事实上  secondValue ： 方法声明的一部分是方法的名称：

```
someMethodWithFirstValue:secondValue:
```

这是一个有助于使 Objective-C 更加可读的语言，因为通过方法调用传递的值是指定的内联函数 ，旁边的方法名称的相关部分在[You Can Pass Objects for Method Parameters](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW13)
中有所描述。

>注： 上面使用的  value1  和  value2  值的名称不是严格意义的方法声明，这意味着它是不需要像你在执行中要使用完全相同值作为名称一部分。它唯一的要求是签名要相匹配，这意味着您必须保留该方法的参数名称，并保证返回类型完全相同。
下面的例子，这种方法具有相同的签名，就如上面所示：

```
- (void)someMethodWithFirstValue:(SomeType)info1 secondValue:(AnotherType)info2;

- (void)someMethodWithFirstValue:(SomeType)info1 anotherValue:(AnotherType)info2;
- (void)someMethodWithFirstValue:(SomeType)info1 secondValue:(YetAnotherType)info2;
```

###类的名称一定要与众不同 - Class Names Must Be Unique 

请特别注意在同一个应用程序中每个类的名称必须是唯一的，甚至跨越包括的库或框架也要这样。如果你试图用一个项目中的现有类相同的名称创建一个新类，您会收到一个编译器错误。

出于这个原因，也建议您使用三个或更多字母定义任何类的名称的前缀。这些字母可能涉及到您目前正在编写的应用程序，或者是可重复利用的代码框架名字。
在本文档的其余部分给出的所有例子都使用类名前缀，像这样：
```
@interface XYZPerson : NSObject
@property (readonly) NSString *firstName;
@property (readonly) NSString *lastName;
@end
```
历史注释： 如果你想知道为什么所以你遇到的许多类有一个  NS  前缀，其实因为它是过去的 Cocoa and Cocoa Touch 历史。Cocoa 开始生命的收集的框架，用于构建 NeXTStep 操作系统的应用程序。当苹果公司在 1996 年买下一回时下, 一步步骤大量被纳入OS X 上，包括现有的类名。介绍了可可触摸 iOS 等同于可可豆 ；一些类，可在可可粉和可可触摸，虽然也有大量的类独有的每个平台。

两个字母前缀像  NS  和  UI （对于在 iOS 用户的界面元素） 被苹果公司所使用。
与此相反的是，方法和属性的名称，只需在定义它们的类内唯一。虽然每个应用程序中的 C 函数必须具有唯一的名称，但在许多Object-C 中用相同名字定义类是完全可以接受的 （甚至是这样更好） 。然而，你不能在相同的类声明中不止一次的定义同一个方法，虽然你想要重写从父类继承的方法，但您必须使用原始声明中使用的确切名称。

作为与方法，对象的属性和实例变量 （[多数属性受配于实例变量](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW6)有所描述） 需要在他们定义的类中是唯一的。但是，如果要使用的全局变量，这些必须在应用程序或项目内唯一名称。
更多的命名规定和建议详见 [Conventions](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Conventions/Conventions.html#//apple_ref/doc/uid/TP40011210-CH10-SW1)。

##类的实现规定其内部的行为 - The Implementation of a Class Provides Its Internal Behavior

一旦你定义了一个类，其中包括的属性和方法都是供公众查阅的，您需要编写代码来实现的类行为。

如前所述，该接口的类通常放置在一个专用的文件中，通常被称为一个头文件，它一般都有文件扩展名 .h。写 Objective-C 类里面一个源代码文件以扩展名执行 . m。

每当在头文件中定义的接口时，你需要告诉编译器在试图编译的源代码文件中执行前请先阅读。为此，Objective-C  提供预处理器指令，#import。它类似于 C  #include 指令，但可以确保文件是只包括在编译过程中一次。
请注意预处理器指令有别于传统的 C 语句，不使用分号终止。

### 基本语法 - Basic Syntax

为一个类提供实现的基本语法如下所示：
```
#import "XYZPerson.h"
 
@implementation XYZPerson
 
@end
```
如果你声明一个类接口中的方式，您需要在这个文件内执行他们。

### 执行方法 - Implementing Methods

用这种方法，像这样的简单的类接口如下：
```
@interface XYZPerson : NSObject
- (void)sayHello;
@end
```
实现过程就像这样：
```
#import "XYZPerson.h"
 
@implementation XYZPerson
- (void)sayHello {
    NSLog(@"Hello, World!");
}
@end
```
本示例使用  NSLog() 函数将消息记录到控制台。它类似于标准的 C 库中的  printf ()  函数，并采用可变数目的参数，其中第一个必须是 Objective-C 字符串。
方法实现类似于 C 函数的定义，相关的代码必须被大括号所包在内。此外，方法的名称必须与它的原型和参数和返回类型完全匹配。
Objective-C 从C语言中继承属性区分大小写 ，所以此方法：
```
- (void)sayhello {
}
```
会被视为由编译器作为完全不同于前面所示的  sayHello  方法。
一般情况下，方法名称应以小写字母开头。Objective-C  要求使用比你可能看到用于C中的函数更具描述性的名称。如果方法名称涉及到多个单词，请使用棕色大小写 （每个新单词的首字母大写），使它们易于阅读。
请注意在 Objective-C 中那空白也是灵活的。习惯在代码中使用制表符或空格，或者各个块内每一行的缩进，你会经常看到左大括号位于单独的一行，像这样：
```
- (void)sayHello
{
    NSLog(@"Hello, World!");
}
```
Xcode，苹果公司的集成开发环境 (IDE) 用于创建  OS X  和  iOS  软件，它将基于一组自定义的用户首选项自动缩进代码。请参见更改缩进和制表符宽度Xcode Workspace Guide。
在下一章中你会看到更多例子的方法实现，[Working with Objects](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW1)。

##Objective-C类也是对象 - Objective-C Classes Are also Objects
在Objective-C 中，类本身不透明的类型被称为类的对象。类不能具有属性以使用上文所示的实例，但他们的声明语法定义可以接收消息。
类方法的典型用途是作为工厂模式，它替代对象分配和初始化步骤在对象创建中有所描述。例如，NSString  类，有各种各样的工厂方法可用于创建一个空的字符串对象或使用特定的字符，包括初始化的字符串对象：
```
+ (id)string;
+ (id)stringWithString:(NSString *)aString;
+ (id)stringWithFormat:(NSString *)format, …;
+ (id)stringWithContentsOfFile:(NSString *)path encoding:(NSStringEncoding)enc error:(NSError **)error;
+ (id)stringWithCString:(const char *)cString encoding:(NSStringEncoding)enc;
```

如这些示例中所示，类方法通过使用 + 表示，这让他们从使用实例方法的标志中区别开来。
类方法原型可能包含在类接口中，就像实例方法原型。类方法实现与  @implementation  块的类的实例方法方式相同。

##练习 - Exercises
>注： 为了跟着每章结尾给出的练习，您可能也希望创建  Xcode  项目。这将让您确保您的代码编译没有错误。
使用  Xcode  的新项目模板窗口从可用的  OS X  应用程序项目模板创建一个命令行工具。出现提示时，指定为基础的项目类型。

1.使用 Xcode 的新文件模板窗口创建名为的接口和实现文件。其中 XYZPerson 继承于 NSObject。  
2.向 XYZPerson 类接口添加属性为一个人的名字、 姓氏和 出生日期的类。（日期由 NSDate 类描述） 的出生日期。  
3.声明 sayHello 的方法，并像前文所述那样实现它。  
4.添加一个声明为类称为"person"的工厂模式。不会不要紧，读读下一章！  

>注意： 如果您要编译代码，你可能会由于此缺少的实现而出现 " Incomplete implementation " 警告。

