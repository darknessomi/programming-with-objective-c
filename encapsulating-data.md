#数据封装

  除了前面一章提到的消息行为（ messaging behavior ）外，对象还可以通过属性封装数据。
  这一章节描述了 ObjC 中用于声明对象属性的语法，阐明了属性如何通过默认的访问方法和实例变量的生成实现。如果实例变量支持该属性，那么该变量必须在所有初始化方法中正确的设定。
  如果一个对象需要通过权限包含一个连向其他对象的链接，那么考虑这两个对象之间的关系性质将会变得很重要。尽管ObjC的内存管理将会通过自动引用计数（ Automatic Reference Counting (ARC)）为你处理大部分类似情况，但你仍需要了解这些，以避免类似强引用环（ strong reference cycle ）导致内存溢出等问题的出现。这一章还介绍了对象的生命周期，以及如何从利用关系来管理对象图的角度思考。
##用属性封装对象的值
 大部分对象都会通过跟踪信息来执行任务。一些对象被设计为一个以上具体值的模型，如Cocoa 中的用来保存数值的NSNumber类 或用来代表一个有姓、名的人的自定义类 XYZperson 。还有些对象作用域更为广泛，甚至可能用来处理用户界面和其显示信息之间的交互，但即使这样的对象，仍需要跟踪用户界面元素或相关模式对象的信息。
 
###为外部数据声明公共属性

  ObjC的属性提供了一种定义信息的方式，而这些信息正是类中将要封装的。正如你在[ Properties Control Access to an Object’s Values](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW7)一节中看到的，类的接口（ interface ）包含了属性声明，例如：
  
    @interface XYZPerson : NSObject
    @property NSString *firstName;
    @property NSString *lastName;
    @end
在这个例子中XYZPerson类声明了string 属性来保存一个人的名和姓。
这是面向对象编程中一个非常基本的原则——将对象的内部运作隐藏在它的公共接口之后，所以我们总是使用一个对象的外部特性来访问它的属性，而不是直接尝试去访问它内部的值。

 ###通过访问器（ accessor ） 方法来获得或设置属性的值

你通过访问器（ accessor ）方法来访问或设定对象的属性：

    NSString *firstName = [somePerson firstName];
    [somePerson setFirstName:@"Johnny"];
默认地编译器自动为你生成访问器方法，除了在类接口中用 @propertys 声明属性外你不需要做任何事情。
生成中遵循的特定命名惯例：
*  用于访问值的方法（ getter 方法）拥有与属性一样的名字
  对于上例中，名为firstName的属性它的 gette r方法名也为firstName 。
*  用于设置值的方法（ setter 方法）用“ set ”开头的单词命名，并且其中的属性名首字母要大写。
对于上例中，名为 firstName 的属性它的setter方法可以取名 setFirstName 。
如果你不想属性通过setter方法改变值，可以在属性声明中增加一个特征（ attribute ） readonly 表明其不能修改只能读取：

    @property (readonly) NSString *fullName;

除了告诉其他的对象他们应该怎样和一个属性交互以外，特征还告诉了编译器如何合成相应的访问方法。在这种情况下编译器将会合成一个名为fullName getter方法，而不是一个setFullName方法。

**注：** 与 readonly 相对的是 readwrite ，因为 readwrite 特征是默认设置的，所以没有必要再去指明它。
如果你想给访问器方法另外命名，通过给相应属性添加特征，来确定自定义名称是可行的。对于布尔属性（属性值只有 YES 或 NO ），它的 getter 方法按照惯例应该以“ is ”开头，例如，对于一个名为 finished 属性，它的getter方法，应该命名为“ isFinished ”。
同样的，给属性添加一个特征是也是可行的：

    @property (getter=isFinished) BOOL finished;

如果你需要指明多个特征，只需把他们排成一排并用逗号分隔开，像下面这样：

    @property (readonly, getter=isFinished) BOOL finished;

在这种情况下，编译器仅会合成一个 isFinished 方法，而不是 setFinished 方法。

**注：**一般来说属性访问方法遵循键/值编码，也就是说它的命名是遵循一定的命名惯例的。参看[Key-Value Coding Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/KeyValueCoding/Articles/KeyValueCoding.html#//apple_ref/doc/uid/10000107i) 获得更多信息

###点语法对于访问器方法调用是简洁的选择

除了明确的访问器（accessor）方法调用，ObjC 还提供了另外一种选择来访问对象属性——点语法
你可以这样使用点语法访问属性：

    NSString *firstName = somePerson.firstName;
    somePerson.firstName = @"Johnny";

点语法仅是对访问器方法做了一个单纯的包装，当你使用它的时候，你仍是在使用前面提到的 getter 和 setter 方法，来对属性进行访问或变更。
*  通过somePerson.firstName  获得一个值与使用 [somePerson firstName]是相同的
*  通过 somePerson.firstName = @"Johnny" 赋值与使用 [somePerson setFirstName:@"Johnny"] 是相同的
这意味着通过点语法访问属性也是由属性特征控制的。比如一个属性标记为readonly ，那么当你试图通过点语法对该属性进行设置时，将会出现编译错误。
###实例变量支持大多数的属性

默认情况下，一个可读写属性会获得实例变量的支持，这些会由编译器自动生成。
实例变量是一种在对象的生命周期中一直存在并保存着值的变量。实例变量的存储空间是在初次创建时被分配（通过 alloc ），在 dealloc 时被释放。
你可以另外指定其他的名称，或者实例变量将会生成与属性一样的名字，但实例变量的名字之前还会有一个下划线前缀。例如，实例变量的一个可能的名字 _firstName 
尽管通过访问器方法或点语法来访问对象自身的属性是最好的实践，但直接通过类实现中的任意实例方法来访问实例变量也是可行的。下划线前缀表明了你访问的是一个实例变量而不是其他的，诸如局部变量之类的变量

    - (void)someMethod {
        NSString *myString = @"An interesting string";
 
        _someString = myString;
    }

在这个例子中很明显 myString 是一个局部变量 _someString 是一个实例变量。一般来说，你会使用点语法或访问方法进行属性访问，即使是在对象自身的实现里进行也是这样，这时将会用到 self ：

    - (void)someMethod {
    NSString *myString = @"An interesting string";
 
    self.someString = myString;
    // or
    [self setSomeString:myString];
    }

对于这条规则使用的特例是，初始化、释放空间或自定义访问器（accessor）方法，这些情况将会在后面的部分讲到。
####你可以自定义生成的实例变量的名字

正如前面提到的，一个可写属性的默认行为是使用一个名为 _propertyname 的实例变量。
如果你想为实例变量取另外的名字，你需要指导编译器在你的实现中生成一个使用下列语法的变量：

    @implementation YourClass
    @synthesize propertyName = instanceVariableName;
    ...
    @end

例如

    @synthesize firstName = ivar_firstName;

在这种情况下，这个属性仍然要被命名为 firstName ，并仍可以通过访问器（accessor）方法—— firstName 和 setFirstName 或点语法访问。但这种情况下，属性是通过一个名为 ivar_firstName 的实例变量获得支持的。
**重要：** 如果你使用 @synthesize 关键字时，不去指明实例变量的名字的话，像下面这样：

    @synthesize firstName;

那么实例变量将会被命成与属性一样的名字。
在这个例子中，实例变量将会被命名为 firstName ,并且不会加上下划线前缀。
####你可以不通过属性来定义实例变量

当你需要跟踪一个值或者对象时，最好的实现是通过使用另一个对象的属性。
如果你确实需要定义一个你自己单独的实例变量，即不通过声明属性获得，你可以将他们声明在，类接口或实现的那个大括号的最开始，例如：

    @interface SomeClass : NSObject {
    NSString *_myNonPropertyInstanceVariable;
    }
    ...
    @end
 
    @implementation SomeClass {
    NSString *_anotherCustomInstanceVariable;
    }
    ...
    @end
**注意：** 你还需要将这样的实例变量声明在拓展类的最开始，参见 [Class Extensions Extend the Internal Implementation](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html#//apple_ref/doc/uid/TP40011210-CH6-SW3) 。
###可以直接通过初始化方法来访问实例变量

Setter 方法可能会产生额外的副作用，它可能会触发 KVC 通知，或在你编写了自定义方法时执行进一步的任务。
你应该总是从一个初始化方法中直接访问实例变量，因为当属性被设置好时，一个对象的其余部分可能还未初始化完全。即使你不提供自定义访问器（ accesso ）方法,或对自定义类中可能产生的副作用有一定了解，之后产生的子类还是很可能会覆写行为（ behavior ）。
一个典型的init方法会像下面这样：
    - (id)init {
    self = [super init];
 
    if (self) {
        // initialize instance variables here
    }
 
    return self;
    }
一个 init 方法应该在开始它自己的初始化之前，首先用 self 响应父类的初始化方法请求 。一个父类在初始化对象失败之后会返回一个 nil ，所以应当总是在执行你自己类的初始化之前，检查 self 的返回值。
图3-1 初始化过程

![initflow.png](../images/initflow.png)

正如你在前面的章节所看到的，对象的初始化有两种方式，通过调用 init ,或调用为对象初始化具体值的方法。
在 XYNPerson例子中，提供一个可以设置初始名和姓的初始化方法是行的通的。

    - (id)initWithFirstName:(NSString *)aFirstName lastName:(NSString *)aLastName;
你可以这样实现这个方法：

    - (id)initWithFirstName:(NSString *)aFirstName lastName:(NSString *)aLastName {
    self = [super init];
 
    if (self) {
        _firstName = aFirstName;
        _lastName = aLastName;
    }
 
    return self;
    }

####指定初始器是最主要的初始化方法

如果一个对象声明了一个以上的初始化方法，你应该确定一个方法作为指定初始器方法，它通常是为初始化提供最多选择的方法（像是拥有最多参数的方法），并会被其他的便利方法调用。你通常还需要覆写 init 方法来通过合适的默认值调用指定初始器。
如果一个XYZPerson 类还包含一个用来表示生日的属性，那么它的指定初始器方法应该是这样：

    - (id)initWithFirstName:(NSString *)aFirstName lastName:(NSString *)aLastName
                                            dateOfBirth:(NSDate *)aDOB;
这个方法还会设置相应的实例变量值，像上面展示的一样。如果仍希望提供一个只包含姓、名的指定初始器，你需要像下面这样调用指定初始器，来实现这个方法。

    - (id)initWithFirstName:(NSString *)aFirstName lastName:(NSString *)aLastName {
    return [self initWithFirstName:aFirstName lastName:aLastName dateOfBirth:nil];
    }
你或许还需要实现一个标准的 init 方法来提供合适的默认值，例如：

    - (id)init {
    return [self initWithFirstName:@"John" lastName:@"Doe" dateOfBirth:nil];
    }
如果你需要在一个使用多个 init 方法的子类创建时，编写初始化方法，那么，你可以覆写父类的指定初始器来实现你自己的初始化，或者选择再另外添加一个你自己的初始器。无论哪种方法，你都需要在开始你自己的初始化之前，调用父类的指定初始器（在此处 [ super init ] 调用）。

###你可以实现你自定义的访问器方法

属性也不总是被他们自己的实例变量支持的。举一个例子，XYZPerson 类可以为一个人的全名定义一个只读属性：
 
    @property (readonly) NSString *fullName;

比起每次姓或名一变更就不得不更新 fullName 属性 ，只使用自定义访问器（accessor）方法来建立一个要求的全名字符串就显得容易许多。

    - (NSString *)fullName {
    return [NSString stringWithFormat:@"%@ %@", self.firstName, self.lastName];
    }

这个简单的例子使用格式字符串和标识符，建立了一个包含了以空格隔开的姓名的字符串。

**注意：**  尽管这是一个很实用的例子，但认识到它的语境特殊性是很重要的，并且它只适用于那些将名放在姓之前的国家。
如果你为一个确实需要实例变量的属性自定义了一个访问器方法，你必须直接从这个方法的内部来访问这个属性的实例变量。
例如，将属性的初始化推延到它第一次被调用时是普遍的做法，这种做法叫做“ lazy访问器”：
 
    - (XYZObject *)someImportantObject {
    if (!_someImportantObject) {
        _someImportantObject = [[XYZObject alloc] init];
    }
 
    return _someImportantObject;
    }
在返回值之前这个方法会首先检查 _someImportantObject  实例变量的值是不是 nil ，如果是，它将会分配一个对象。

**注意：**  当编译器生成了至少一个访问器（accessor）方法时，它都会再自动生成一个实例变量。但当你为一个读写属性实现了 getter , setter 方法，或为只读属性实现了 getter 方法，编译器都会假设你想要控制属性的实现，并不再会自动生成一个实例变量。此时，如果你仍需要一个实例变量，你需要手动请求生成：

    @synthesize property = _property;

###属性是默认的原子单元

属性是 ObjC 中默认的原子单元。

    @interface XYZObject : NSObject
    @property NSObject *implicitAtomicObject;          // atomic by default
    @property (atomic) NSObject *explicitAtomicObject; // explicitly marked atomic
    @end
这意味着生成的访问器（ accessor ）会确保值总是被 getter 方法取出，或被 setter 方法设置，即使它此时正在被不同的线程调用。由于原子访问器的内部实现和同步是私有的，所以将自己实现的访问器方法，同编译器生成的结合到一起是不太可能的。如果你这样尝试了，你将会得到编译器警告，例如这种情况：为一个原子读写属性提供一个自定义的 setter 方法，却将 getter  方法留给编译器生成。

你可以使用非原子（ nonatomic ）属性特征来为生成的访问器方法指明，它是要直接设置一个值还是返回一个值，但当同样的一个值被不同的线程访问时，不保证会发生什么情况。正是由于这个原因，访问一个非原子属性比访问原子属性快，并且将生成的 setter 方法与其他方法结合是可行的，例如，与你自己实现的 getter 方法。
   
    @interface XYZObject : NSObject
    @property (nonatomic) NSObject *nonatomicObject;
    @end

    @implementation XYZObject
    - (NSObject *)nonatomicObject {
    return _nonatomicObject;
    }
    // setter will be synthesized automatically
    @end
**注意：**  属性原子性与对象线程安全性（ thread safety ）并不是同义词。
考虑 XYZPerson 对象的例子，如果出现一个人的名和姓被一个线程的原子访问器方法修改的情况。此时，另一个线程也在访问这两个字符串，原子的 getter 方法会返回完整的字符串，但并不保证这时的这两个值彼此之间是正确对应的。比如名是在另一个线程访问前变更的，而姓则是在之后，那么你最终得到的将会是一对前后不一，错误匹配的姓和名。

这个例子很简单，但如果考虑了关联对象的关系网后，线程安全性问题将会变得更加复杂。更多细节参看[Concurrency Programming Guide](https://developer.apple.com/library/mac/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091)。

##通过所有权和责任来管理对象图

正如你所看到的， ObjC 为对象划分的内存是动态分配的（通过堆），这意味着你需要通过指针来跟踪对象的地址。与标量值不同，通过指针变量作用域来确定对象生命周期不一定可行。相反，一个对象只要是被其他对象调用了，就要始终在内存中保持活跃。
相比起去关心如何手动管理每个对象的生命周期，你更应该去认真考虑对象之间的关系。
就拿 XYZPerson 的例子来说， firstName  和 lastName 这两个字符串属性可以说是有效的被 XYZPerson 实例所“拥有”，这意味着只要 XYZPerson 对象存在在内存中， 这两个属性就同样存在。
当一个对象，通过有效地掌握其他对象的所有权的方式，依赖于其他对象时，这个对象就被称为强引用（ strong reference ）其他对象。
在 ObjC 中，只要有至少一个对象强引用了另一个对象，那么后面这个对象都会一直保持活跃。 XYZPerson 实例与两个 NSString 对象的关系图见图3-2：

图3-2 强参考

![strongpersonproperties](../images/strongpersonproperties.png)

当一个 XYZPerson 对象从内存中被释放时，假设这时不再有其他任何对象强引用他们，那么这两个字符串对象也会同样被释放。再为这个例子增加一点复杂性，考虑一下下面展示的这个应用的对象图：

图3-3  姓名标志制作 应用

![namebadgemaker](../images/namebadgemaker.png)

当用户点击了更新按钮时，这个标志的预览图就会根据相应的姓名信息更新。当一个 person 对象的信息第一次输入并点击更新按钮时，简化的的对象图可能会是图3-4中的样子，

图3-4 初始 XYZPerson 创建时的关系图

![simplifiedobjectgraph1](../images/simplifiedobjectgraph1.png)

当用户调整了输入的名时，关系图会变成图3-5 中的样子

图3-5 当名改变时的简化关系图

![simplifiedobjectgraph2](../images/simplifiedobjectgraph2.png)

尽管此时 XYZPerson 对象已经有了一个不同的 firstName ，标志视图仍然还包含着一个跟最开始的 @”John” 字符对象的强引用。这意味着 @”John” 对象仍在内存中，并且还被标志视图用来输出名字。
一旦用户第二次点击了更新按钮，标志视图界面将会被告知更新它的内部属性以达到与 person 对象匹配。这时关系图会是图3-6中的样子：

图3-6 更新了标志视图后的简化对象图

![simplifiedobjectgraph3](../images/simplifiedobjectgraph3.png)

这时不再会有任何强引用与最开始的 @”John” 对象关联，它将会从内存中移出。

###避免强引用环( strong reference cycles )

尽管对于对象之间的单向关系，强引用表现的很好，但当你在处理一组互相联系的对象时你就需要小心了。当一组对象是通过强引用环联系时，那么即使外接已经不存在任何跟他们的强引用，他们还是因为对彼此的强引用而保持活跃。
一个明显的例子就是，视图表对象与其授权对象（ delegate ）之间可能隐含的引用环（ iOS 中的UITableView  和 OS X 中的  NSTableView ）。为了使通用视图表类在大多数情况下都可用，它会将一些决定授权给其他外部对象处理。这意味着视图表对象依赖于其他对象来决定显示什么样的内容，或者当用户与视图表中的特定项发生交互时应该做什么。
一个常见的情况是视图表引用了他的授权对象，相应的授权对象也会发出一个关联视图表的引用。

图3-7 视图表与授权对象之间的强引用

![strongreferencecycle1](../images/strongreferencecycle1.png)

但是当其他对象撤销了他们与视图表与其授权对象之间的强引用时，问题就出现了

图3-8 一个强引用环

![strongreferencecycle2](../images/strongreferencecycle2.png)

即使现在这两个对象已经没有任何在内存中存在的必要了——除了他俩之间还存在关联之外，已经没有任何对象与他们有强引用了，但他们却因为彼此之间存在的强引用而一直保持活跃。
当视图表将它与它授权对象之间的关联修改为弱关联（ weak relationship ）的时候（这也是 UITableView  和  NSTableView  如何解决这个问题的），最初的关系图将会变成图3-9。

图3-9 视图表与其授权对象之间的正确关系

![strongreferencecycle3](../images/strongreferencecycle3.png)

如果此时其他对象再撤销他们与视图表和其授权之间的强引用，视图表就不会再与它的授权有强引用了。如图3-10
图3-10 规避了强引用的环

![strongreferencecycle4](../images/strongreferencecycle4.png)

这意味着授权对象会被释放，因此它对视图表的强引用也会解除，如图3-11
图3-11 释放授权对象

![strongreferencecycle5](/images/strongreferencecycle5.png)

一旦授权对象被释放，也就不再有关联于视图表的强引用了，因此它也会被释放。

###通过强、弱声明（Strong and Weak Declaration）来管理所有权

对象属性的默认声明一般是下面这样：

    @property id delegate;
为它生成的实例变量使用了强引用。你可以为属性添加一个特征，来声明弱关联，像这样：

    @property (weak) id delegate;

**注：**  与弱（ weak ）相反的是强(  strong  )，由于强是默认的，所以不需要特别指明出强特征。
局部变量（还有非属性实例变量）同样也默认包含与对象的强引用，这意味着下面这段代码将会如你所期待的那样准确运行：
    
    NSDate *originalDate = self.lastModificationDate;
    self.lastModificationDate = [NSDate date];
    NSLog(@"Last modification date changed from %@ to %@",
                        originalDate, self.lastModificationDate);

在这个例子中，局部变量 originalDate 包含了一个与初始对象 lastModificationDate 的强引用。当 lastModificationDate  属性变更时，它不会再强引用原来的日期值，但这个日期仍然会被 originalDate 强变量保存着。

**注：**  只有当变量在作用域内、或还未分配给其他对象前、或值为 nil 的时候，它才会包含一个与对象的强引用。
如果你不想一个对象包含强引用，你可以把它声明为 _weak ，像是这样：
  
    NSObject * __weak weakVariable;
因为弱关联不会保证对象的活跃，所以有可能在关联还在使用的时候关联对象就被释放了。为避免出现悬空指针的情况，即指针指向的内存开始有对象后来却被释放了，当对象被释放时弱关联会自动被设置为 nil 。
这意味着你在前面的例子中使用一个弱变量时，
    
    NSDate * __weak originalDate = self.lastModificationDate;
    self.lastModificationDate = [NSDate date];

originalDate 变量存在被设置成 nil 的可能性。当  self.lastModificationDate 被重新分配时，属性将不再会保有一个与原日期值相关的强引用。如果此时没有其他变量强引用该日期值，那么原日期将被释放， originalDate 也会被设置成 nil 。
弱变量可能会是混乱的来源，特别是在下面的编码中：

    NSObject * __weak someObject = [[NSObject alloc] init];

在上面的例子中，新分配的对象没有任何与之相关的强引用，所以它会立即被释放， someObject 也会被置为nil。

**注意：**  与 _weak 相对的是 _strong ，再次重申，你不需要特地指明 _strong ，因为它是默认设置的。
同时去思考一个需要多次访问弱属性的方法含义也是很重要的，就像下面这样：

    - (void)someMethod {
    [self.weakProperty doSomething];
    ...
    [self.weakProperty doSomethingElse];
    }
在这种情况下，你可能需要将弱属性存放在一个强变量中，从而确保它在你需要使用的过程中一直保存在内存中。
    - (void)someMethod {
    NSObject *cachedObject = self.weakProperty;
    [cachedObject doSomething];
    ...
    [cachedObject doSomethingElse];
    }
在上面的例子中， cachedObject 包含了一个与初始弱属性值关联的强引用，所以只要 cachedObject 变量还在它的作用域中（同时没有被重新赋予其他值），弱属性就不会被释放。
你需要特别记住的是，如果想确保弱属性在使用前它的值不是 nil ，去测试它还远远不够，像下面这样：

    if (self.someWeakProperty) {
        [someObject doSomethingImportantWith:self.someWeakProperty];
    }
因为在多线程应用中，属性可能会在测试与方法调用之间被释放掉，这样测试就无效了。因此你需要声明一个强局部变量来保存值，像是这样：
 
    NSObject *cachedObject = self.someWeakProperty;           // 1
    if (cachedObject) {                                       // 2
        [someObject doSomethingImportantWith:cachedObject];   // 3
    }                                                         // 4
    cachedObject = nil;                                       // 5

在这个例子中，强引用在第一行被创建，意味着将会在测试和方法调用的过程中，保证对象活跃。在第5行，cachedObject 被设置成 nil ，这样强引用就被解除了，如果此时初始对象并没有其他与之相关的强引用，它将会被释放  someWeakProperty  也会被设置成 nil 。

###对一些类使用不安全、无保留的引用

在Cocoa 和 Coacoa Touch 中还存在一些类，至今还不支持弱关联，这意味着你不能通过声明弱属性或弱变量来跟踪他们。这些类包括  NSTextView ,  NSFont  和  NSColorSpace ，想获得完整的相关类列表，参看Transitioning to ARC Release Notes.
如果你想对这些类使用弱关联，你必须使用不安全引用。对于一个属性，这意味着将要使用 unsafe_unretained 特征：

    @property (unsafe_unretained) NSObject *unsafeProperty;
对于变量，你需要使用 __unsafe_unretained:
    
    NSObject * __unsafe_unretained unsafeReference;

一个不安全引用与弱关联之间的相同点在于，它们都不会保证相应对象的活动。但当目标对象被释放时，不安全引用不会被设置成 nil 。这意味着将会留下一个悬空指针，指向内存中一块开始存有对象之后被释放的区域，这就是所谓的“不安全”。对一个悬空指针发送消息将会引起崩溃。

###备份属性(Copy Properties )保有他们自己的备份

在一些情况下，一个对象可能会希望保存一份，为它的属性设置的其他所有对象的备份。
举个例子，早前在图3-4中提到的 XYZBadgeView  类的接口可能会是这样：

    @interface XYZBadgeView : NSView
    @property NSString *firstName;
    @property NSString *lastName;
    @end

上例声明了两个  NSString  属性，他们都包含了与自己对象的不明确强引用。
当有另一个对象也创建了一个字符串来设置标志视图中的一个属性，考虑会发生的情况，
    
    NSMutableString *nameString = [NSMutableString stringWithString:@"John"];
    self.badgeView.firstName = nameString;
这是完全有效的，因为  NSMutableString  是 NSString 的一个子类。尽管标志视图认为它正在处理的是 NSString 实例，但实际上它处理的是 NSMutableString 。
这意味着字符串可以通过这样的方式变更：
  
    [nameString appendString:@"ny"];

在这个例子中，尽管最开始时为标志视图 firstName  属性设置的名字值是“ John ”，但因为可变字符串（ mutable string ）值被改变了，所以它现在变成了“Johnny ”。
你可能会选择为标志视图保存一份他自己的，包含所有为它的 firstName 属性和 lastName 属性设置的字符串值的备份，这样就可以有效的捕捉属性被设置时字符串的值。通过为这两个属性声明一个 copy 特征可以达到这样的目的：

    @interface XYZBadgeView : NSView
    @property (copy) NSString *firstName;
    @property (copy) NSString *lastName;
    @end

现在标志视图包含了自己关于这两个字符串的备份了。即使可变字符串后来改变了，标志视图获取的都还是这两个字符串最初被设定的值。例如：
    
    NSMutableString *nameString = [NSMutableString stringWithString:@"John"];
    self.badgeView.firstName = nameString;
    [nameString appendString:@"ny"];

这次，被标志视图保存的 firstName 值将会是来自于一份保有初始“ John ”字符串的抗影响备份。
Copy 特征表明，属性因为需要与新创建的对象保持一致，而使用了强引用。

**注：**  任何你希望设置备份属性的对象都需要支持 NSCopying ，这意味着它需要遵守 NSCopying协议。协议的描述在 [Protocols Define Messaging Contracts](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithProtocols/WorkingwithProtocols.html#//apple_ref/doc/uid/TP40011210-CH11-SW2) ，获取更多关于 NSCopying 的信息可以参看 NSCopying  或者[ Advanced Memory Management Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)
如果你需要直接设置一个备份属性的实例变量，例如在初始器方法中，不要忘记为原始的对象设置一个备份：

    - (id)initWithSomeOriginalString:(NSString *)aString {
    self = [super init];
    if (self) {
        _instanceVariableForCopyProperty = [aString copy];
    }
    return self;
    }

##练习

1.  为 XYZPerson 添加一个 sayHello 方法，使用人的名和姓来加载一句打招呼的话。
2.  声明并实现一个新的指定初始器（ designated initializer ），用来创建一个XYZPerson类，该类使用指定姓、名、和生日日期， 同时还包含合适的类制造方法（ class factory method ）
3.  测试当你设置可变字符串（ mutable string ）做为人名，然后在调用你添加的 sayHello 方法之前，改变人名字符串的值会出现的情况。为 NSString 属性声明添加备份特征，再测试一次。
4.  尝试使用main() 函数中各种强、弱变量来创建  XYZPerson 对象。验证强变量会按照你期望的那样，保持XYZPerson对象活动。
为了验证一个 XYZPerson 对象是在何时被释放的，你可能会想通过在  XYZPerson 实现中添加一个 dealloc 方法，来将它与对象的生命周期关联起来。当一个ObjC 对象从内存中释放时这个方法会被自动调用，同时该方法也可以用于释放你手动分配的内存，就像C中的 malloc() 函数一样，参看[Advanced Memory Management Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)
为了这种目的的练习，覆写  XYZPerson 中的 dealloc 方法来加载消息，像这样：

    - (void)dealloc {
    NSLog(@"XYZPerson is being deallocated");
    }
尝试通过设置  XYZPerson 中的每一个指针变量为 nil ，来验证对象如你所期待的那样被
释放了。
**注：**  在 Xcode 工程中，为命令行提供的样板，在 main() 函数内使用 @autoreleasepool { } 块，以达到使用编译器的自动保留计数功能，来为你处理内存管理。你在main() 函数中写的任何代码都会进入自动释放池（ autoreleasepool ），这点是非常重要的。
自动释放池在本文档中没有涉及，更多细节参看[Advanced Memory Management Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i)
当你正在编写 Cocoa 或 Cocoa Touch 应用而不是命令行时，你不需要过多的担心关于创建你自己的自动释放池的问题，因为那样你就在尝试进入对象的构架中，而这个构架却可以保证这个池的正确存在。
5.  改类的描述，使你可以跟踪配偶（ spouse ）或伙伴（ partner ）。你需要考虑怎样才能最好的模拟这种关系——你可以认真思考一下对象图管理。


