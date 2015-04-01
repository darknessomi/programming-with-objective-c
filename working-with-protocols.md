# 使用协议 - Working with Protocols

在现实世界中,公务人员在处理某些情况时往往需要遵循严格的程序。例如,执法人员在进行询问或收集证据时要“遵循协议”。

在面向对象编程的世界,重要的是在一个给定的情况下能够定义一组对象的行为。举个例子,一个表视图为了查明它需要显示什么内容，希望能够与一个数据源对象通信。这意味着数据源必须响应表视图可能发送的一组特定的消息。

数据源可以是任何类的一个实例,比如视图控制器(子类 NSViewController OS X 或 UIViewController iOS)或者是一个刚从 NSObject 继承的专用的数据源类。为了使表视图知道一个对象是否为合适的数据源,重要的是能够声明对象实现的必要方法。

objective - c 允许您定义协议,声明的方法将被用于一个特定的情况。本章介绍了定义一个正式协议的语法,并解释了如何标记一个类界面使其符合一个协议,这意味着该类必须执行要求的方法。


## 协议定义消息传递合同

一个类的接口声明这个类的方法和属性。相比之下,一个协议声明的方法和属性,独立于任何特定的类。

定义一个协议的基本语法如下:

```
@protocol ProtocolName
// list of methods and properties
@end
```

协议可以包括声明实例方法和类方法,以及属性。

作为一个例子,考虑一个定制的视图类,用于显示一个饼图,如图 5-1 所示。

图 5-1 饼图自定义视图

![image](../images/piechartsim.png)

为了使视图尽可能重用,所有的决策信息应该留给另一个作为数据源的对象。这意味着相同的视图类的多个实例可以显示不同的信息仅仅通过与不同数据源的交流。

饼图视图所需的最小信息包括分块的数量,每个分块的相对大小,每个分块的标题。饼图的数据源协议,因此,看起来像这样:

```
@protocol XYZPieChartViewDataSource
- (NSUInteger)numberOfSegments;
- (CGFloat)sizeOfSegmentAtIndex:(NSUInteger)segmentIndex;
- (NSString *)titleForSegmentAtIndex:(NSUInteger)segmentIndex;
@end
```

**注:**本协议使用无符号整数的 NSUInteger 值标量值。这种类型在下一章详细讨论。饼图视图类接口需要有一个记录数据源对象的属性。这个对象可以是任何类,所以基本属性类型为 id 。关于对象唯一知道的是其符合相关协议。

声明数据源属性视图的语法应该像这样:

```
@interface XYZPieChartView : UIView
@property (weak) id <XYZPieChartViewDataSource> dataSource;
...
@end
```

objective - c 使用尖括号来表示与协议的一致性。本例为一个通用类对象指针声明一个弱属性符合 theXYZPieChartViewDataSource 协议。

**注:**接口和数据源属性通常标记为弱，根据前面提到的对象图管理原因,避免强引用周期。

指定与所需协议一致的属性,如果您试图将属性设置为一个对象, 这是不符合协议的，你会得到一个编译器警告，尽管基本属性类的类型是通用的。对象是否为 UIViewController orNSObject 的一个实例是无关紧要的。最重要的是,它符合协议,这意味着饼图视图知道它可以请求所需要的信息。


### 协议有可选择的方法

默认情况下,在一个协议中声明的所有方法都是需要的方法。这意味着符合协议的任何类必须实现这些方法。

在协议里指定可选方法也是可能的。这些方法是一个类只要需要就可执行的。

例如,您可能会决定,饼图的标题应该是可选的。如果数据源对象不实现 titleForSegmentAtIndex: ,则应该没有标题显示在视图中。

您可以使用 @optional 指令协议方法标记为可选,如下:

```
@protocol XYZPieChartViewDataSource
- (NSUInteger)numberOfSegments;
- (CGFloat)sizeOfSegmentAtIndex:(NSUInteger)segmentIndex;
@optional
- (NSString *)titleForSegmentAtIndex:(NSUInteger)segmentIndex;
@end
```

本例中,只有 titleForSegmentAtIndex: 方法标记为可选。前面的方法没有指令,所以被认为是必需的。

@optional 指令适用于遵循它的任何方法 ,直到协议定义的最后,或者遇到另一个指令之前,例如 @required。你可能会添加进一步的方法到协议中，如下:
```
@protocol XYZPieChartViewDataSource
- (NSUInteger)numberOfSegments;
- (CGFloat)sizeOfSegmentAtIndex:(NSUInteger)segmentIndex;
@optional
- (NSString *)titleForSegmentAtIndex:(NSUInteger)segmentIndex;
- (BOOL)shouldExplodeSegmentAtIndex:(NSUInteger)segmentIndex;
@required
- (UIColor *)colorForSegmentAtIndex:(NSUInteger)segmentIndex;
@end
```
这个例子定义了一个有三种必需方法和两种可选方法的协议。


### 运行时检查可选方法的实现

如果一个方法在协议中被标记为可选的,您必须检查是否有对象在实现之前试图调用它。

例如,饼图视图测试分块标题的方法可能是这样的:
```
  NSString *thisSegmentTitle;
      if ([self.dataSource respondsToSelector:@selector(titleForSegmentAtIndex:)]) {
          thisSegmentTitle = [self.dataSource titleForSegmentAtIndex:index];
      }
```
respondsToSelector:方法使用一个选择器,引用编译后的标识符的方法。您可以使用 @selector() 指令和指定方法的名称来提供正确的标识符。

如果在本例中数据源实现了这个方法,那么标题被使用;否则,标题仍然是零。

**切记:**本地对象变量自动初始化为零。

如果您试图调用有一个协议中 id 的 respondsToSelector: 方法,你会得到一个没有已知的实例方法的编译错误。一旦你有了一个协议中的 id ,所有静态类型检查复原,如果你试图调用任何未在指定协议中声明的方法，系统会报错。避免编译错误的一种方法是设置自定义协议采用 NSObject 协议。


### 协议继承其他协议

一个 Objective-C 类可以继承一个父类,以同样的方式，你还可以指定一个协议符合另一个协议。

作为一个例子,最佳的实践是定义您的协议符合 NSObject 协议(一些 NSObject 行为从它们的类接口划分到一个单独的协议; NSObject 类采用 NSObject 协议)。

通过表明自己的协议符合 NSObject 协议,这表明任何采用自定义协议的对象,还将提供每个 NSObject 协议方法的实现。因为你可能使用一些 NSObject 的子类,你不需要担心为这些 NSObject 提供自己的实现方法。不管怎样，对于前述的情况，采用的协议是有效的。

指定一个协议符合另一个协议,您需提供其他协议的名称在尖括号内,像这样:
```
@protocol MyProtocol <NSObject>
...
@end
```
在这个例子中,任何采用 MyProtocol 的对象,也有效地采用了 NSObject 协议中声明的所有方法。


## 符合协议

表示一个类采用了协议需再次使用尖括号括起来,像这样：
```
@interface MyClass : NSObject <MyProtocol>
...
@end
```
这意味着任何 MyClass 实例不仅响应在接口中特意声明的方法, MyClass 还在 MyProtocol 中提供了所需方法的实现。不需要在类的接口重新定义协议方法,采用协议就足够了。

**注:**编译器不会自动合成在采用的协议里声明的属性。

如果您需要一个类采用了多种协议,你可以用一个逗号分隔,像这样:
```
@interface MyClass : NSObject <MyProtocol, AnotherProtocol, YetAnotherProtocol>
...
@end
```
**提示:**如果您发现自己在一个类中采用大量的协议,它可能是一个信号,表明您需要重构那个过于复杂的类，可以通过必要的行为将其拆分到多个小类,每个小类都有明确的责任。  

对新 OS X 和 iOS 开发者来说，一个相对常见的困难是使用一个应用程序委托类去包含一个应用程序的大部分功能(管理底层数据结构,提供数据到多个用户界面元素,以及响应手势和其他用户交互)。随着复杂性的增加,类变得更难以维护。

一旦您表明遵循某个协议,类必须至少为每个所需的协议方法提供实现方法,以及您选择的任何可选的方法。如果不能实现任何所需的方法，编译器会提醒您。

**注:**协议中的方法声明类似其他任何声明。协议中实现的方法名和参数类型必须与声明匹配。


### Cocoa和Cocoa Touch定义大量的协议

Cocoa 和 Cocoa Touch 使用的协议针对各种不同情况的对象。例如,表视图类( NSTableView OS X and UITableView iOS )都使用一个数据源对象来为他们提供必要的信息。两者都定义自己的数据源协议,与上面的例子 XYZPieChartViewDataSource 协议使用差不多的方法。两者的表视图类还允许您设置一个委托对象,又必须符合相关 NSTableViewDelegate 或 UITableViewDelegate 协议。委托对象负责处理用户交互,或定制化显示某些条目。

一些协议是用于表示类之间没有相似之处。不是与特定类需求关联,一些协议与更普遍的 Cocoa 和 Cocoa Touch 通信机制关联,可能会采用多个不相关的类。

例如,许多框架模型对象(如集合类，如 NSArray 和 NSDictionary )支持 NSCoding 协议,这意味着它们可以编码和解码档案的属性或分布为原始数据。NSCoding 使它相对容易的将整个对象图写到磁盘中,提供每个对象采用协议的图表。

一些 Objective-C 语言级特性也依靠协议。为了使用快速枚举,例如,集合必须采用 NSFastEnumerationprotocol 协议,如 Fast Enumeration Makes It Easy to Enumerate a Collection 中所述。此外,一些对象可以被复制, 例如在使用一个属性和一个复制属性时，如 Copy Properties Maintain Their Own Copies 所述。你试图复制的任何对象必须采用 NSCopying 协议,否则你会得到一个运行异常。


## 协议用于匿名

对象的类不是已知的,或需要被隐藏的情况下协议也是很有用的。

比如,一个框架的开发人员可能会选择不发布框架内一个类的接口。因为类名称不清楚,框架的用户直接创建这个类的一个实例是不可能的。相反,框架内一些其他对象通常会指定返回一个现成的实例,像这样:
```
  utility = [frameworkObject anonymousUtility];
```
为了让这个 anonymousUtility 对象是有用的,框架的开发人员就可以发布一个协议,显示它的一些方法。但不提供原始类接口,这意味着类保持匿名,对象仍是在有限的方式内被使用:
```
  id <XYZFrameworkUtility> utility = [frameworkObject anonymousUtility];
```
如果您在写一个 iOS 应用程序,使用核心数据框架,例如,您可能会遇到 NSFetchedResultsController 类。这个类是为了帮助一个数据源对象提供存储数据到一个 iOS UITableView ,便于提供信息的行数。

如果您正在使用的表视图内容分为多个部分,您也可以访问一个获取结果控制器获取相关部分信息。而不是返回一个特定的类包含这部分信息, NSFetchedResultsController 类相反是返回一个匿名对象,它符合 NSFetchedResultsSectionInfo 协议。这意味着它仍然可以查询你需要的对象的信息,如一部分内的行数:
```
  NSInteger sectionNumber = ...
      id <NSFetchedResultsSectionInfo> sectionInfo =
              [self.fetchedResultsController.sections objectAtIndex:sectionNumber];
      NSInteger numberOfRowsInSection = [sectionInfo numberOfObjects];
```
即使您不知道 sectionInfo 对象的类, NSFetchedResultsSectionInfo 协议规定,它可以应对 thenumberOfObjects 信息。 





