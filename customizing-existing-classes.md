# 自定义现有的类 - Customizing Existing Classes

每一个对象都应该有明确的任务，如为具体信息建模，显示可视化内容或者控制信息流。
另外正如你所知道的，一个类的接口定义了其利用一个对象来帮助它完成任务的连接方式。

有时你会发现你希望通过添加某些方法来扩展现有的类，但这只在某些情况下是有用的。
举个例子，你发现你的应用程序经常需要在一个可视化界面显示一些字符串信息。
那么除了在每次你需要显示字符串时创造一个字符串绘图对象来使用外，给 NSString 类自己本身赋予可以在屏幕上绘制字符的能力会更有意义。

在这种情况下，对原始的、主要的类的接口增加功能方法并不总是可行的。
因为在大多数应用字符串对象的程序中，绘图能力并不总是被要求的。
例如，在 NSString 类中，你不能修改原来的接口或是继承，因为它是一个框架类。

此外，上述方法对现有类的子类也是没有意义的，因为你可能希望你的绘图能力不仅对原始的 NSString 类有效，也有对该类子类有效，如 NSMutableString 类。
另外虽然 NSString 类在 OS X 和 iOS 两个操作系统内均可使用，但相关绘图能力的代码在每个操作系统内是不同的，所以你需要在每个操作系统内使用不同的子类。

然而，Objective-C 允许你通过 categories 和类扩展来对已有的类中添加你自定义的方法。

## 使用 Categories 对现有的类添加方法

如果你需要添加方法到现有类，是为了添加某些功能使你自己的应用程序在完成某些人任务时更容易，那么使用 category 是最方便的方法。

使用 @ interface 关键字来声明一个 category ，就像标准的 Objective-C 类的描述一样，但并不表示这个 category 从任何一个子类继承。
另外它指定 category 的名称在括号内，像这样：

```
   @interface ClassName (CategoryName)
 
   @end
```

一个 category 可以声明任何类，即使是在没有原始代码的类（如标准的 Cocoa 或 Cocoa Touch 的类）。
你在 category 中声明的任何方法都可以被原始类和任何原始类的子类所实例化。
同时在运行时，你在 category 里添加的方法和由原始类实现的方法之间是没有区别的。

请考虑在之前章节提过的 XYZPerson 类，它具有一个人的姓氏和名字的属性。
如果你在写一个记录的应用程序，你会发现你经常需要显示一个按姓氏排列的名单，像这样：

```
Appleseed, John
Doe, Jane
Smith, Bob
Warwick, Kate
```

如果你不想在每次显示这个列表的时候再编写代码来生成适当的 lastName ， firstName 字符串，
那么你可以向 XYZPerson 类如下所示添加一个 category ：

```
#import "XYZPerson.h"
 
@interface XYZPerson (XYZPersonNameDisplayAdditions)
- (NSString *)lastNameFirstNameString;
@end

```

在此示例中，名为 XYZPersonNameDisplayAdditions 的 category 声明了一个额外的方法以返回必需的字符串。

一个 category 通常是在单独的头文件中声明的，并在单独的源代码文件被实现。
例如在 XYZPerson 类中，你可能会声明一个 category 在 XYZPerson+XYZPersonNameDisplayAdditions.h 的头文件中。

虽然用 category 添加的任何方法都可用于此类及其子类的所有实例中，但你仍需要在任何要使用添加的方法的源代码文件中导入含有 category 的头文件，否则你可能会遇到编译器警告和错误。

Category 的实现如下所示：

```
#import "XYZPerson+XYZPersonNameDisplayAdditions.h"
 
@implementation XYZPerson (XYZPersonNameDisplayAdditions)
- (NSString *)lastNameFirstNameString {
    return [NSString stringWithFormat:@"%@, %@", self.lastName, self.firstName];
}
@end
```

一旦你已经声明一个 category 并继承这些方法，你可以在此类的任何实例中使用这些方法，就好像他们是原始类接口的一部分一样：

```
#import "XYZPerson+XYZPersonNameDisplayAdditions.h"
@implementation SomeObject
- (void)someMethod {
    XYZPerson *person = [[XYZPerson alloc] initWithFirstName:@"John"
                                                    lastName:@"Doe"];
    XYZShoutingPerson *shoutingPerson =
                        [[XYZShoutingPerson alloc] initWithFirstName:@"Monica"
                                                            lastName:@"Robinson"];

    NSLog(@"The two people are %@ and %@",
         [person lastNameFirstNameString], [shoutingPerson lastNameFirstNameString]);
}
@end
```

除了可以向现有的类添加方法，你还可以使用 categories 把多功能的源代码文件中一个复杂的类拆分。
例如，你把一个自定义用户界面元素的绘图代码放在一个单独的 category 中，来执行一些其余的功能如几何计算、 颜色和渐变等，这就是一个特别复杂的类的例子。
另外你可以给类别的方法提供不同的实现，具体取决于你正在写一个 OS X 还是 iOS 的应用程序。
 
Categories 可用于声明类方法或成员方法，但并非通常适合声明附加属性。
在一个 category 的接口中包含属性声明时编译器不会报错，但是不能在一个 category 中声明一个附加的成员变量。
这意味着，编译器不会为该属性合成任何成员变量，也不合成任何属性访问方法。
在类的实现过程中，你可以编写你自己的访问方法，但是你不能来跟踪该属性的值，除非原始类中已有了该成员变量。

添加一个传统属性的唯一方式——也就是从现有类支持一个新的成员变量——是使用类扩展，如 [Class Extensions Extend the Internal Implementation]
(https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW3)。

<table>
<tr>
<td>
注： Cocoa 和 Cocoa Touch 包括了大量的主要框架类的 categories 。
</td>
<tr>
</tr>
<tr>
</tr>
<tr>
<td>
这一章的导言中提到的字符串绘图功能事实上已经由 OS X 中名为 NSStringDrawing 的 category 提供给 NSString 类了，其中包括 drawAtPoint:withAttributes: 和 drawInRect:withAttributes: 方法。
对于 iOS ，UIStringDrawing category 包括 drawAtPoint: withFont 方法和 drawInRect: withFont 方法。
</td>
</tr>
</table>


## 避免 categories 方法名冲突

因为在一个 category 中声明的方法已经添加到现有的类中，所以你需要非常小心有关方法名的定义问题。

如果在一个 category 中声明的方法和在原始类中的方法或该类（甚至是在一个父类）的其他 category 中的方法名称相同，在运行时，编译哪种方法的指令将被认为是未定义的。
如果你正在使用你自己的类的 categories ，使用的 categories 会将方法添加到标准的 Cocoa 或 Cocoa Touch 类时导致问题。

例如当你的应用程序与远程 web 服务交互时，可能需要一种使用 Base64 编码技术来编码字符串的方法。
因此你可以通过在 NSString 类上定义一个 category ，添加一个称为 base64EncodedString 的实例方法以返回一个 Base64 编码的字符串。

但是如果你链接到另一个框架，恰巧也在 NSString 类的自定义 category 中包括了此方法 也称为 base64EncodedString 时 ，那么将会出现问题。
在运行时，只有一个方法会“赢”，并添加到 NSString 类中，另一个则成为未定义不起作用。

如果你添加方法到 Cocoa 或 Cocoa Touch 类和之后版本的原始类中，那么可能会出现另一个问题。
例如 NSSortDescriptor 类，它描述了一个对象的集合应该是如何排序的，包含有 aninitWithKey: accending 初始化方法。
但并没有在早期的 OS X 和 iOS 版本下提供相应的工厂类方法。

按照约定，工厂类方法应该叫做 sortDescriptorWithKey: accending ，所以为方便起见你要选择添加一个 category 到 NSSortDescriptor 类上来提供此方法。
这是在旧版本的 OS X 和 iOS 下操作的，但随着 Mac OS X 10.6 版本和 iOS 4.0 的发布，一个叫 sortDescriptorWithKey 的方法添加到原始的 NSSortDescriptor 类中，意味着在这些或更高版本操作系统上运行你的应用程序时，你不再会有命名冲突的问题。

为了避免未定义的行为，最佳的做法是给框架类 categories 中的方法名添加一个前缀，就像你向你自己的类的名称添加一个前缀一样。
你可以选择使用和你自己的类的前缀相同的三个字母，但要小写以遵循方法命名的规则，然后在方法名称的其余部分之间用一个下划线连接。
对于 NSSortDescriptor 的示例，你的 category 应该看起来像这样：

```
@interface NSSortDescriptor (XYZAdditions)
 (id)xyz_sortDescriptorWithKey:(NSString *)key ascending:(BOOL)ascending;
@end
```

这意味着你可以肯定你的方法在运行时可以使用。歧义将会被删除，你的代码现在看起来像这样：

```
    NSSortDescriptor *descriptor =
               [NSSortDescriptor xyz_sortDescriptorWithKey:@"name" ascending:YES];
```

## 用 extension 来实现类的扩展

类扩展与 category 有相似性，但在编译时它只能被添加到已有源代码的一类中（该类扩展和该类同时被编译）。
声明一个类扩展的方法在原始类 @ implementation 块中，所以你不能，举个例子，在框架类上声明一个类扩展，如 Cocoa 或 Cocoa Touch 的 NSString 类。

用于声明类扩展的语法类似于一个 category 声明的语法，看起来像这样：

```
@interface ClassName ()
 
@end
```

因为没有在括号内给定名称，所以类扩展通常称为匿名类。

不像一般的 categories ，类扩展可以向类中添加其自己的属性和成员变量。如果你在类扩展中声明一个属性，要像这样：

```
@interface XYZPerson ()
@property NSObject *extraProperty;
@end
```

编译器会自动合成相关的访问方法，以及一个成员变量，继承到主要的类。

如果你在一个类扩展中添加任何方法，这些必须在主要类中继承。

也可以使用一个类扩展来添加自定义的成员变量。这些变量在类扩展接口中的大括号内声明：

```
@interface XYZPerson () {
    id _someCustomInstanceVariable;
}
...
@end
```

## 使用类扩展来隐藏私有信息

一个类的主要接口用于定义其他类将与之进行交互的方式。换句话说，它是类的公共部分。

类扩展通常用于扩展额外的私有方法或属性的公共接口以便在类本身的实现中使用。
例如，通常在界面中定义一个只读属性，但是为了在类的内部方法可以直接更改属性值，在继承上层的一个类扩展声明中定义该属性为读写属性。

举个例子，XYZPerson 类可以添加一个称为 uniqueIdentifier 的属性，用于跟踪信息，比如在美国的社会安全号码。

在现实世界中它通常需要大量的文书工作来给每一个人分配唯一的标识符，所以 XYZPerson 类接口可能会声明此属性为只读，并提供一些方法请求标识符分配，像这样：

```
@interface XYZPerson : NSObject
...
@property (readonly) NSString *uniqueIdentifier;
- (void)assignUniqueIdentifier;
@end
```

这意味着 uniqueIdentifier 不可能直接由另一个对象设置。
如果一个人还未有一个唯一的标识符，那么通过调用 assignUniqueIdentifier 方法将会作出分配一个标识符的请求。

为了 XYZPerson 类能够更改其内部的属性值，可以通过在类扩展中重新定义在顶层类继承的文件中被定义的属性值来实现：

```
@interface XYZPerson ()
@property (readwrite) NSString *uniqueIdentifier;
@end
 
@implementation XYZPerson
...
@end
```

<table>
<tr>
<td>
注： 读写属性是可选的，因为它是默认值。为清楚起见你可以在想使用它时重新声明属性。
</td>
</tr>
</table>

这意味着编译器现在将合成一个 setter 方法，所以在 XYZPerson 类执行内部的任何方法都能够直接使用 setter 方法或语法来设置该属性值。
通过为 XYZPerson 类继承的源代码文件声明类扩展， 使得 XYZPerson 类的信息是私有的。
如果另一种类型的对象试图设置该属性时，编译器将生成一个错误。

<table>
<tr>
<td>
注： 如上所示通过添加类扩展，重新定义 uniqueIdentifier 属性为读写属性，一个名为 setUniqueIdentifier: 的方法将在运行时在每个 XYZPerson 对象上存在，无论其他源代码文件是否知道该类扩展的存在。
</td>
</tr>
<tr>
<td>
当其他源代码文件中的某段代码试图调用一个私有方法或设置一个只读属性的值时，编译器会报错，但利用动态运行功能使用其他方式调用这些方法是可以避免编译器错误的，例如通过使用由 NSObject 类提供的 performSelector 的方法。
你应该避免出现一个类的层次结构或者仅在必须的时候使用；相反主类接口应始终定义正确的"公共接口"。
</td>
</tr>
<tr>
<td>
如果你打算在选择其他类别时，“私有”方法或属性仍是可用的，例如在一个框架内的相关类中。
你可以在单独的头文件中声明一个类扩展，并在需要它的源文件中导入它。在一个类中有两个头文件并不罕见，例如， XYZPerson.h 和 XYZPersonPrivate.h 等。
当你释放框架时，你只需释放公共的 XYZPerson.h 头文件即可。
</td>
</tr>
</table>

## 考虑其他办法来自定义类

Categories 和类扩展使得直接添加方法到一个现有的类变得很容易，但有时这并不是最好的选择。

面向对象编程的主要目标之一是编写可重用的代码，这意味着在各种情况下所有的类都尽可能地被重复使用。
如果你正在创建一个视图类来描述一个对象用于在屏幕上显示信息，那考虑一下这个类能在多种情况下可用是必要的。

除了将关于布局或内容的部分硬编码，一种可选择的方法是利用继承并将这些部分留在方法中，特别是子类重写的方法中。
虽然重用类并不会相对容易，因为每次你想要使用的那个原始的类时你仍然需要创建一个新的子类。

另一种选择是要对类使用一个 delegate  对象。
任何可能会限制可重用性的部分都可授权给另一个对象，也就是说可以在运行时编译这些部分。
一个常见的例子是标准表视图类 ( OS X 的 NSTableView 和 iOS 的 UITableView )。
为了使一般表格视图 （使用一个或多个列和行显示信息的对象）可用，它将内容部分留给另一个对象在运行时决定。
在下一章 [Working with Protocols] (https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithProtocols/WorkingwithProtocols.html#//apple_ref/doc/uid/TP40011210-CH11-SW1) 中会详细的介绍如何使用授权 。

## 直接与 Objective-C 运行库进行交互

Objective-C 通过其运行库系统提供动态功能。

许多决定并不在编译时作出，而在应用程序运行时决定，例如哪些方法调用时会发送消息的决定。
Objective- C 不仅仅是一种编译机器的语言代码，而且它还需要一个运行库系统来执行代码。

它是可以直接与运行库系统进行交互的，例如给对象添加关联引用。
不同于类扩展，关联引用不会影响原始类的声明和继承，这意味着你可以将它们用于你没有权限访问的原始源代码的框架类。

一个关联引用是用来链接两个对象的，类似于一个属性和成员变量。
获取更多的信息，请参阅关联引用部分。若要了解更多有关 Objective-C 的内容，请参阅 [Objective-C Runtime Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008048)。

## 练习

1. 添加一个 category 到 XYZPerson 类来声明和继承附加的功能，例如以不同的方式显示一个人的名字。

2. 向 NSString 类 添加一个 category，以添加一个方法来在给定位置绘制全部字母大写的字符串，通过调用到一个现有的 NSStringDrawing  category 方法来执行实际的绘制。
这些方法都记录在 iOS 的 NSString UIKit Additions Reference 中和 OSX 的 [NSString Application Kit Additions Reference](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ApplicationKit/Classes/NSString_AppKitAdditions/index.html#//apple_ref/doc/uid/TP40004121)中。

3. 将两个只读属性添加到原始 XYZPerson 类的继承中，来代表一个人的身高和体重，分别是 measureWeight 和 measureHeight 方法。

   使用类扩展重新声明属性为读写属性，并继承以便将属性设置为适当的值。

