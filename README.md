
* [条件语句](#条件语句)
  * [尤达表达式](#尤达表达式)
  * [nil 和 BOOL 检查](#nil-和-bool-检查)
  * [黄金大道](#黄金大道)
  * [复杂的表达式](#复杂的表达式)
  * [三元运算符](#三元运算符)
  * [错误处理](#错误处理)
* [Case语句](#case语句)
    * [枚举类型](#枚举类型)
* [命名](#命名)
  * [通用的约定](#通用的约定)
  * [常量](#常量)
  * [方法](#方法)
  * [字面值](#字面值)
* [类](#类)
  * [类名](#类名)
  * [Initializer 和 dealloc](#initializer-和-dealloc)
    * [Designated 和 Secondary 初始化方法](#designated-和-secondary初始化方法)
      * [Designated Initializer](#designated-initializer)
      * [Secondary Initializer](#secondary-initializer)
        * [参考](#参考)
    * [instancetype](#instancetype)
      * [单例](#单例)
  * [属性](#属性)
      * [Init 和 Dealloc](#init-和-dealloc)
      * [点符号](#点符号)
    * [属性定义](#属性定义)
      * [私有属性](#私有属性)
    * [可变对象](#可变对象)
    * [懒加载 (Lazy Loading)](#懒加-（lazy-loading）)
* [代码格式](#代码格式)
    * [空格](#空格)
    * [换行](#换行)
    * [括号](#括号)
* [文件夹结构](#文件夹结构)
* [代码组织](#代码组织)



# 条件语句

条件语句体应该总是被大括号包围。

**推荐:**

```objective-c
if (!error) {
    return success;
}
```

**不推荐:**

```objective-c
if (!error)
    return success;
```

和

```objective-c
if (!error) return success;
```

## 尤达表达式

不要使用尤达表达式。尤达表达式是指，拿一个常量去和变量比较而不是拿变量去和常量比较。它就像是在表达 “蓝色是不是天空的颜色” 或者 “高个是不是这个男人的属性” 而不是  “天空是不是蓝的” 或者 “这个男人是不是高个子的” 

**推荐:**
```objective-c
if ([myValue isEqual:@42]) { ...
```

**不推荐:**  
```objective-c
if ([@42 isEqual:myValue]) { ...
```

## nil 和 BOOL 检查

**推荐:**
```objective-c
if (someObject) { ...
if (![someObject boolValue]) { ...
if (!someObject) { ...
```

**不推荐:**
```objective-c
if (someObject == YES) { ... // Wrong
if (myRawValue == YES) { ... // Never do this.
if ([someObject boolValue] == NO) { ...
```

## 黄金大道

在使用条件语句编程时，代码的左边距应该是一条“黄金”或者“快乐”的大道。 也就是说，不要嵌套 `if` 语句。使用多个 return 可以避免增加循环的复杂度，并提高代码的可读性。因为方法的重要部分没有嵌套在分支里面，并且你可以很清楚地找到相关的代码。

**推荐:**

```objective-c
- (void)someMethod {
  if (![someOther boolValue]) {
      return;
  }

  //Do something important
}
```

**不推荐:**

```objective-c
- (void)someMethod {
  if ([someOther boolValue]) {
    //Do something important
  }
}
```
## 复杂的表达式 

当你有一个复杂的 if 子句的时候，你应该把它们提取出来赋给一个 BOOL 变量，这样可以让逻辑更清楚，而且让每个子句的意义体现出来。


```objective-c
BOOL nameContainsSwift  = [sessionName containsString:@"Swift"];
BOOL isCurrentYear      = [sessionDateCompontents year] == 2014;
BOOL isSwiftSession     = nameContainsSwift && isCurrentYear;

if (isSwiftSession) {
    // Do something very cool
}
```

## 三元运算符


三元运算符 ? 应该只用在它能让代码更加清楚的地方。 一个条件语句的所有的变量应该是已经被求值了的。类似 if 语句，计算多个条件子句通常会让语句更加难以理解。或者可以把它们重构到实例变量里面。


**推荐:**
```objective-c
result = a > b ? x : y;
```

**不推荐:**
```objective-c
result = a > b ? x = c > d ? c : d : y;
```

当三元运算符的第二个参数（if 分支）返回和条件语句中已经检查的对象一样的对象的时候，下面的表达方式更灵巧：

**推荐:**
```objective-c
result = object ? : [self createObject];
```

**不推荐:**
```objective-c
result = object ? object : [self createObject];
```

## 错误处理

有些方法通通过参数返回 error 的引用，使用这样的方法时应当检查方法的返回值，而非 error 的引用。

**推荐:**
```objective-c
NSError *error = nil;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

此外，一些苹果的 API 在成功的情况下会对 error 参数（如果它非 NULL）写入垃圾值（garbage values），所以如果检查 error 的值可能导致错误 （甚至崩溃）。


# Case语句

除非编译器强制要求，括号在 case 语句里面是不必要的。但是当一个 case 包含了多行语句的时候，需要加上括号。


```objective-c
switch (condition) {
    case 1:
        // ...
        break;
    case 2: {
        // ...
        // Multi-line example using braces
        break;
       }
    case 3:
        // ...
        break;
    default: 
        // ...
        break;
}
```


有时候可以使用 fall-through 在不同的 case 里面执行同一段代码。一个 fall-through  是指移除 case 语句的 “break” 然后让下面的 case 继续执行。

```objective-c
switch (condition) {
    case 1:
    case 2:
        // code executed for values 1 and 2
        break;
    default: 
        // ...
        break;
}
```


当在 switch 语句里面使用一个可枚举的变量的时候，`default` 是不必要的。比如：

```objective-c
switch (menuType) {
    case ZOCEnumNone:
        // ...
        break;
    case ZOCEnumValue1:
        // ...
        break;
    case ZOCEnumValue2:
        // ...
        break;
}
```


此外，为了避免使用默认的 case，如果新的值加入到 enum，程序员会马上收到一个 warning 通知

`Enumeration value 'ZOCEnumValue3' not handled in switch.（枚举类型 'ZOCEnumValue3' 没有被 switch 处理）`

### 枚举类型


当使用 `enum` 的时候，建议使用新的固定的基础类型定义，因它有更强大的的类型检查和代码补全。 SDK 现在有一个 宏来鼓励和促进使用固定类型定义 - `NS_ENUM()`

**例子:**

```objective-c
typedef NS_ENUM(NSUInteger, ZOCMachineState) {
    ZOCMachineStateNone,
    ZOCMachineStateIdle,
    ZOCMachineStateRunning,
    ZOCMachineStatePaused
};
```

#  命名

##  通用的约定


尽可能遵守 Apple 的命名约定，尤其是和 [内存管理规则](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)) 相关的地方。

推荐使用长的、描述性的方法和变量名。

**推荐:**
```objective-c
UIButton *settingsButton;
```

**不推荐:**
```objective-c
UIButton *setBut;
```

##  常量

常量应该以驼峰法命名，并以相关类名作为前缀。

**推荐:**
```objective-c
static const NSTimeInterval ZOCSignInViewControllerFadeOutAnimationDuration = 0.4;
```

**不推荐:**
```objective-c
static const NSTimeInterval fadeOutTime = 0.4;
```

推荐使用常量来代替字符串字面值和数字，这样能够方便复用，而且可以快速修改而不需要查找和替换。常量应该用 `static` 声明为静态常量，而不要用 `#define`，除非它明确的作为一个宏来使用。

**推荐:**

```objective-c
static NSString * const ZOCCacheControllerDidClearCacheNotification = @"ZOCCacheControllerDidClearCacheNotification";
static const CGFloat ZOCImageThumbnailHeight = 50.0f;
```

**不推荐:**

```objective-c
#define CompanyName @"Apple Inc."
#define magicNumber 42
```

常量应该在头文件中以这样的形式暴露给外部：

```objective-c
extern NSString *const ZOCCacheControllerDidClearCacheNotification;
```
并在实现文件中为它赋值。

只有公有的常量才需要添加命名空间作为前缀。尽管实现文件中私有常量的命名可以遵循另外一种模式，你仍旧可以遵循这个规则。


##  方法

方法名与方法类型 (`-`/`+` 符号)之间应该以空格间隔。方法段之间也应该以空格间隔（以符合 Apple 风格）。参数前应该总是有一个描述性的关键词。

尽可能少用 "and" 这个词。它不应该用来阐明有多个参数，比如下面的 `initWithWidth:height:` 这个例子：

**推荐:**
```objective-c
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**不推荐:**

```objective-c
- (void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

##  字面值

使用字面值来创建不可变的 `NSString`, `NSDictionary`, `NSArray`, 和 `NSNumber` 对象。注意不要将 `nil` 传进 `NSArray` 和 `NSDictionary` 里，因为这样会导致崩溃。

**例子：**

```objective-c
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**不要这样:**

```objective-c
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

如果要用到这些类的可变副本，我们推荐使用 `NSMutableArray`, `NSMutableString` 这样的类。

**应该避免**下面这样:

```objective-c
NSMutableArray *aMutableArray = [@[] mutableCopy];
```

上面这种书写方式的效率和可读性的都存在问题。


#  类 

##  类名

类名应该以大写字母作为前缀（除了 Apple 预留的类前缀）， 如MG。

一个好的类的命名规范：当你创建一个子类的时候，你应该把说明性的部分放在前缀和父类名的在中间。

举个例子：如果你有一个 `ZOCNetworkClient` 类，子类的名字会是`ZOCTwitterNetworkClient` (注意 "Twitter" 在 "ZOC" 和 "NetworkClient" 之间); 按照这个约定， 一个`UIViewController` 的子类会是 `ZOCTimelineViewController`.


## Initializer 和 dealloc 

推荐的代码组织方式是将 `dealloc` 方法放在实现文件的最前面（直接在  `@synthesize` 以及 `@dynamic` 之后），`init` 应该跟在 `dealloc` 方法后面。

如果有多个初始化方法， 指定初始化方法 (designated initializer) 应该放在最前面，间接初始化方法 (secondary initializer) 跟在后面，这样更有逻辑性。如今有了 ARC，dealloc 方法几乎不需要实现，不过把 init 和 dealloc 放在一起可以从视觉上强调它们是一对的。通常，在 init 方法中做的事情需要在 dealloc 方法中撤销。

`init` 方法应该是这样的结构：

```objective-c
- (instancetype)init
{
    self = [super init]; // call the designated initializer
    if (self) {
        // Custom initialization
    }
    return self;
}
```

### Designated 和 Secondary 初始化方法

Objective-C 有指定初始化方法(designated initializer)和间接(secondary initializer)初始化方法的观念。
designated 初始化方法是提供所有的参数，secondary 初始化方法是一个或多个，并且提供一个或者更多的默认参数来调用 designated 初始化的初始化方法。

```objective-c
@implementation ZOCEvent

- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
                     location:(CLLocation *)location
{
    self = [super init];
    if (self) {
        _title    = title;
        _date     = date;
        _location = location;
    }
    return self;
}

- (instancetype)initWithTitle:(NSString *)title
                         date:(NSDate *)date
{
    return [self initWithTitle:title date:date location:nil];
}

- (instancetype)initWithTitle:(NSString *)title
{
    return [self initWithTitle:title date:[NSDate date] location:nil];
}

@end
```

`initWithTitle:date:location:` 就是 designated 初始化方法，另外的两个是 secondary 初始化方法。因为它们仅仅是调用类实现的 designated 初始化方法


### instancetype 

一个相关的返回类型可以明确地规定用 `instancetype` 关键字作为返回类型，并且它可以在一些工厂方法或者构造器方法的场景下很有用。它可以提示编译器正确地检查类型，并且更加重要的是，这同时适用于它的子类。

```objective-c
@interface ZOCPerson
+ (instancetype)personWithName:(NSString *)name;
@end
```


####   单例

如果可能，请尽量避免使用单例而是依赖注入。
然而，如果一定要用，请使用一个线程安全的模式来创建共享的实例。对于 GCD，用 `dispatch_once()` 函数就可以咯。


```objective-c
+ (instancetype)sharedInstance
{
   static id sharedInstance = nil;
   static dispatch_once_t onceToken = 0;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });
   return sharedInstance;
}
```


使用 dispatch_once()，来控制代码同步，取代了原来的约定俗成的用法。


```objective-c
+ (instancetype)sharedInstance
{
    static id sharedInstance;
    @synchronized(self) {
        if (sharedInstance == nil) {
            sharedInstance = [[MyClass alloc] init];
        }
    }
    return sharedInstance;
}
```

##  属性

属性应该尽可能描述性地命名，避免缩写，并且是小写字母开头的驼峰命名。

**例子 :**
```objective-c
NSString *text;
```

**不要这样 :**
```objective-c
NSString* text;
NSString * text;
```

### 属性定义


推荐按照下面的格式来定义属性

```objective-c
@property (nonatomic, readwrite, copy) NSString *name;
```

属性的参数应该按照下面的顺序排列： 原子性，读写 和 内存管理。 这样做你的属性更容易修改正确，并且更好阅读。(译者注：习惯上修改某个属性的修饰符时，一般从属性名从右向左搜索需要修动的修饰符。最可能从最右边开始修改这些属性的修饰符，根据经验这些修饰符被修改的可能性从高到底应为：内存管理 > 读写权限 >原子操作)


你必须使用 `nonatomic`，除非特别需要的情况。在iOS中，`atomic`带来的锁特别影响性能。

属性可以存储一个代码块。为了让它存活到定义的块的结束，必须使用 `copy` （block 最早在栈里面创建，使用 `copy`让 block 拷贝到堆里面去）


为了完成一个共有的 getter 和一个私有的 setter，你应该声明公开的属性为 `readonly`  并且在类扩展总重新定义通用的属性为 `readwrite` 的。

```objective-c
//.h文件中
@interface MyClass : NSObject
@property (nonatomic, readonly, strong) NSObject *object;
@end
//.m文件中
@interface MyClass ()
@property (nonatomic, readwrite, strong) NSObject *object;
@end

@implementation MyClass
//Do Something cool
@end

```

描述`BOOL`属性的词如果是形容词，那么setter不应该带`is`前缀，但它对应的 getter 访问器应该带上这个前缀，如：

```objective-c
@property (assign, getter=isEditable) BOOL editable;
```

文字和例子引用自 [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)。

在实现文件中应避免使用`@synthesize`,因为Xcode已经自动为你添加了。

#### 私有属性


私有属性应该定义在类的实现文件的类的扩展 (class extensions) 中。不允许在有名字的的 category(如 `ZOCPrivate`）中定义私有属性，除非你扩展其他类。

**例子:**

```objective-c
@interface ZOCViewController ()
@property (nonatomic, strong) UIView *bannerView;
@end
```

### 可变对象


任何可以用来用一个可变的对象设置的（(比如 `NSString`,`NSArray`,`NSURLRequest`)）属性的的内存管理类型必须是 `copy` 的。

这是为了确保防止在不明确的情况下修改被封装好的对象的值(译者注：比如执行 array(定义为 copy 的 NSArray 实例) = mutableArray，copy 属性会让 array 的 setter 方法为 array = [mutableArray copy], [mutableArray copy] 返回的是不可变的 NSArray 实例，就保证了正确性。用其他属性修饰符修饰，容易在直接赋值的时候，array 指向的是 NSMuatbleArray 的实例，在之后可以随意改变它的值，就容易出错)。

你应该同时避免暴露在公开的接口中可变的对象，因为这允许你的类的使用者改变类自己的内部表示并且破坏类的封装。你可以提供可以只读的属性来返回你对象的不可变的副本。

```objective-c
/* .h */
@property (nonatomic, readonly) NSArray *elements

/* .m */
- (NSArray *)elements {
  return [self.mutableElements copy];
}
```

### 懒加载（Lazy Loading）

当实例化一个对象需要耗费很多资源，或者配置一次就要调用很多配置相关的方法而你又不想弄乱这些方法时，我们需要重写 getter 方法以延迟实例化，而不是在 init 方法里给对象分配内存。通常这种操作使用下面这样的模板：


```objective-c

- (NSDateFormatter *)dateFormatter {
  if (!_dateFormatter) {
    _dateFormatter = [[NSDateFormatter alloc] init];
        NSLocale *enUSPOSIXLocale = [[NSLocale alloc] initWithLocaleIdentifier:@"en_US_POSIX"];
        [_dateFormatter setLocale:enUSPOSIXLocale];
        [_dateFormatter setDateFormat:@"yyyy-MM-dd'T'HH:mm:ss.SSS"];//毫秒是SSS，而非SSSSS
  }
  return _dateFormatter;
}

```

# 代码格式

###  空格

* 缩进使用 4 个空格。 永远不要使用  tab, 确保你在 Xcode 的设置里面是这样设置的。
* 方法的大括号和其他的大括号(`if`/`else`/`switch`/`while` 等) 总是在同一行开始，在新起一行结束。

**推荐:**

```objective-c
if (user.isHappy) {
    //Do something
}
else {
    //Do something else
}
```

**不推荐:**

```objective-c
if (user.isHappy)
{
  //Do something
} else {
  //Do something else
}
```



* 方法之间应该要有一个空行来帮助代码看起来清晰且有组织。 方法内的空格应该用来分离功能，但是通常不同的功能应该用新的方法来定义。
* 优先使用 auto-synthesis。但是如果必要的话， `@synthesize` and `@dynamic` 
* 在实现文件中的声明应该新起一行。
* 应该总是让冒号对齐。有一些方法签名可能超过三个冒号，用冒号对齐可以让代码更具有可读性。即使有代码块存在，也应该用冒号对齐方法。


**推荐:**

```objective-c
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];

```


**不推荐:**

```objective-c
[UIView animateWithDuration:1.0 animations:^{
    // something 
} completion:^(BOOL finished) {
    // something
}];
```

如果自动对齐让可读性变得糟糕，那么应该在之前把 block 定义为变量，或者重新考虑你的代码签名设计。

###  换行

本指南关注代码显示效果以及在线浏览的可读性，所以换行是一个重要的主题。

举个例子：

```objective-c
self.productsRequest = [[SKProductsRequest alloc] initWithProductIdentifiers:productIdentifiers];
```

一个像上面的长行的代码在第二行以一个间隔（2个空格）延续

```objective-c
self.productsRequest = [[SKProductsRequest alloc] 
  initWithProductIdentifiers:productIdentifiers];
```

###  括号


在以下的地方使用 [Egyptian风格 括号](https://en.wikipedia.org/wiki/Indent_style#K.26R_style) （译者注：又称 K&R 风格，代码段括号的开始位于一行的末尾，而不是另外起一行的风格。关于为什么叫做 Egyptian Brackets，可以参考 <http://blog.codinghorror.com/new-programming-jargon/> )

* 控制语句 (if-else, for, switch)

非 Egyptian 括号可以用在：

* 类的实现（如果存在)
* 方法的实现

#文件夹结构
基本使用MVC构架,使用cocosPod管理三方库,凡cocosPod能搜到的使用cocosPod管理,GitHub Star少于2000的三方框架不考虑使用.
目录结构
一级目录

|  目录名 | 说明 |
| --- | --- |
|  Main | 项目主文件夹  |
|  Singleton | 项目中所用到的单利 |
|  Category | 项目中所用到的类目|
|  Expand  | 项目中全局所用到字符串常量的定义,枚举,以及全局引用的头文件,Pch文件等 |
|  Category | 项目中所用到的类目|
|ThreeFramework|cocosPod不存在的三方框架|
|SupportingFiles|支持文件,例如.plist,Main等文件|

-------

Main下二级目录 以大功能区分,

-------

Main下三级目录 具体到ViewController或者小功能(如地址管理,订单管理),最好具体到ViewController ,另外包含View,Model文件夹  View下包含SubView,以及CellView文件夹.

-------

# <h1 id="代码组织">代码组织</h1>

## viewController的代码结构 

```objective-c

@property (strong, nonatomic) UIButton *confirmButton;
...

- (void)dealloc { /* ... */ }
- (instancetype)init { /* ... */ }

#pragma mark - View Lifecycle （View 的生命周期）

- (void)viewDidLoad { /* ... */ }
- (void)viewWillAppear:(BOOL)animated { /* ... */ }
- (void)didReceiveMemoryWarning { /* ... */ }

#pragma mark - UITableViewDelegate

#pragma mark - CustomDelegate

#pragma mark - Event Response

#pragma mark - Public 

- (void)publicMethod { /* ... */ }

#pragma mark - Private

- (void)privateMethod { /* ... */ }

#pragma mark - IBActions  

- (IBAction)submitData:(id)sender { /* ... */ }

#pragma mark - Getter and Setter

- (UIButton *)confirmButton
- (UITableView *)tableView

                        
```

## 其他
* .h 文件尽量暴露对外的方法和对象，内部使用的方法和对象放到实现文件中


