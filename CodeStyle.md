[< Back](README.md)

# Objective-C 编码规范

## <a name='TOC'/></a>目录
* [命名](#naming)
  * [基本原则](#naming-basic-principle)
  * [命名空间](#namespace)
  * [方法名](#naming-method)
  * [协议名](#naming-protocol)
  * [通知命名](#naming-notifications)
  * [临时变量命名](#naming-temporary-variable)
  * [常量命名](#naming-constant)
  * [大小写](#naming-match-case)
  * [缩写](#abbreviation)
  * [其他](#naming-others)
* [代码格式化](#formatting)
  * [空格](#spaces)
  * [花括号](#braces)
  * [折行](#line-wrap)
* [代码组织](#code-organization)
* [类](#class)
  * [Property attributes](#property-attributes)
  * [类模板](#class-tmp)
* [注释](#comment)
  * [块注释](#block-comment) 
* [其他](#others)
  * [异常](#exception)
* [参考](#reference)

## <a name='naming'/></a>命名
### <a name='naming-basic-principle'></a>基本原则

* 仿照 Cocoa 风格来，使用长命名风格
* 变量命名推荐的命名语素顺序是：最开头是命名空间简写，然后越重要、区别度越大的语素越要往前放。经典的结构是：作用范围+限定修饰+类型。例：

```C
extern ushort APIDefaultPageSize;        // 还行，能明白意思了
extern ushort APIDefaultFetchPageSize;   // 加上些限定更好一些
extern ushort APIFetchPageSizeDefault;   // 再好些，把重要的往前放

YHToolbarComment    // 不推荐
YHCommentToolbar    // OK，把类型（toolbar）置后
```

### <a name='namespace'></a>命名空间
* 类名、protocols、C 函数、常量、结构体和枚举应带有命名空间前缀；
* 类方法不要带前缀，结构体字段也不要带前缀


### <a name='naming-method'></a>方法名
* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为。我们的项目中不要使用`new`创建对象。[^1]
* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

```Objective-C
// 来自官方文档
insertObject:atIndex:    // OK
insert:at:               // 不清晰，插入了什么？at 具体指哪里？
removeObjectAtIndex:     // OK
removeObject:            // OK
remove:                  // 糟糕，什么被移除了？
```

### <a name='naming-protocol'></a>协议名
好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。


### <a name='naming-notifications'></a>通知命名
基本命名格式是：`[与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification`，例：

```Objective-C
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```

### <a name='naming-temporary-variable'></a>临时变量命名
* 临时变量可以写得很短，如 i、k、vc 这样；
* 临时变量使用驼峰命名，以小写字母开头：

```Objective-C
int i = 0;
NSString *userName = @"name";
```


### <a name='naming-constant'></a>常量命名
除以上规则约定外，其他常量约定了以下前缀：

前缀   | 含义
-------|-----
k      | 宏常量
UDk    | User Default key

另见：[常量管理](#constant)

### <a name='naming-match-case'></a>大小写
* 类名采用大驼峰（`UpperCamelCase`）
* 类成员、方法小驼峰（`lowerCamelCase`）
* 局部变量大小写首选小驼峰，~~也可使用小写下划线的形式（`snake_case`）~~
* C函数的命名用大驼峰

### <a name='abbreviation'/></a>缩写
可以使用广泛使用的缩写，如 `URL`、`JSON`，并且缩写要大写。但像将`download`简写为`dl`这种是不可以的。


```Objective-C
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

destinationSelection       // OK
destSel                    // 糟糕
setBackgroundColor:        // OK
setBkgdColor:              // 糟糕
```

命名类名的时候尽量不要使用缩写，类名应该清晰

```
// 糟糕
@interface DPHomePageVC : UIViewController
@end

// 允许，但不够清晰
@interface DPHomePageController : UIViewController
@end

// OK
@interface DPHomePageViewController : UIViewController
@end
```

### <a name='naming-others'></a>其他
i，j专用于循环标号

为私有方法命名不要直接以`_`开头，而应以`命名空间_`开头，分类（Categories）中的方法也应以`命名空间_`开头。

## <a name='formatting'/></a>代码格式化
### <a name='spaces'></a>空格
类方法声明在方法类型与返回类型之间要有空格。

```Objective-C
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void)methodName:(NSString *)string;
```

指针符号`*`与变量直接不需要空格。

```Objective-C
// OK
NSString *name;
// 糟糕
NSString * name;
// 糟糕
NSString* name;
```

条件判断的括号内侧不应有空格。

```C
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
    // something
}
```

关系运算符（如 `>=`、`!=`）和逻辑运算符（如 `&&`、`||`）两边要有空格。

```C
// OK
(someValue > 100) ? YES : NO
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。

多个参数逗号后留一个空格（这也符合正常的西文语法）。

### <a name='braces'></a>花括号
方法的花括号推荐另起一行。方法内部需要写在一行。

```Objective-C
  - (void)methodName:(NSString *)string {
   ↑空格                              ↑空格，推荐花括号在一行
      if () {
   空格↑  ↑空格，花括号不要另起一行
      }
      else {
 要换行↑ ↑空格，花括号不要另起一行
      }    
  }
```

**动机**
> Xcode 默认的花括号位置是这样的：方法内部的各种补全都是在同一行的；方法定义的比较混乱，默认模版另起一行，但从 Interface Builder 中连线生成的方法在同一行的。
> 
> 考虑到 Xcode 的默认行为，方法内部要另起一行，方法所在行不强制定死。另外，模版可以定制，而 IB 生成的代码不可定制，所以不另起一行的写法优先。
>
> 另起一行的写法在代码折叠后非常难看。
> 

### <a name='line-wrap'></a>折行
与多数其他规范不同，不建议手动折行。

**动机**
> 手动折行的效果严重宽度依赖于窗口宽度——窗口过宽浪费宝贵的屏幕空间，较窄时可能无法阅读。而且 Xcode 自动折行的效果还是不错的。

<a name='code-organization'></a>代码组织
----
* 函数长度不要过长，尽量不要超过2/3屏幕，不要超过100行。
: 例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度。
* 单个文件方法数不应超过30个，可以通过分类（Categories），分层（ViewModel）等方式控制方法数
* 禁止出现超过两层循环的代码，用函数或block替代。

尽早返回错误：

```Objective-C
// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }
    
    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }
    
    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }
    
    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}
```

一个例子：

```Objective-C
@interface SomeObject : NSObject
          ↑          ↑ ↑
@property (nonatomic, copy) NSString *name;
                                    ↑ 
@property (nonatomic, weak) id<SomeDelegate> delegate;
         ↑           ↑     ↑                ↑
@end

@implementation SomeObject
@synthesize name = _name;

- (instancetype)init {
 ↑空格               ↑空格
    if (self = [super init]) {
      ↑     ↑ ↑             ↑
    }
    return self;
}

- (NSString *)defaultName {
    // 3种写法
    
    // 第一种
    if (self.name) {
      ↑           ↑
        return self.name;
    } else {
     ↑    ↑
        return @"defaultName";
    }
    
    // 第二种
    return self.name ?: @"defaultName";
          ↑         ↑  ↑
    
    // 第三种
    return self.name ? self.name : @"defaultName";
          ↑         ↑ ↑         ↑ ↑
}

@end
```


## <a name='class'/></a>类
禁止在类的 interface 中定义任何 iVar 成员，只允许使用属性，但可以在特定情形中使用属性生成的 iVar。

**尽量总是使用点操作符访问属性**，而不是属性生成的 iVar 变量。以下情形除外：

* 明确要避免修改产生 KVO 通知的；
* 需重写属性 getter 或 setter 的；
* 性能分析确定使用属性会导致性能不可接受的；
* 多线程环境中，为防止互斥一次进行多个修改的；
* init、dealloc 方法中，苹果官方文档中写到：Don’t Use Accessor Methods in Initializer Methods and dealloc（不要在 init 和 dealloc 方法中使用属性方法访问变量），这点要特别注意。

动机
> 如果使用 iVar，很多情况要特殊处理，容易出错。总是使用成员，规则简单，不易出问题。
> 
> 直接访问 iVar 的 block 会 retain iVar 所属的对象，这点很容易被忽略
>
> 定义和使用 iVar 都会产生编译警告，只不过默认设置没启用这两个警告

```Objective-C
@interface DPSomeClass : NSObject {
@private
    NSString *_name;	// 不要直接定义iVar
}
@property (nonatomic, copy) NSString *name;  // 定义属性
@end

@implementation DPSomeClass
@synthesize name = _name;	// 可以通过属性生成iVar，一般情况下，clang会自动帮属性生成iVar

- (void)doSomething {
    self.name = @"YourName";	// 推荐
    _name = @"YourName";	   // 不推荐，除非有特殊需求
}

@end

```

### <a name='property-attributes'></a>Property attributes

什么时候使用 copy？

* block 属性要定义成 copy。
* 当一个属性赋值后不期望改变时应当用 copy，最常见的类型如 NSString、NSURL。可变类型的成员，如 NSMutableArray、NSMutableDictionary 不能定成 copy 的。

相关 Demo 可在 https://github.com/BB9z/PropertyTest 获得。

### <a name='class-tmp'></a>类模板
类方法、属性划分一个明确，项目中使用统一的模板。比如声明一个UIViewController的子类，将函数按照功能做如下区分：

```Objective-C
#pragma mark - Lifecycle- (instancetype)init {
    if (self = [super init]) {
    }
    return self;
}
- (void)dealloc {
}
#pragma mark - View Lifecycle- (void)viewDidLoad {
    [super viewDidLoad];
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
}

- (void)viewWillDisappear:(BOOL)animated {
    [super viewWillDisappear:animated];
}
#pragma mark - Layout
- (void)setupProperty {
}
- (void)setupViewConstraints {
}

#pragma mark - Override#pragma mark - Public Interface

#pragma mark - Internal Interface

#pragma mark - Override#pragma mark - User Interaction- (void)foobarButtonTapped {
    // TODO:
}#pragma mark - Delegate
#pragma mark - UITableViewDelegate

#pragma mark - UITableViewDataSource
#pragma mark - Properties (getter, setter)

```

## <a name='constant'></a>常量
除非调试用的、控制不同编译模式行为的常量可用宏外，其他常量不得用宏定义。
可以通过 const 和 static 组合的方式代替宏定义的功能。

常量定义示例：

```
// 头文件
extern ushort APIFetchPageSizeDefault;    // 无const，可在外部修改

// 实现文件
ushort APIFetchPageSizeDefault = 10;

// Good
static const CGFloat kCellHeight = 30;

// Bad
#define kCellHeight 30
```

## <a name='comment'></a>注释
不用的代码直接删掉，不要注释，除非是少量的测试代码（这个也要尽量避免，可以通过单元测试去做）。现在项目中有大片的注释，都是直接将以前的代码直接注释掉了。

尽量让代码可以自表述，而不是依赖注释。

> 注释应该表达那些代码没有表达以及无法表达的东西。如果一段注释被用于解释一些本应该由这段代码自己表达的东西，我们就应该将这段注释看成一个改变代码结构或编码惯例直至代码可以自我表达的信号。我们重命名那些糟糕的方法和类名，而不是去修补。我们选择将长函数中的一些代码段抽取出来形成一些小函数，这些小函数的名字可以表述原代码段的意图，而不是对这些代码段进行注释。尽可能的通过代码进行表达。你通过代码所能表达的和你想要表达的所有事情之间的差额将为注释提供了一个合理的候选使用场合。对那些代码无法表达的东西进行注释，而不要仅简单地注释那些代码没有表达的东西。”[^2]

### <a name='block-comment'></a>块注释
方法内部禁止使用块注释。除非要临时注释大段代码，一般情况总应使用行注释。

**动机**
> 因为块注释不能正确嵌套。

## <a name='others'></a>其他
### <a name='exception'></a>异常
* 作为被调用模块的维护者，当被调用不当时（参数有问题、不和时宜），如何处理需要考虑（抛出异常还是返回错误状态）；
* 不要依赖 try catch，它不是代替你做检查、填补遗漏的工具。

### <a name='spaceline'></a>空行
* 方法实现之间要留一行空行
* 类定义前后要留一行空行
* 不要留过多的空行，空白行应该不超过2行
* 不要随意的留空行，经常在项目里看到，一个方法上来就好几行空行，然后才是实现代码，这些空行完全没有必要

---
## 写在后面：
看完这些后，希望你们看下[iOS应用开发之十大坑队友](http://www.iwangke.me/2014/09/04/fuck-your-ios-teamates/)，并在开发过程中注意。同时记住一句话，代码整理就像是个人卫生，你的代码风格能反映出你和你的工作风格。

[项目中的一些问题](StyleIssues.md)

<a name='reference'></a>参考
------
* [Objective-C 编码规范](https://github.com/lzyy/iOS-Team-Norms/blob/master/CodeStyle.md)  
* [iOS最佳实践](http://www.jianshu.com/p/b0bf2368fb95)  
* [Coding Guidelines for Cocoa](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Practical Memory Management](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html)

<a name='footnote'></a>
[^1]: [再谈ARC - 苹果核](http://pingguohe.net/2012/06/22/talk_arc_again/)  
[^2]: [只对代码无法表达的东西写注释 - Tony Bai](http://bigwhite.blogbus.com/logs/125602412.html)


