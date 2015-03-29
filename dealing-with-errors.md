#错误处理

几乎所有的 APP 都会出现错误。一些错误可能会在你的可控范围之外，例如硬盘空间耗尽或者网络连接中断。另一些错误却是可恢复的，例如无效用户输入。当开发者在不断追逐完美的过程中，也可能会伴随着偶尔的编程错误的出现。
如果你来自于其他的语言和开发平台，你也许会习惯于处理大多数错误处理中的异常。当你用 ObjC 编程的时候，异常仅会出现在编程错误中，就像数组越界访问或无效方法参数。这些就是在你的 APP 上线前的测试中，你需要排查并修复的问题。
NSError 类的实例代表了所有其他的错误。这一章我们将简单的介绍一下 NSError 对象的使用，包括怎样处理构造方法可能出现的失败和返回错误。更多信息参见 [Error Handling Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ErrorHandlingCocoa/ErrorHandling/ErrorHandling.html#//apple_ref/doc/uid/TP40001806)
##对大多数的错误使用 NSError

错误是任何APP 生命周期中不可避免的一部分，假设你需要向一个远程网络服务器请求数据，在这个过程中有多种潜在问题可能出现，包括：
*  无网络连接
*  远程网络服务器不可访问
*  远程网络服务器不能提供你请求的信息
*  得到的数据与你期望的不匹配
遗憾的是，建立所有可能问题的应急计划与方案是不现实的。相反你必须为可能出现的错误筹划并且知道如何解决，从而获得最好的用户体验。

###一些授权方法（ delegate method ）向你提醒错误

如果你正实现一个授权对象，它是与一个执行某任务的构造类（ framework class ）一起使用的，比如这个对象需要从远程服务器上下载信息。通常，你会发现你需要至少实现一个错误关联方法（error-related method）。例如 包括了一个 connection:didFailWithError:  方法的NSURLConnectionDelegate 协议：
    
    - (void)connection:(NSURLConnection *)connection didFailWithError:(NSError *)error;
当一个错误发生时，授权方法将会被调用，以向你提供一个 NSError 对象来描述这个错误。
一个NSError对象包含一个数字错误代码，域名和描述，以及封装在一个用户信息字典里的其他相关信息。
比起做出让所有可能错误都具有唯一的数字代码的要求，Cocoa 和 Cocoa Touch 的错误被划分成域。例如一个错误发生在 NSURLConnection 中，NSURLErrorDomain 域中的  connection:didFailWithError: 方法将会报一个错。错误对象还包含一个本地化的描述，例如“找不到指定主机名的服务器”。


###一些方法可以通过引用传递错误

一些Cocoa 和 Cocoa Touch 的API 通过引用传回错误，举个例子，你可能决定通过  NSData  的  writeToURL:options:error: 方法，将你从网络服务器获得的信息通过存入硬盘的形式保存下来。这个方法的最后一个参数是一个指向 NSError 指针的引用。

    - (BOOL)writeToURL:(NSURL *)aURL
    options:(NSDataWritingOptions)mask
    error:(NSError **)errorPtr;
在你调用这个方法之前，你需要创建一个合适的指针来传递地址：

    NSError *anyError;
    BOOL success = [receivedData writeToURL:someLocalFileURL
                                    options:0
                                    error:&anyError];
    if (!success) {
    NSLog(@"Write failed with error: %@", anyError);
    // present error to user
    }
当错误发生时，writeToURL:options:error: 方法会返回 NO ,并会更新你的 anyError指针，指向一个错误类来描述出现的问题。在处理应用传递的错误时，重要的是去检测方法的返回值以确定是否有错误发生。不要只是测试是否有设置指向错误的指针。

**提示：** 如果你并不关心出错的对象，则只需要将 NULL 传递给 error: 参数

###如果可以，尽量将错误恢复或显示给用户

对你的 APP 来说最好的用户体验是隐蔽地从错误中恢复。例如，你正在向远程网络服务器发出请求，如果此时发生了错误，你可以试着再向另一个服务器发送请求，或者你可以向用户请求更多的信息，比如有效的用户名和密码，然后再次发出请求。
当从错误中恢复是不可能的时候，你才应该去警告用户。如果你正在使用 iOS的Cocoa Touch进行开发，你需要创建一个 UIAlertView 并调节它，从而向用户显示错误。如果你正在使用 OS X 的 Cocoa ，你可以在任何一个 NSResponder 对象（像是一个界面、窗口甚至是应用程序对象本身）上调用  presentError:  ，然后这个错误便会被传输到响应链上，以进行进一步的调试或恢复。当它到达应用程序对象时，应用程序会通过一个警告板将错误呈现给用户。更多关于向用户呈现错误，参看 [Displaying Information From Error Objects](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ErrorHandlingCocoa/CreateCustomizeNSError/CreateCustomizeNSError.html#//apple_ref/doc/uid/TP40001806-CH204-BAJCIGIA)

###生成你自己的错误信息

为创建你自己的 NSError 类，你需要通过以下格式定义你自己的错误类：

    com.companyName.appOrFrameworkName.ErrorDomain
你需要为每一个可能发生在你的错误域中的错误选择一个唯一的错误编码，同时还有合适的错误描述，关于错误的描述将会被存储在用户信息字典中。像下面这样：

    NSString *domain = @"com.MyCompany.MyApplication.ErrorDomain";
    NSString *desc = NSLocalizedString(@"Unable to…", @"");
    NSDictionary *userInfo = @{ NSLocalizedDescriptionKey : desc };
 
    NSError *error = [NSError errorWithDomain:domain
                                         code:-101
                                     userInfo:userInfo];

这个例子使用了 NSLocalizedDescriptionKey  函数来查看来自  Localizable.strings 文件中，错误描述的本地化版本，即在本地化字符串资源中描述的那样。
如果你需要像早前描述的那样，通过引用将错误传回，你的方法标识中还需要包含一个为指针设置的参数，用来指向一个 NSError 对象。你同时还需要利用返回的值指明是成功还是失败，像是这样：

    - (BOOL)doSomethingThatMayGenerateAnError:(NSError **)errorPtr;
当错误发生时，在返回表示失败的NO之前，你需要首先检查为错误参数提供的指针值是否为空（ NULL ），然后才能取消引用来设置错误信息：

    - (BOOL)doSomethingThatMayGenerateAnError:(NSError **)errorPtr {
    ...
    // error occurred
    if (errorPtr) {
        *errorPtr = [NSError errorWithDomain:...
                                        code:...
                                    userInfo:...];
      }
       return NO;
    }

##用于编程错误的异常

ObjC 用与其他编程语言类似的方式支持异常，并且与这些语言，如 C++ 、Java ，支持的语法也很相似。就像 NSError 一样，在 Cocoa  和  Cocoa Touch 里的异常，也是以 NSException 类实例为代表的对象。
如果你编写的代码可能导致异常抛出，你可以将这段代码封装在一个 try-catch 块内：

     @try {
        // do something that might throw an exception
    }
    @catch (NSException *exception) {
        // deal with the exception
    }
    @finally {
        // optional block of clean-up code
        // executed whether or not an exception occurred
    }

如果异常抛出发生在 @try 块内，那么它将会被 @catch 块捕捉，以方便你对它的处理。比如你在用使用异常作为错误处理的低级别C++ 库进行开发，你可能会捕捉到异常，并生成一个合适的 NSError 对象以向用户显示异常。
如果一个异常被抛出但并未被捕获，那么默认的未捕捉异常处理器（ uncaught exception handler ）将会加载一个可以控制并终止应用的消息。
你不应该使用 try-block 块来代替 ObjC 的标准程序检测，以 NSArray 为例，你应该总是先使用数组的数目（ count ）来确定元素的数量，然后才通过给定索引访问一个对象。如果你发出了越界访问请求，那么 objectAtIndex: 方法将会抛出一个异常，这样你就可以在早期的开发过程中及时发现 bug ——你应该尽量避免在你已经上线的 APP 中出现异常抛出。
更多关于 ObjC 应用中的异常，参看 [Exception Programming Topics](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Exceptions/Exceptions.html#//apple_ref/doc/uid/10000012i)
