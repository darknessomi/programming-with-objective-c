# 使用块 - Working with Blocks

一个 object-c 类定义了一个对象，这个对象整合了与行为相关的数据。有时，它仅仅代
表了一个简单的小任务或是一个行为单元而不是一个方法的集合。
区块是添加在 c，object-c 和 c++ 语言中的语言级别的形式，它允许你编写一个独特的代
码段，这个代码段能够在作为值方法和函数中传递。块是 object-c 的对象，这意味着他
们能够被添加到像 `NASArray` 或是 `NSDictionary` 的集合中。他们也有从作用域中捕获值的
能力，使他们与其他编程语言关于 toclosures 或 lambda 表达式上的作用非常类似。
本章阐释了定义和引用块的语法，也展示了如何使用块简化一般的任务
，比如集合枚举。欲了解更多的信息，请参阅Blocks Programming Topics。
## 块语法
块的定义语法是用脱字符（^）来定义的，如下：

```
 ^{
         NSLog(@"This is a block");
    }
```

函数和方法定义时，大括号表明了块的起点和终点。在本例中，该块不返回任何值，也没有任何参数。
你也可以用同样的方式函数指针指向一个c函数，声明一个变量来跟踪块，如下：

```
    void (^simpleBlock)(void);
```

如果你没有接触过c函数指针，那么这里的语法你也许会觉得有一些困扰。这个例子声明了一个变量 called `simpleBlock` 来调用一个没有参数和返回值的块，这意味着变量能够被像上面那样的块进行赋值，如下：

```
 simpleBlock = ^{
        NSLog(@"This is a block");
    };
```

这就像其他任何的变量赋值，所以语句必须被以右大括号后面的分号来进行结束。你也可以把变量的声明和赋值进行合并:

```
    void (^simpleBlock)(void) = ^{
        NSLog(@"This is a block");
    };
```

一旦你已经声明和赋值了一个块变量，你能够用它来调用这个块：

```
simpleBlock();
```

提示：如果你试图用没有被赋值的变量来调用一个块（零块变量），你的应用将会崩溃。


### 有参数和返回值的块
块也可以有参数和返回值就像其他的方法或是函数。
举一个例子，考虑一个变量来引用返回两个变量相乘结果的块：

```
double (^multiplyTwoValues)(double, double);
```

相应的块文字可能是这样的：

```
    ^ (double firstValue, double secondValue) {
        return firstValue * secondValue;
    }
```

就像其他函数的定义那样,当块被调用时 `firstValue` 和 `secondValue` 是用来计算结果值的。在这个例子中，返回值类型是由块中返回语句来决定的。

根据你的个人所好，你可以通过在脱字符之后明确写出返回值类型：

```
   ^ double (double firstValue, double secondValue) {
        return firstValue * secondValue;
    }
```

一旦你已经声明或定义了一个块，你就能像一个函数一样来调用他：

```
 double (^multiplyTwoValues)(double, double) =
                              ^(double firstValue, double secondValue) {
                                  return firstValue * secondValue;
                              };
 
    double result = multiplyTwoValues(2,4);
 
    NSLog(@"The result is %f", result);
```


###块能够从封闭区域中捕捉值

和包含可执行代码一样，块也有能够从封闭区域捕捉的能力。

比如，如果你在一个方法中声明了块，他可以捕捉到任何在方法域中可以访问到的值，如下：

```
\- (void)testMethod {
    int anInteger = 42;
 
    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };
 
    testBlock();
}
```

在这个例子中 `anInteger` 在块外进行定义，但是当块被定义时，该值也是被捕获了。

除非另行指定，值是一定会被捕获的。这意味着如果你在定义块的时间点和调用块的时间点之间改变外部变量的值，如下：

```
  int anInteger = 42;
 
    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };
 
    anInteger = 84;
 
    testBlock();
```

被块捕获的值是不受影响的。这意味着输出结果仍然会显示：

```
Integer is: 42
```

它同样意味着块不能够改变初始变量的值，或者是被捕获的值（被当做常量）。


#### 使用_ _block变量共享存储

如果您需要能够从一个块中改变捕获变量的值，你可以使用 `_ _block` 存储类型修饰符对原变量声明。这意味着变量是存在于在作用域范围之内声明的块共享的存储空间。

举个例子，你可能会改写先前的例子，如下：

__block int anInteger = 42;
 
    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
    };
 
    anInteger = 84;
 
    testBlock();

由于 `anInteger` 声明为 `__block` 变量，其存储与块声明共享。这意味着，在日志的输出将现在显示：

```
Integer is：84
```

这也意味着，该块可以修改原始值，如下：

```
    __block int anInteger = 42;
 
    void (^testBlock)(void) = ^{
        NSLog(@"Integer is: %i", anInteger);
        anInteger = 100;
    };
 
    testBlock();
    NSLog(@"Value of original variable is now: %i", anInteger);
```

这一次，输出将显示：

```
Integer is: 42
Value of original variable is now: 100
```


###你可以把块作为方法或函数的参数

本章前面的例子，都是再定义一个块后，立即就调用它。实际中更常见的是在其他地方收到指令后在把块传递给方法或函数。比如，你可能用 GCD 技术来在后台调用一个块，或者定义一个块来表示一个任务被反复调用。比如，列举的集合时。并发性和枚举将本章后面介绍。

块也用于回调，定义了当一个任务结束时将被执行的代码。举一个例子，你的应用程序可能需要通过创建一个对象，执行一个复杂的任务，诸如从web服务请求信息，以响应用户操作。因为任务可能需要很长的时间，任务正在发生时，你应该显示某种进度指示器。一旦任务完成，就要来隐藏该指示器。

这将有可能使用授权做到这一点：你需要创建一个合适的委托协议，实施必要的方法，设置你的对象作为任务的代表，一旦任务完成了，那么就在你的对象中等待它调用委托方法。

使用块将使这方面内容非常容易，因为你可以在你初始化你的任务时，来定义回调行为，如下：

```
- (IBAction)fetchRemoteInformation:(id)sender {
    [self showProgressIndicator];
 
    XYZWebTask *task = ...
 
    [task beginTaskWithCallbackBlock:^{
        [self hideProgressIndicator];
    }];
}
```

这个例子调用一个方法来显示进度指示器，然后创建任务，并告诉它启动。毁掉快明确了在任务完成后将要执行的代码；在这种情况下，调用一个方法来隐藏进度指示器是十分简单的。请注意，此回调块捕获自己以便能够在被调用时能够调用 `hideProgressIndicator` 方法。捕获自己时应当非常小心，因为这非常容易形成一个强引用循环，这将在后面的`“在捕捉自时避免强引用循环”`中详细描述。

在代码的可读性方面，该块可以在任务完成前和任务完成后很容易地看到在一个地方会发生什么。避免了通过跟踪和委托方式来查明将要放生什么的需要。

声明 `beginTaskWithCallbackBlock`：在这个例子中所示的方法是这样的：

```
- (void)beginTaskWithCallbackBlock:(void (^)
(void))callbackBlock;
```

在(void (^)(void)) 指定的参数是块不带任何参数或者返回任何值。该方法的实现可以用普通的方法来调用块：

```
- (void)beginTaskWithCallbackBlock:(void (^)(void))callbackBlock {
    ...
    callbackBlock();
}
```

用和块变量一样的方法来确定以一个或多个参数的块为参数的方法：

```
- (void)doSomethingWithBlock:(void (^)(double, double))block {
    ...
    block(21.0, 2.0);
}
```


#### 块应该总是一个方法的最后一个参数

一个方法仅使用一个块参数为好。如果方法还需要其他的非块参数，块应该放在参数的最后一个。

```
- (void)beginTaskWithName:(NSString *)name completion:(void(^)(void))callback;
```

这使得指定块内嵌时的方法调用更容易阅读，如下：

```
[self beginTaskWithName:@"MyTask" completion:^{
        NSLog(@"The task is complete");
    }];
```

###使用类型定义简化块语法

如果您需要定义具有相同签名多个块，你可能想给这个签名定义自己的类型。
举一个例子，你能够定义一个类型来创建一个没有参数和返回值的块，如下：

```
typedef void (^XYZSimpleBlock)(void);
```

然后，你可以用你自定义的类型作为方法参数，或作为创建的块变量，如下：

```
XYZSimpleBlock anotherBlock = ^{
        ...
    };
```

```
- (void)beginFetchWithCallbackBlock:(XYZSimpleBlock)callbackBlock {

    ...
    callbackBlock();
}
```

当处理以块为参数或返回值类型的块是定义自定义类型尤其重要，考虑如下的例子：

```
void (^(^complexBlock)(void (^)(void)))(void) = ^ (void (^aBlock)(void)) {
    ...
    return ^{
        ...
    };
};
```

该 `complexBlock` 变量是指一个块需要另一块作为一个参数（ABLOCK）并返回另一个块。

重写代码使用类型定义使其可读性更高：

```
XYZSimpleBlock (^betterBlock)(XYZSimpleBlock) = ^ (XYZSimpleBlock aBlock) {
    ...
    return ^{
        ...
    };
};
```

###对象用属性来跟踪块

定义一个属性来跟踪块的语法和定义块变量的语法是相似的：

```
@interface XYZObject : NSObject
@property (copy) void (^blockProperty)(void);
@end
```

注意：你应该指定副本的属性特征，因为一个块需要被复制来跟踪其在原始范围外抓获的状态。
使用自动引用计数的时候，你就不必这样做了，因为它会自动发生，但属性特征最好是来显示
其必然行为。想要得到更多的相关信息，请参考块编程条目。

一个块属性被初始化或调用的方法和其他块变量是相似的：

```
self.blockProperty = ^{
        ...
    };
    self.blockProperty();
```

它也可以使用块属性声明的类型定义，如下：

```
typedef void (^XYZSimpleBlock)(void);
 
@interface XYZObject : NSObject
@property (copy) XYZSimpleBlock blockProperty;
@end
```

###在捕捉自时避免强引用循环
如果你需要在块中捕捉自，比如说定义一个回调块，考虑内存管理的影响十分重要。

块对任何捕获的对象都保持了强引用，包括自己，这意味着它很容易结束了一个很强的参考周期
举个例子，一个对象维护一个捕获自的块的副本属性：

```
@interface XYZBlockKeeper : NSObject
@property (copy) void (^block)(void);
@end
```

```
@implementation XYZBlockKeeper
- (void)configureBlock {
    self.block = ^{
        [self doSomething];    // capturing a strong reference to self
                               // creates a strong reference cycle
    };
}
...
@end
```

编译器会警告你的简单错误可能是这样的，但更复杂的例子可能涉及的对象创建周期之间的多个强引用，使之更难以诊断。为了避免这个问题，捕获一个弱参考是比较好的方法，如下：

```
- (void)configureBlock {
    XYZBlockKeeper * __weak weakSelf = self;
    self.block = ^{
        [weakSelf doSomething];   // capture the weak reference
                                  // to avoid the reference cycle
    }
}
```

通过捕获自我弱指针，块将无法保持牢固的关系去回到XYZBlockKeeper对象。如果该对象在
快被调用前释放，weakSelf指针会简单地设置为零。

##块可以简化枚举

除了一般完成处理程序，许多 cocoa 和 cocoa Touch API 用块来简化一般的任务，如集合枚举
像是 `NSArray` 类，提供了三个基于块的方法，包括：

```
- (void)enumerateObjectsUsingBlock:(void (^)(id obj, NSUInteger idx, BOOL *stop))block;
```

这个方法有一个块参数，对每个项目来说只掉用一次。

```
NSArray *array = ...
    [array enumerateObjectsUsingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"Object at index %lu is %@", idx, obj);
    }];
```

块本身有三个参数，前两个参数表明了当前的对象和他在阵列中的索引。第三个参数为一个指向波尔变量的指针，

你可以用它来停止枚举：

```
[array enumerateObjectsUsingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
        if (...) {
            *stop = YES;
        }
    }];
```

它也可以通过使用 `enumerateObjectsWithOptions` 定制枚举。指定 `theNSEnumerationReverse`选项。例如，以相反的顺序遍历集合。

如果枚举代码块是密集处理的，并且能安全并发执行，你可以使用 `NSEnumerationConcurrent`选项：

```
    [array enumerateObjectsWithOptions:NSEnumerationConcurrent
                            usingBlock:^ (id obj, NSUInteger idx, BOOL *stop) {
        ...
    }];
```

这个标志指示枚举块调用可以分布在多个线程，如果代码块是密集处理型，那么该调用将提供一个潜在的性能提升。请注意，使用此选项时，枚举顺序是不确定的。

```
 NSDictionary 类同样提供基于块的方法，包括：
 NSDictionary *dictionary = ...
    [dictionary enumerateKeysAndObjectsUsingBlock:^ (id key, id obj, BOOL *stop) {
        NSLog(@"key: %@, value: %@", key, obj);
    }];
```

举个例子来说，相对于传统的循环，这样做能够更方便的枚举所有键值对。

##模块可以简化并发任务


块代表了一个不同的工作单元，综合了可执行代码和周围范围可捕获的状态。这使得它非常适合使用OS X和iOS提供的并发选项中的异步调用。你可以使用块简单地定义你的任务，然后让系统执行这些任务的处理器资源可用，而不必弄清楚像线程低层次的机制怎样使用。

OS X和ios提供了多种并发技术，其中包括两个任务调度机制：操作队列和中央调度。这些机制是围绕着等待被调用的任务队列的一个想法。你按照调用顺序将你的块加入队列，然后在处理器资源可用时，你的系统将从队列中取出调用。

一个串行队列只允许同时执行一个任务--下一个任务将在上一个任务完成后才会在队列中取出调用。一个并发队列调用可以包括非常多的任务，并且不用等待前一个人物执行完毕。


###使用块操作与运行队列

一个运行队列是 Cocoa 和 Cocoa Touch 提供的任务调度。要创建的 `NSOperation` 实例来封装工作单元以填充必要数据然后将该操作加入 `NSOperationQueen` 来执行。

尽管你可以创建有自己的自定义NSOperation子类来实现复杂任务，但也可以通过使用 `NSBlockOperation` 和块来创建操作，如下：

```
NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
    ...
}];
```

这虽然是可以手动执行的操作，但是操作也会被添加在正准备执行的已经存在的运行队列或是你自己创建的运行队列：

```
// schedule task on main queue:
NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
[mainQueue addOperation:operation];
 
// schedule task on background queue:
NSOperationQueue *queue = [[NSOperationQueue alloc] init];
[queue addOperation:operation];
```

如果您使用的操作队列，可以配置操作的优先级或依赖性，比如限制一个操作在其他几项操作运行完成后才能执行。你同样可以通过键值观察来监视操作的状态改变，这样，当一个任务完成时，可以更加容易的更新进度指示器。
想要得到更多的关于操作运行队列的信息，请参考Operation Queues。


###用GCD技术在调度队列调度块

如果你需要调用任意代码块来执行，你可以直接利用GCD技术控制的调度队列。对于请求者，调度队列可以很容易地同步或异步执行任务，并以先入先出的顺序执行他们的任务。

你可以创建你自己的调度队列，或是利用GCD提供的自动生成的队列。比如，如果你需要调度一个并发执行的任务，你能从已经存在一个队列中得到参考，通过使用 dispatch_get_global_queue()函数来调节队列优先级，如下：

```
dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
```

你可以用 `dispatch_async()` 方法或是 `dispatch_sync()` 方法，调度块到队列中。 `dispatch_async()` 方法不用等到块被调用会直接立即返回。

```
dispatch_async(queue, ^{
    NSLog(@"Block for asynchronous execution");
});
```

`dispatch_sync()` 方法不会返回指导块完成执行；比如，在一个需要等待其他工作完成才能继续的并发块中你可以运用该方法。

想要得到更多关于 GCD 和调度队列的信息，请参考 `Dispatch Queues`。

























