# 赋值与集合 - Values and Collections

尽管 Objective-C 是一种面向对象的编程语言，它也是C语言的加强版，这意味着你可以在 Objective-C 中使用标准 C 中任意的纯量类型（非对象的），例如 int，float，char。在 Cocoa 和 Cocoa Touch 应用中，你还可以使用一些额外的类型，例如 NSInteger，NSUInteger 和 CGFloat，在不同的系统结构中，他们的定义方式也不同。

纯量类型用于当你不需要用一个对象来表示值的时候。字符型经常作为 NSString 的实例而使用，数值被存储在纯量类型的局部变量或属性中。

你可以在 Objective-C 中定义与 C 语言类似的数组，但是在 Cocoa 和 Cocoa Touch 应用中，集合被用于实例化像 NSArray 或 NSDictionary 这样的类。这些类只能存放对象，这意味着在添加对象到集合之前，你就要用 NSValue, NSNumber 或 NSString 这样的类为对象赋好值。

在前面的章节中，我们多次使用了 NSString 类、它的初始化、方法函数库，@"string" 字符为创建 NSString 的实例提供了简单的语法。在本章中，我们会通过方法调用和赋值语句来示范如何使用 NSValue 和 NSNumber 类。


## 你可以使用C中的基本数据类型

在 Objective-C 中，C 的每一个 scalar 变量类型都是可以使用的：

```

    int someInteger = 42;
    float someFloatingPointNumber = 3.1415;
    double someDoublePrecisionFloatingPointNumber = 6.02214199e23;

```

还有 C 的操作符也都可用：

```

    int someInteger = 42;
    someInteger++;            // someInteger == 43
 
    int anotherInteger = 64;
    anotherInteger--;         // anotherInteger == 63
 
    anotherInteger *= 2;      // anotherInteger == 126

```

如果你要为 Objective-C 属性使用纯量类型 ，请这样做：

```

    @interface XYZCalculator : NSObject
    @property double currentValue;
    @end

```

你也可以对属性使用 C 操作符，用点语法进行赋值操作，就像这样：

```

    @implementation XYZCalculator
    - (void)increment {
        self.currentValue++;
    }
    - (void)decrement {
    self.currentValue--;
    }
    - (void)multiplyBy:(double)factor {
    self.currentValue *= factor;
    }
    @end

```

点语法是一种纯粹用于存取器（accessor）调用方法的语法，所以这个例子中的每一条操作都是先使用 get accessor 方法获取实例变量值，运行程序后，再使用 set accessor 方法存储实例变量值作为结果。


### Objective-C中其他的基本类型

BOOL 类型在 Objective-C 中用来表示布尔值：yes 和 no。正如你想的那样，yes 在逻辑上等于 true 和 1，no 等于 false 和 0；

在 Cocoa 和 Cocoa Touch 中，许多方法的参数也可以使用特殊的数据类型，例如 NSInteger 和 CGFloat。

例如， NSTableViewDataSource 和 UITableViewDataSource 协议（在之前章节提到的）都有令数据按行显示的方法：

```

    @protocol NSTableViewDataSource <NSObject>
    - (NSInteger)numberOfRowsInTableView:(NSTableView *)tableView;
    ...
    @end

```

像 NSInteger 和 NSUInteger 这样的类型，在不同的系统结构中有不同的定义方式。当为 32 位运行环境（例如 IOS）编程时，他们分别是 32 位的有符号整数和无符号整数；当为 64 位运行环境（例如 modern OS X runtime）编程时，他们分别是 64 位的有符号整数和无符号整数。

如果你希望在 API 边界间（包括内外部 API）传值，最好使用这种特定平台的类型，例如在你的应用代码和平台架构间，利用方法、函数调用传递参数或返回数值。

对于局部变量，例如循环中的计数值，如果你知道该值作用域的限制，使用 C 的基本类型定义它也是可以的。

### C的结构可以存储基本数据

一些 Cocoa and Cocoa Touch API 使用 C 的数据结构存储数值。例如，一个字符串对象可以作为一个子字符串的取值域，就像这样：

```

    NSString *mainString = @"This is a long string";

    NSRange substringRange = [mainString rangeOfString:@"long"];

```

一个 NSRange 结构保存了一个地址和一个长度值。在这个例子中，substringRange 中会保存 {10,4} 这两个值：以 0 开始计算的位置，主字符串中的第 10 个字母为子字符串 @"long" 的第 1 个字母，@"long" 的长度为 4。

相似的是，如果你要编写自定义图形代码，你需要使用 Quartz，它要使用以 CGFloat 数据类型为基础的结构，就像 OS X 上的 NSPoint 和 NSSize ，IOS 上的 CGPoint 和 CGSize。CGFloat 也同样在不同的系统结构上有不同的定义方式。

想看更多关于 Quartz 2D 绘制机制？点击[Quartz 2D Programming Guide](https://developer.apple.com/library/mac/documentation/GraphicsImaging/Conceptual/drawingwithquartz2d/Introduction/Introduction.html#//apple_ref/doc/uid/TP30001066)


## 对象可以表示基本数值

如果你需要用 scalar 表示一个对象的值，你可以使用 Cocoa 和 Cocoa Touch 提供的基础赋值类，例如我们在下一个部分里面提到的集合类。

### 通过NSString类的实例表示Strings

就像之前章节里面提到的，NSString 是用来表示字符串的，例如 Hello World。创建 NSString 对象有很多种方式，包括分配空间并初始化，使用工厂方法或者纯语法：

```

    NSString *firstString = [[NSString alloc] initWithCString:"Hello World!"

    encoding:NSUTF8StringEncoding];

    NSString *secondString = [NSString stringWithCString:"Hello World!"
    
    encoding:NSUTF8StringEncoding];

    NSString *thirdString = @"Hello World!";

```

每一个例子都高效的完成了同一件事：创建了一个字符串对象，其值为给定内容。

基本 NSString 类是不可变的，它的内容在创建时设好就不能再改变了。如果你想要一个不同的字符串，你必须创建一个新的对象，就像这样：

```

    NSString *name = @"John";

    name = [name stringByAppendingString:@"ny"]; // returns a new string object

```

NSMutableString 类是 NSString 的可变子类，允许在方法中修改，与 appendString 和 appendFormat 相似：

```

    NSMutableString *name = [NSMutableString stringWithString:@"John"];

    [name appendString:@"ny"];   // same object, but now represents "Johnny"

```

### 使用Format Strings创建来自其他对象和值的Strings

如果你需要创建一个带有变量的字符串，你要使用 format string。它允许你使用格式符来表示值是如何插入的：

```

    int magicNumber = ...
    NSString *magicString = [NSString stringWithFormat:@"The magic number is %i", magicNumber];

```

可用的格式符在这里[String Format Specifiers](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html#//apple_ref/doc/uid/TP40004265)。想了解更多关于strings，请点击[String Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Strings/introStrings.html#//apple_ref/doc/uid/10000035i)。

### 通过NSNumber类的实例表示数字

NSNumber 类用来表示所有 C 基础的 scalar 类型，包括 char，double，float，int，long，short 以及它们的无符号变量，还有 Objective-C 中的布尔类型，BOOL。

使用 NSString，你有很多种方式可以创建 NSNumber 实例，包括空间分配并初始化，以及使用工厂方法：

```

    NSNumber *magicNumber = [[NSNumber alloc] initWithInt:42];

    NSNumber *unsignedNumber = [[NSNumber alloc] initWithUnsignedInt:42u];

    NSNumber *longNumber = [[NSNumber alloc] initWithLong:42l];
 
    NSNumber *boolNumber = [[NSNumber alloc] initWithBOOL:YES];
 
    NSNumber *simpleFloat = [NSNumber numberWithFloat:3.14f];

    NSNumber *betterDouble = [NSNumber numberWithDouble:3.1415926535];
 
    NSNumber *someChar = [NSNumber numberWithChar:'T'];

```

你也可以用 Objective-C 中的语法来创建 NSNumber 实例：

```

    NSNumber *magicNumber = @42;

    NSNumber *unsignedNumber = @42u;

    NSNumber *longNumber = @42l;
 
    NSNumber *boolNumber = @YES;
 
    NSNumber *simpleFloat = @3.14f;

    NSNumber *betterDouble = @3.1415926535;
 
    NSNumber *someChar = @'T';

```

这些例子都等同于使用 NSNumber 类的工厂方法。

一旦你已经创建了一个 NSNumber 实例，你就可以使用 accessor 方法申请一个纯量值：

```

    int scalarMagic = [magicNumber intValue];

    unsigned int scalarUnsigned = [unsignedNumber unsignedIntValue];

    long scalarLong = [longNumber longValue];
 
    BOOL scalarBool = [boolNumber boolValue];
 
    float scalarSimpleFloat = [simpleFloat floatValue];

    double scalarBetterDouble = [betterDouble doubleValue];
 
    char scalarChar = [someChar charValue];

```

NSNumber 类也为 Objective-C 额外的基本类型提供了方法。如果你想创建 NSInteger 和 NSUInteger 的对象，请使用如下方法：

```

    NSInteger anInteger = 64;

    NSUInteger anUnsignedInteger = 100;
 
    NSNumber *firstInteger = [[NSNumber alloc] initWithInteger:anInteger];

    NSNumber *secondInteger = [NSNumber numberWithUnsignedInteger:anUnsignedInteger];
 
    NSInteger integerCheck = [firstInteger integerValue];

    NSUInteger unsignedCheck = [secondInteger unsignedIntegerValue];

```

所有的 NSNumber 实例都是不可变的，并且它也没有可变的子类；如果你需要一个不同的数字，请使用另一个 NSNumber 实例。

> **提示**

>NSNumber 实际上是一个类集。这意味着当你在运行时创建了一个实例时，你会得到一个合适的具体化的子类，值为给定值。只把创建的对象当做一个 NSNumber 的实例就可以了。


### 使用NSValue类的实例表示其他值

NSNumber 类是基础 NSValue 类的子类，它给单独的数据或数据项提供了包装对象。除了 C 的基本纯量类型，NSValue 也可以表示指针和结构体。

NSValue 类提供了很多的工厂方法来创建一个给定结构的值，这使得创建一个实例变得十分简单，例如之前例子中的 NSRange：

```

    NSString *mainString = @"This is a long string";

    NSRange substringRange = [mainString rangeOfString:@"long"];

    NSValue *rangeValue = [NSValue valueWithRange:substringRange];

```

你也可以使用 NSValue 来创建自定义的结构体对象。如果你一定要用 C 中结构体存储信息，请这样做：

```

    typedef struct {
        int i;
        float f;
    } MyIntegerFloatStruct;

```

你可以通过指向结构体的指针或编好的 Objective-C 类型来创建一个 NSValue 实例。@encode() 这条编译器指令是用来创建正确的 Objective-C 类型的，如：

```

    struct MyIntegerFloatStruct aStruct;

    aStruct.i = 42;

    aStruct.f = 3.14;
 
    NSValue *structValue = [NSValue value:&aStruct
                             withObjCType:@encode(MyIntegerFloatStruct)];

```

标准 C 中的 & 符号在这里表示 aStruct 中 value 参数的地址。

## 大多数集合都是对象

尽管你可以使用 C 中的数组存放 scalar 数据的集合，甚至还可以存放对象指针，大多数 Objective-C  代码中的集合都是 Cocoa 和 Cocoa Touch 集合类中的一个，例如 NSArray, NSSet 和 NSDictionary。

这些类用来管理一组对象，这意味着你加入集合的任何一项都必须是 Objective-C 类的实例。如果你需要添加一个 scalar 值，你必须要先创建一个合适的 NSNumber 或 NSValue 实例。

集合类使用强大的引用持续追踪他们的内容，而不是不管怎样都为集合中的每一个对象做一份拷贝。这意味着你加入集合中的每一个对象的生命周期都将至少和集合的生命周期一样长， 正如这里描述的[Manage the Object Graph through Ownership and Responsibility](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html#//apple_ref/doc/uid/TP40011210-CH5-SW3)

除了追踪他们的内容，Cocoa 和 Cocoa Touch 中的每一个集合类都可以简单地完成一些任务，例如列举，存取特定的项，查找某一个对象是否在这个集合中。

基本的 NSArray, NSSet 和 NSDictionary 类都是不可变的，它们的值在创建时就固定了。但它们都拥有可变的子类，你可以通过子类来添加和删除对象。

想查看更多 Cocoa 和 Cocoa Touch 中的集合类，请点击[Collections Programming Topics](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Collections/Collections.html#//apple_ref/doc/uid/10000034i)


### 数组是有序的集合

NSArray 是用来表示对象的有序集合。对集合中内容唯一的要求是每一项都要是 Obejctive-C 的对象，但它们并不需要是同一个类的实例。

为了满足有序，每一个元素的存储都是从 0 开始记序，就像图 6-1 这样。

![图6-1 Objective-C 对象数组](images/orderedarrayofobjects.png)

#### 创建数组

就像之前章节里写的赋值类那样，你可以通过分配空间并初始化，使用工厂类方法或纯语法方式来创建数组。

初始化方法和工厂方法有很多种，你可以根据对象的个数来选择不同的方法：

```

    + (id)arrayWithObject:(id)anObject;
    
    + (id)arrayWithObjects:(id)firstObject, ...;
    
    - (id)initWithObjects:(id)firstObject, ...;

```

arrayWithObjects 和 initWithObjects: 这两种方法都采用零终止（nil-terminated），参数个数是可变的，这意味着你数组的最后一个值必须是 nil：

```

    NSArray *someArray =
    [NSArray arrayWithObjects:someObject, someString, someNumber, someValue, nil];

```

这个例子创建了一个类似图6-1中的数组。第一个对象：someObject，下标是 0；最后一个对象：someValue，下标是 3。

如果数组中有一个值为 nil，那么这个数组会在这里被截断：

```

    id firstObject = @"someString";

    id secondObject = nil;

    id thirdObject = @"anotherString";

    NSArray *someArray =
    [NSArray arrayWithObjects:firstObject, secondObject, thirdObject, nil];

```

在这里，someArray 数组只包含 firstObject，因为第二个元素为 nil，系统认为它是数组的结束。

**语法**

你可以用 Objective-C 的语法创建数组：

```

    NSArray *someArray = @[firstObject,secondObject, thirdObject];

```

在这里你不能用 nil 来结束数组，事实上 nil 是非法的值。如果你运行下面的代码，系统会抛出异常：

```

    id firstObject = @"someString";

    id secondObject = nil;

    NSArray *someArray = @[firstObject, secondObject];
    // exception: "attempt to insert nil object"

```

如果你一定要使用 nil 值，你可以用 NSNull 类，详情请查看[Represent nil with NSNull](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/FoundationTypesandCollections/FoundationTypesandCollections.html#//apple_ref/doc/uid/TP40011210-CH7-SW34)

#### 查找数组对象

一旦你创建了数组，你可以查询有关的信息，例如对象个数或查找特定的元素：

```

    NSUInteger numberOfItems = [someArray count];
 
    if ([someArray containsObject:someString]) {
        ...
    }

```


你也可以查询指定下标的元素。如果你查询的下标数是错误的，系统会抛出越界异常，所以你应该首先确认数组长度：

```

    if ([someArray count] > 0) {
        NSLog(@"First item is: %@", [someArray objectAtIndex:0]);
    }

```


这个例子检查了数组长度是否大于 0。如果大于 0，它会记录下第一个元素，也就是下标为 0 的元素信息。

**下标**

还有另一种下标写法，使用 objectAtIndex：这与 C 中数组写法十分像。使用这种方法重写前面的例子：

```

    if ([someArray count] > 0) {
        NSLog(@"First item is: %@", someArray[0]);
    }

```


#### 给数组对象排序

NSArray 类提供了很多种排序的方法。因为 NSArray 是不可变的，所以所有的方法都会返回一个有序数组。

例如，你可以通过调用 compare 给字符串排序：

```

    NSArray *unsortedStrings = @[@"gammaString", @"alphaString", @"betaString"];
    NSArray *sortedStrings =
                 [unsortedStrings sortedArrayUsingSelector:@selector(compare:)];
```


#### 可变性

尽管 NSArray 类是不可变的，这并不会影响集合中的对象。如果你想添加一个可变的字符串，可以这样做：

```

    NSMutableString *mutableString = [NSMutableString stringWithString:@"Hello"];
    NSArray *immutableArray = @[mutableString];

```

没有什么可以阻止你改变它：

```

    if ([immutableArray count] > 0) {
        id string = immutableArray[0];
        if ([string isKindOfClass:[NSMutableString class]]) {
            [string appendString:@" World!"];
        }
    }

```


使用 NSMutableArray 增删初始化后的数组对象，它提供了很多个方法添加、删除和替换一个或多个对象：

```

    NSMutableArray *mutableArray = [NSMutableArray array];

    [mutableArray addObject:@"gamma"];

    [mutableArray addObject:@"alpha"];

    [mutableArray addObject:@"beta"];
 
    [mutableArray replaceObjectAtIndex:0 withObject:@"epsilon"];

```

这个例子创建了以 @"epsilon", @"alpha",@"beta" 结尾的数组。


不用创建一个新的数组也可以给可变数组排序：

```

    [mutableArray sortUsingSelector:@selector(caseInsensitiveCompare:)];

```

这使得该数组以字母升序排序：@"alpha", @"beta", @"epsilon"。


### 无序集合：Sets

NSSet和数组很相似，但是它可以存储不同对象的无序组，如图6-2

![图6-2 一组对象](images/unorderedsetofobjects.png)

因为 sets 是无序的，所以当涉及到测试成员时，sets 比数组更有优势。


基础的 NSSet 类也是不可变的，所以它的值必须在创建时确定，使用空间分配并初始化或工厂方法：

```

    NSSet *simpleSet =
      [NSSet setWithObjects:@"Hello, World!", @42, aValue, anObject, nil];

```

和 NSArray, initWithObjects:,setWithObjects: 方法一样，NSSet 以 nil 作为结束，参数个数可变。可变的 NSSet 的子类是 NSMutableset。

Sets 中每一个对象只能存储一个引用,所以你不能多次添加同一个对象：

```

    NSNumber *number = @42;
    
    NSSet *numberSet =
      [NSSet setWithObjects:number, number, number, number, nil];
    // numberSet only contains one object

```

查看更多关于sets： [Sets: Unordered Collections of Objects](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Collections/Articles/Sets.html#//apple_ref/doc/uid/20000136)


### 字典是一种存储键值对的集合

NSDictionary 存储了对象和它们的关键字，而不是单单只保存了一个有序或无序的集合，这个功能可以用来恢复信息。

最好使用字符串作为字典的关键字，如图6-3：

![图6-3 对象词典](images/dictionaryofobjects.png)

>**提示**
>
使用其他对象作为关键字也可以，但值得注意的是关键字需要被字典复制，所以你的关键字一定要支持 NSCopying。
>
>如果你的代码包含键值，你必须用字符串关键字作为字典对象，正如这里提到的： [Key-Value Coding Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueCoding/Articles/KeyValueCoding.html#//apple_ref/doc/uid/10000107i)


#### 字典的创建

你可以用空间分配并初始化或工厂方法创建字典：

```

    NSDictionary *dictionary = [NSDictionary dictionaryWithObjectsAndKeys:
                   someObject, @"anObject",
             @"Hello, World!", @"helloString",
                          @42, @"magicNumber",
                    someValue, @"aValue",
                             nil];

```

注意使用 dictionaryWithObjectsAndKeys: 和 initWithObjectsAndKeys:方法时，每个对象在给定关键字前需要确定好，对象和关键字都必须以 nil 结束。

**语法**

Objective-C 也可以使用纯语法创建字典：

```

    NSDictionary *dictionary = @{
                  @"anObject" : someObject,
               @"helloString" : @"Hello, World!",
               @"magicNumber" : @42,
                    @"aValue" : someValue
    };

```

注意在字典语法中，关键字需要先确定，然后再确定对象，而且关键字不能以 nil 结束。

#### 字典的查询

一旦你创建了字典，你可以从中给对象指定一个关键字：

```

    NSNumber *storedNumber = [dictionary objectForKey:@"magicNumber"];

```

如果没有找到该对象，objectForKey：方法会返回 nil。

objectForKey：方法还有另一种使用方法也可以完成这个功能：

```

    NSNumber *storedNumber = dictionary[@"magicNumber"];

```

#### 可变性

如果要在创建 dictionary 后增删对象，你需要使用 NSMutableDictionary 子类：

```

    [dictionary setObject:@"another string" forKey:@"secondString"];

    [dictionary removeObjectForKey:@"anObject"];

```

###　使用NSNull代替nil

在这一部分中，你不可以在集合类中添加　nil，因为在　Objectiv-C　中，nil　表示“没有对象”。如果你需要在集合中表示“没有对象”，你要使用　NSNull　类：

```

    NSArray *array = @[ @"string", @42, [NSNull null] ];

```

NSNull 是一个单例类，这意味着 null 方法总是返回同样的实例。这表示你可以用共享的 NSNull 实例来查找数组中的对象：

```

    for (id object in array) {
        if (object == [NSNull null]) {
            NSLog(@"Found a null object");
        }
    }

```


## 使用集合来保存对象的图形。
直接写 NSArray 和 NSDictionary 类是十分简单的：

```

    NSURL *fileURL = ...

    NSArray *array = @[@"first", @"second", @"third"]; 

    BOOL success = [array writeToURL:fileURL atomically:YES];

    if (!success) 
    {
        // an error occured...
    }
```

如果集合中的某一个对象是 property list 类型（ NSArray，NSDictionary，NSString，NSData，NSDate 和 NSNumber ），你可以从磁盘中重新创建完整的层次：

```

    NSURL *fileURL = ...
    NSArray *array = [NSArra arrayWithContentsOfURL:fileURL];
    if (!array) {
        // an error occurred...
    }

```

查看更多关于property lists，请看[Property List Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/PropertyLists/Introduction/Introduction.html#//apple_ref/doc/uid/10000048i)

如果你需要保存除了上面提到的标准property list类之外的对象类，你可以使用存档对象，例如 NSKeyedArchiver，来创建一个集合式的对象存档。

创建存档的唯一要求是每一个对象必须支持 NSCoding 协议。这意味着每一个对象必须知道如何在存档中编码（通过实现 encodeWithCoder: 方法），以及在读取存档时解码（通过 initWithCoder: 方法）。

NSArray，NSSet，NSDictionary 以及他们的可变子类，都支持 NSCoding，这意味着你可以通过存档来保存复杂的对象层次。如果你使用   Interface Builder 来布局窗口和视图，那么你的 nib 文件就是视觉化的层次对象存档。在程序执行时，对于使用有关类的对象层次，nib 文件是不能存档的。

查看更多关于 Archives，请看[Archives and Serializations Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i)


## 使用最有效的集合列举技术

Objective-C、Cocoa、Cocoa Touch 提供了多种列举集合内容的方法。当然，使用 C 语言中传统的 for 循环来列举内容也是可以的：

```

    int count = [array count];
    for (int index = 0; index < count; index++) {
        id eachObject = [array objectAtIndex:index];
        ...
    }

```

你最好可以练习使用这部分中描述的其他技术来实现这个功能。


### 快速列举使列举一个集合变得容易

许多集合类都符合 NSFastEnumeration 协议，包括 NSArray，NSSet 和 NSDictionary。这意味着你可以使用快速列举，一种 Objective-C 中特有的语言。

快速列举数组或集合中内容的语法：

```

    for (<Type> <variable> in <collection>) {
        ...
    }

```

使用快速列举描述每一个数组中对象：

```

    for (id eachObject in array) {
        NSLog(@"Object: %@", eachObject);
    }

```

其中的 eachObject 变量在每一层循环里自动地被设为当前对象，所以每一个对象都会列举出来。

如果你在字典中进行快速列举，你要像这样遍历字典的关键字：

```

    for (NSString *eachKey in dictionary) {
        id object = dictionary[eachKey];
        NSLog(@"Object: %@ for key: %@", object, eachKey);
    }

```

快速列举十分像 C 中的 for 循环，所以你可以用 break 来终止循环，用 continue 来进入下一层。

如果你要列举一个有序的集合，列举操作会按循序进行。对于 NSArray 类，列举操作的第一个对象是下标为 0 的那个元素，第二个对象是下标为 1 的那个，以此类推。如果你需要记录当前下标的值，只要简单的计算迭代次数：

```

    int index = 0;
    for (id eachObject in array) {
        NSLog(@"Object at index %i is: %@", index, eachObject);
        index++;
    }

```


###大多数集合也支持列举对象

你可以使用 NSEnumerator 对象来列举许多 Cocoa and Cocoa Touch 集合。

你也可以使用 NSArray 中的 objectEnumerator 或者 reverseObjectEnumerator 来实现快速列举：

```

    for (id eachObject in [array reverseObjectEnumerator]) {
        ...
    }

```

在这个例子中，循环会倒序输出集合中的对象，所以最后一个对象将会第一个被列举。

你也可以通过重复地调用 nextObject 方法来迭代内容：

```

    id eachObject;
    while ( (eachObject = [enumerator nextObject]) ) {
        NSLog(@"Current object is: %@", eachObject);
    }

```

在这个例子中，while 用来设置下一个循环中对象的 eachObject 变量。当集合中没有剩下的对象时，nextObject 方法会返回 nil ，它被当成 false 所以循环终止。

> **提醒**

> 由于在 C 程序中，等号（==）非常容易写错成赋值符号（=），当你在分支程序或循环中设定变量时，编译器会弹出警告：
> ```
>
     if (someVariable = YES) 
     {
       ...
     }
>
>```
>
>如果你真的想让逻辑值等于等号左边的变量，你可以将表达式用括号括起来：
>
> ```
>
     if ((someVariable = YES)) 
     {
       ...
     }
>
>```

当你使用快速列举时，你不可以改变集合。而且，使用快速列举法比人工列举对象要更快，因为你不用收集对象的名字。


### 许多集合都支持基于块的列举
你也可以使用块来列举 NSArray，NSSet 和 NSDictionary。关于块的详细信息会在下一章中介绍。
