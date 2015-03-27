#命名规则

使用框架类时，你会发现 Objective-C 代码非常易于阅读。它的类名和方法名比一般的 C 语言函数或 C 标准库要更加具有描述性。Camel Case 适用于带有多个单词的名称。在编写类的时候，你应当使用与 Cocoa 和 Cocoa Touch 相同的规则，这样可以使你的代码对于你和其他需要与你协同工作的 Objective-C 开发者来说更加易读，并且可以保证代码库的一致性。

此外，请严格遵循命名规则，保证 Objective-C 框架功能的各种机制的正常工作。例如，访问方法的名称必须遵循命名规则来命名才能在 Key-Value Coding（KVC）或 Key-Value Observing（KVO）机制下正常工作。

本章介绍了一些广泛运用于 Cocoa 及 Cocoa Touch 编码中的命名规则，也列举了需要在整个应用程序项目（包括使用到的框架类）中唯一命名的情况。

##一些名称必须在程序中唯一

创建一个新的类别、符号或是标识符时，你必须先考虑它的名称在所属区域中是否唯一。这个区域可能是整个应用程序（包括所涉及到的框架类）；也有可能仅仅是一个封装的类或是一个代码块。

###类名应在程序中唯一

不论是在项目中还是在项目所包含的框架或 bundle 中，Objective-C 中的类名必须是唯一的。例如，避免使用如 ViewController 或 TextParser 作为类名，因为这些名称有可能是包含在 App 中的一个框架名。

在命名类时使用前缀命名可以保证类名唯一。你会注意到 Cocoa 及 Cocoa Touch 的类名通常都以 NS 或 UI开头。像这样两个字符组成前缀的命名方式在 Apple 中专门为框架类而保留。随着你学习更多关于 Cocoa 及 Cocoa Touch 的知识，你会遇到一些其他的特定框架的前缀，如下表所示。

| Prefix       | Framework   |
| ------------- |:-------------:| -----:|
| NS      | Foundation (OS X and iOS) and Application Kit (OS X)  |
| UI      | UIKit (iOS)  |
| AB      | Address Book  |
| CA      | Core Animation  |
| CI      | Core Image  |

而你所创建的类的命名必须使用三个字符组成的前缀，可以是你的公司名和 App 名的结合，也可以是你的 App 中的某个组成部分。例如，如果你的公司叫做 Whispering Oak，你正在开发的一个游戏叫做 Zebra Surprise，那么你可以使用 WZS 或 WOZ 作为类名的前缀。

此外，你应该尽可能使用名词来命名你的类名，这样可以明确地显示这个类所代表的含义。例如下表中来自 Cocoa 及 Cocoa Touch中的例子。


| NSWindow       | CAAnimation   | NSWindowController | NSManagedObjectContext |
||
||

如果你需要使用多个单词来命名你的类，那么每个单词的首字母必须是大写。

###类中的方法名应具描述性且唯一

在保证了类名唯一后，同时这个类中所定义的方法名也必须是唯一的，但它可以与其他类中的某一个方法名相同。例如复写一个父类方法或是使用多态方法的情况。在不同类中具有相同功能的方法应当有相同的方法名、返回类型和参数类型。

方法名不需要包含前缀，且应当以小写字母开头，而对于多单词组成的名称，后续单词的首字母应该大写。下表为 NSString 类中的方法的命名。

| length      | characterAtIndex: | lengthOfBytesUsingEncoding: |
| ------------- |:-------------:| -----:|
||

如果一个方法中带有多个参数，那么方法名中必须提示每一个参数：

| substringFromIndex:      | writeToURL:atomically:encoding:error: | enumerateSubstringsInRange:options:usingBlock: |
| ------------- |:-------------:| -----:|
|  |


方法名的第一部分提示方法的主要作用或提示方法调用后的结果。例如，如果一个方法在调用后返回一个值，那么第一个单词应当表明该返回值的类型，如上文表格中的方法名 length，character...，substring...等等；如果你还需要在方法名中提示一些关于这个返回值的信息，那么应当使用多个单词来命名方法，如 NSString 类中的 mutableCopy 方法，capitalizedString 方法和 lastPathComponent 方法；如果一个方法用于执行某个操作，如写磁盘或列举目录，那么方法名的第一个单词必须提示这个操作，如 write... 方法或 enumerate... 方法。

如果方法中含有一个 error 指针参数来指示错误，那么应在其名称的最后加上 error 参数；如果方法中使用了 block，同样，为了使得能够应在方法名的最后加上 blcok 参数。

使用一个能够清楚表明方法的作用但又简洁明了的方法名非常重要，这不代表你需要取一个冗长的方法名，而同时方法名过于简洁明了也无法很好地表明一个方法的含义，因此最佳的方法是在这二者中找寻平衡点，如：

| | |
| ---:|---:|
|stringAfterFindingAndReplacingAllOccurrencesOfThisString:withThisString|Too verbose|
|strReplacingStr:str:|Too concise|
|stringByReplacingOccurrencesOfString:withString:|Just right|

除非在不同语言和文化中某个单词的缩写是众所周知的，否则你应当避免在方法名中使用缩写。这里提供了一个常见缩写的列表：[Acceptable Abbreviations and Acronyms](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/APIAbbreviations.html#//apple_ref/doc/uid/20001285)。

###在框架类的 Categories 中使用前缀命名方法
当使用 Category 来创建方法到一个已经存在的框架类时，你需要在方法名中使用前缀来避免冲突。具体可参考：[Avoid Category Method Name](Clashes。https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW4)。

##同一区域内局部变量名必须唯一

由于 Objective-C 是 C 语言扩展而来，因此 Objective-C 同样要遵守 C 语言中的变量命名规则。一个局部变量的变量名不允许与在相同范围内声明的其他变量名冲突。

```
(void)someMethod {
    int interestingNumber = 42;
    ...
    int interestingNumber = 44; // not allowed
}
```

尽管 C 语言允许一个局部变量的变量名与封装区域中的变量名相同。如下：

```
(void)someMethod {
    int interestingNumber = 42;
    ...
    for (NSNumber *eachNumber in array) {
        int interestingNumber = [eachNumber intValue]; // not advisable
        ...
    }
}
```

但这样会使得代码段变得混乱，不易于阅读，所以在编码中请尽量避免这种情况。
##方法名

与类的命名相同，除了考虑唯一性之外，方法的命名还应该遵循严格的命名规则。除了 Cocoa 和 Cocoa Touch 的要求外，这些规则是为了满足一些 Objective-C 的基础机制，如编译和运行。

###访问方法的名称
使用 @property 语法来声明对象的属性时（如 [Encapsulating Data](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW1)），编译器会自动合成 getter 和 setter 方法。如果你需要使用自己提供的访问方法，那么你需要保证一个每一个属性都使用了正确的方法名，这样方法才可以被语法点调用。

除个别特殊情况之外，一个 getter 方法必须使用与属性相同的名称。例如，一个属性名为 firstname，那么它的访问方法名称也应为 firstname。但 Boolean 属性是一个特例，因为 Boolean 属性的 getter 方法名以 is 开头。例如，一个属性名为 paused，那么它的 getter 方法名为 isPaused。

一个属性的 setter 方法命名应使用形如 setPropertyName: 的形式。譬如，一个属性名为 firstname，那么它的 setter 方法应为 setFirstName:；同样对于一个 Boolean 型的属性 paused 而言，相应的 setter 方法应为 setPaused:。

尽管使用 @property 语法能够方便地设定不同的访问方法名称（你只需要特殊考虑 Boolean 型的情况），但你还是需要注意下述的一些规则，以免如 Key Value Coding（getter 名为 valueForKey:，setter名为 setValue:forKey:） 这样的机制无法正常工作。了解更多有关 KVC 的信息，查阅 [Key-Value Coding Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueCoding/Articles/KeyValueCoding.html#//apple_ref/doc/uid/10000107i) 。

###创建对象的方法名

在之前的一些章节中介绍了许多不同创建一个类的实例的方法，你可以同时分配空间和初始化，例如：

```
NSMutableArray *array = [[NSMutableArray alloc] init];
```
或者你可以使用 new 方法来分配空间和初始化：

```
NSMutableArray *array = [NSMutableArray new];
```

一些类还提供了 class factory 方法：

```
NSMutableArray *array = [NSMutableArray array];
```

创建 class facotry 方法应以其所创建的类的名称开头（不使用前缀），以 NSArray 类为例，它的 factory 方法以 array 开头。而 NSMutableArray 类不定义任何它的类依赖 factory 方法，因此它创建一个可变 array 的 factory 方法仍然以 array 开头。

Objective-C 有许多不同的内存管理规则，这些规则让编译器能够保证对象有足够的的内存空间。编译器会根据创建方法的名称来判断遵循哪一条规则。因为 autorelease pool blocks 不同，由 factory 方法创建的对象与由传统的分配空间、初始化或 new 方法得来的对象在管理上会有细微的区别。学习更多有关 autorelease pool blocks 和一般的内存管理知识，查阅 [Advanced Memory Management Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i) 。
