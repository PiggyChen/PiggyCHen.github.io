---
title: Objective-C runtime
date: 2017-05-25 20:05:29
meta: [iOS,Objective-C,Runtime]
tags: [iOS,Objective-C,Runtime]
---
Objective-C是基于C语言加入了面向对象特性和消息转发机制的动态语言，这意味着它不仅需要一个编译器，还需要runtime系统来动态创建类和对象，进行消息发送和转发。

<!-- more --> 

### 什么是runtime
简单来说，runtime就是一组C语言的函数，我们写的代码在程序运行过程中都会被转化成runtime的C代码执行，例如`[target doSomething]`会被转化成`objc_msgSend(target, @selector(doSomething))`。
OC中一切都被设计成了对象，我们都知道一个类被初始化成一个实例，这个实例是一个对象。实际上一个类本质上也是一个对象，在runtime中用结构体表示。

### runtime数据结构
在Objective-C中，使用`[receiver message]`语法并不会马上执行receiver对象的message方法的代码，而是向receiver发送一条message消息，这条消息可能由receiver来处理，也可能由转发给其他对象来处理，也有可能假装没有接收到这条消息而没有处理。其实`[receiver message]`被编译器转化为：

```
id objc_msgSend ( id self, SEL op, ... ); 
```
下面从数据结构id来逐步分析和理解runtime有哪些重要的数据结构。

**id**

在OC中，id是通用类型指针，能够表示任何继承自NSObject的对象，id的数据结构为：

```
/// Represents an instance of a class.  
struct objc_object {  
    Class isa  OBJC_ISA_AVAILABILITY;  
};  
/// A pointer to an instance of a class.  
typedef struct objc_object *id;
```
可以看出，id其实就是一个指向objc_object结构体指针，它包含一个Class isa成员，根据isa指针就能够找出对象所属的类。

**Class**

isa指针的数据类型是Class，Class表示对象所属的类，其数据结构为：

```
struct objc_class {  
    Class isa  OBJC_ISA_AVAILABILITY;  
#if !__OBJC2__  
    Class super_class                                        OBJC2_UNAVAILABLE;  
    const char *name                                         OBJC2_UNAVAILABLE;  
    long version                                             OBJC2_UNAVAILABLE;  
    long info                                                OBJC2_UNAVAILABLE;  
    long instance_size                                       OBJC2_UNAVAILABLE;  
    struct objc_ivar_list *ivars                             OBJC2_UNAVAILABLE;  
    struct objc_method_list **methodLists                    OBJC2_UNAVAILABLE;  
    struct objc_cache *cache                                 OBJC2_UNAVAILABLE;  
    struct objc_protocol_list *protocols                     OBJC2_UNAVAILABLE;  
#endif  
} OBJC2_UNAVAILABLE;  
/// An opaque type that represents an Objective-C class.  
typedef struct objc_class *Class;
```
可以查看到Class其实就是一个objc_class结构体指针，该结构体中真正存储了我们在代码中获取类型，访问变量，调用方法和协议所用到的相关信息，下面就分析一下一些重要的成员变量表示什么意思以及使用的数据结构。

* **isa:**

isa表示一个Class对象的Class，也就是Meta Class。在面向对象设计中，一切都是对象，Class在设计中本身也是一个对象。
其实Meta Class也是一个Class，它也跟其他Class一样有自己的isa和super_class指针，关系如下:
<center>![Alt Image Text](/img/runtime-meta-class.jpg)</center>

* **super_class:** 表示实例对象对应的父类；
* **name:** 表示类名；
* **ivars:** 表示多个成员变量，它指向objc_ivar_list结构体,其定义为：

```
struct objc_ivar_list {  
    int ivar_count                                           OBJC2_UNAVAILABLE;  
#ifdef __LP64__  
    int space                                                OBJC2_UNAVAILABLE;  
#endif  
    /* variable length structure */  
    struct objc_ivar ivar_list[1]                            OBJC2_UNAVAILABLE;  
} 
```
objc_ivar_list其实就是一个链表，存储多个objc_ivar，而objc_ivar结构体存储类的单个成员变量信息。

* **methodLists:** 表示方法列表；

它指向objc_method_list结构体的二级指针，可以动态修改*methodLists的值来添加成员方法，也是Category实现原理，同样也解释Category不能添加属性的原因，其定义为：

```
struct objc_method_list {  
struct objc_method_list *obsolete                        OBJC2_UNAVAILABLE;  
    int method_count                                         OBJC2_UNAVAILABLE;  
#ifdef __LP64__  
    int space                                                OBJC2_UNAVAILABLE;  
#endif  
    /* variable length structure */  
    struct objc_method method_list[1]                        OBJC2_UNAVAILABLE;  
}
```
objc_method_list也是一个链表，存储多个objc_method，而objc_method结构体存储类的某个方法的信息。

* **cache:** 用来缓存经常访问的方法，它指向objc_cache结构体，后面会重点讲到。
* **protocols:** 表示类遵循哪些协议。

**Method**

Method表示类中的某个方法，其数据结构为：

```
/// An opaque type that represents a method in a class definition.  
typedef struct objc_method *Method;  
struct objc_method {  
    SEL method_name                                          OBJC2_UNAVAILABLE;  
    char *method_types                                       OBJC2_UNAVAILABLE;  
    IMP method_imp                                           OBJC2_UNAVAILABLE;  
}
```
其实Method就是一个指向objc_method结构体指针，它存储了方法名(method_name)、方法类型(method_types)和方法实现(method_imp)等信息。而method_imp的数据类型是IMP，它是一个函数指针，后面会重点提及。

**Ivar**

Ivar表示类中的实例变量，数据结构为：

```
/// An opaque type that represents an instance variable.  
typedef struct objc_ivar *Ivar;  
struct objc_ivar {  
    char *ivar_name                                          OBJC2_UNAVAILABLE;  
    char *ivar_type                                          OBJC2_UNAVAILABLE;  
    int ivar_offset                                          OBJC2_UNAVAILABLE;  
#ifdef __LP64__  
    int space                                                OBJC2_UNAVAILABLE;  
#endif  
} 
```
Ivar其实就是一个指向objc_ivar结构体指针，它包含了变量名(ivar_name)、变量类型(ivar_type)等信息。

**IMP**

IMP本质上就是一个函数指针，指向方法的实现，在objc.h找到它的定义：

```
/// A pointer to the function of a method implementation.   
#if !OBJC_OLD_DISPATCH_PROTOTYPES  
typedef void (*IMP)(void /* id, SEL, ... */ );   
#else  
typedef id (*IMP)(id, SEL, ...);   
#endif  
```
当你向某个对象发送一条信息，可以由这个函数指针来指定方法的实现，它最终就会执行那段代码，这样可以绕开消息传递阶段而去执行另一个方法实现。

**Cache**

顾名思义，Cache主要用来缓存，那它缓存什么呢？我们先在runtime.h文件看看它的定义：

```
typedef struct objc_cache *Cache                             OBJC2_UNAVAILABLE;  
struct objc_cache {  
    unsigned int mask /* total = mask + 1 */                 OBJC2_UNAVAILABLE;  
    unsigned int occupied                                    OBJC2_UNAVAILABLE;  
    Method buckets[1]                                        OBJC2_UNAVAILABLE;  
};
```
Cache其实就是一个存储Method的链表，主要是为了优化方法调用的性能。当对象receiver调用方法message时，首先根据对象receiver的isa指针查找到它对应的类，然后在类的methodLists中搜索方法，如果没有找到，就使用super_class指针到父类中的methodLists查找，一旦找到就调用方法。如果没有找到，有可能消息转发，也可能忽略它。但这样查找方式效率太低，因为往往一个类大概只有20%的方法经常被调用，占总调用次数的80%。所以使用Cache来缓存经常调用的方法，当调用方法时，优先在Cache查找，如果没有找到，再到methodLists查找。

### runtime在开发中的用途
有了上面对runtime数据结构的了解，其实对于一个Objective-C中的对象，在编译后，就会转化为runtime中的objc_object，而objc_object结构体中的isa成员变量又最终存储了一个类所包含的各种信息，包括成员变量、方法列表、协议列表以及方法缓存。
因此，利用runtime我们可以动态的获取变量成员列表或者属性列表，可以动态的给类添加新的属性或方法，可以进行方法的解析、交换以及消息转发等等，其实本质上都是在操作isa变量中存储的各类信息。

1.获取类的属性列表、方法列表、成员变量列表以及协议列表

```
unsigned int count;
//获取属性列表
objc_property_t *propertyList = class_copyPropertyList([self class], &count);
for (unsigned int i=0; i<count; i++) {
    const char *propertyName = property_getName(propertyList[i]);
    NSLog(@"property---->%@", [NSString stringWithUTF8String:propertyName]);
}
//获取方法列表
Method *methodList = class_copyMethodList([self class], &count);
for (unsigned int i; i<count; i++) {
    Method method = methodList[i];
    NSLog(@"method---->%@", NSStringFromSelector(method_getName(method)));
}
//获取成员变量列表
Ivar *ivarList = class_copyIvarList([self class], &count);
for (unsigned int i; i<count; i++) {
    Ivar myIvar = ivarList[i];
    const char *ivarName = ivar_getName(myIvar);
    NSLog(@"Ivar---->%@", [NSString stringWithUTF8String:ivarName]);
}
//获取协议列表
__unsafe_unretained Protocol **protocolList = class_copyProtocolList([self class], &count);
for (unsigned int i; i<count; i++) {
    Protocol *myProtocal = protocolList[i];
    const char *protocolName = protocol_getName(myProtocal);
    NSLog(@"protocol---->%@", [NSString stringWithUTF8String:protocolName]);
}
```

2.动态添加属性

```
//首先定义一个全局变量，用它的地址作为关联对象的key
static char associatedObjectKey;
//设置关联对象
objc_setAssociatedObject(target, &associatedObjectKey, @"添加的字符串属性", OBJC_ASSOCIATION_RETAIN_NONATOMIC); //获取关联对象
NSString *string = objc_getAssociatedObject(target, &associatedObjectKey);
NSLog(@"AssociatedObject = %@", string);
```

3.动态添加方法

```
void runAddMethod(id self, SEL _cmd, NSString *string){
    NSLog(@"add C IMP ", string);
}
+ (BOOL)resolveInstanceMethod:(SEL)sel{
    //给本类动态添加一个方法
    if ([NSStringFromSelector(sel) isEqualToString:@"resolveAdd:"]) {
        class_addMethod(self, sel, (IMP)runAddMethod, "v@:*");
    }
    return YES;
}
```

4.方法交换

```
@implementation UIViewController (swizzling)
//load方法会在类第一次加载的时候被调用
//调用的时间比较靠前，适合在这个方法里做方法交换
+ (void)load{
    //方法交换应该被保证，在程序中只会执行一次
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{

        //获得viewController的生命周期方法的selector
        SEL systemSel = @selector(viewWillAppear:);
        //自己实现的将要被交换的方法的selector
        SEL swizzSel = @selector(swiz_viewWillAppear:);
        //两个方法的Method
        Method systemMethod = class_getInstanceMethod([self class], systemSel);
        Method swizzMethod = class_getInstanceMethod([self class], swizzSel);

        //首先动态添加方法，实现是被交换的方法，返回值表示添加成功还是失败
        BOOL isAdd = class_addMethod(self, systemSel, method_getImplementation(swizzMethod), method_getTypeEncoding(swizzMethod));
        if (isAdd) {
            //如果成功，说明类中不存在这个方法的实现
            //将被交换方法的实现替换到这个并不存在的实现
            class_replaceMethod(self, swizzSel, method_getImplementation(systemMethod), method_getTypeEncoding(systemMethod));
        }else{
            //否则，交换两个方法的实现
            method_exchangeImplementations(systemMethod, swizzMethod);
        }
    });
}
- (void)swiz_viewWillAppear:(BOOL)animated{
    //这时候调用自己，看起来像是死循环
    //但是其实自己的实现已经被替换了
    [self swiz_viewWillAppear:animated];
    NSLog(@"swizzle");
}
@end
```

### 总结
其实我个人在工作中用到runtime的机会很少，也就是在category中给类添加属性时候用到。但是，当深入的去理解了runtime的数据结构以及消息转发机制和原理后，至少对于Objective-C的所谓的动态语言特性有了进一步的理解。同时在阅读开源项目时发现，大部分的json转模型的开源库也都用到了runtime，这也使得阅读和学习开源项目更加容易了。

### 参考资料
1.[深入理解Objective-C的Runtime机制](http://www.csdn.net/article/2015-07-06/2825133-objective-c-runtime/6)
2.[iOS中的runtime应用](http://www.jianshu.com/p/364eab29f4f5)
3.[理解 Objective-C Runtime](http://www.cocoachina.com/ios/20141008/9844.html)









