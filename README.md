# Objective-C Style Guide

## 介绍

苹果关于 style guide 的文档：

* [The Objective-C Programming Language][Introduction_1]
* [Cocoa Fundamentals Guide][Introduction_2]
* [Coding Guidelines for Cocoa][Introduction_3]
* [iOS App Programming Guide][Introduction_4]

[Introduction_1]:https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/Introduction/Introduction.html

[Introduction_2]:https://developer.apple.com/legacy/library/documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html

[Introduction_3]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html

[Introduction_4]:http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html

本文遵守 IETF 的 [RFC 2119](http://tools.ietf.org/html/rfc2119)。简单介绍一下各关键字的意思，用于对照：

1. **MUST / REQUIRED / SHALL**    
   表示绝对要求这样做。
2. **MUST NOT / SHALL NOT**    
   表示绝对不要求这样做。
3. **SHOULD / RECOMMENDED**    
   表示一般情况下应该这样做，但是在某些特定情况下可以忽视这个要求。
4. **SHOULD NOT / NOT RECOMMENDED**    
   表示一般情况下不应该这样做，但是在某些特定情况下可以忽视这个要求。
5. **MAY / OPTIONAL**    
   表示这个要求完全是可选的，你可以这样做，也可以不这样做。

## 目录

* [点语法](#点语法)
* [间距](#间距)
* [条件判断](#条件判断)
  * [三目运算符](#三目运算符)
* [错误处理](#错误处理)
* [方法](#方法)
* [变量](#变量)
* [命名](#命名)
  * [Categories](#Categories)
* [注释](#注释)
* [Init 和 Dealloc](#init-和-dealloc)
* [字面量](#字面量)
* [CGRect 函数](#CGRect-函数)
* [常量](#常量)
* [枚举类型](#枚举类型)
* [位掩码](#位掩码)
* [私有属性](#私有属性)
* [图片命名](#图片命名)
* [布尔](#布尔)
* [单例](#单例)
* [导入](#导入)
* [协议](#协议)
* [Xcode 工程](#Xcode-工程)

## 点语法

**RECOMMENDED** 使用点语法来访问或者修改属性，使用方括号来调用方法。

**推荐：**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**反对：**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## 间距

* **MUST** 一个缩进使用 4 个空格，永远不要使用制表符（tab）缩进。请确保在 Xcode 中设置了此偏好。
* **MUST** 方法的大括号和其他关键字的大括号（`if`/`else`/`switch`/`while` 等等）始终和声明在同一行开始，在新的一行结束。

**推荐：**
```objc
if (user.isHappy) {
    // Do something
}
else {
    // Do something else
}
```

* **SHOULD** 方法之间应该正好空一行，这有助于视觉清晰度和代码组织性。
* **MAY** 方法中的功能块之间应该使用空白分开，但往往可能应该拆成几个子方法。
* **MAY** 如果方法名过长，可以在方法名和方法体之间加一个空行，在视觉上进行分隔。
* **MUST** `@synthesize` 和 `@dynamic` 在实现中每个都应该占一个新行。

## 条件判断

**MUST** 条件判断主体部分应该始终使用大括号括住来防止[出错][Condiationals_1]，即使它可以不用大括号（例如它只需要一行）。这些错误包括添加第二行（代码）并希望它是 if 语句的一部分时。还有另外一种[更危险的][Condiationals_2]，当 if 语句里面的一行被注释掉，下一行就会在不经意间成为了这个 if 语句的一部分。此外，这种风格也更符合所有其他的条件判断，因此也更容易检查。

**推荐：**
```objc
if (!error) {
    return success;
}
```

**反对：**
```objc
if (!error)
    return success;
```

或

```objc
if (!error) return success;
```

[Condiationals_1]:(https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256)
[Condiationals_2]:http://programmers.stackexchange.com/a/16530

### 三目运算符

**SHOULD** 三目运算符`?`的作用是让代码更清晰或更整洁。应该只在单一条件时使用。多条件时通常使用 if 语句会更易懂，或者重构为带名字的变量。

**推荐：**
```objc
result = a > b ? x : y;
```

**反对：**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## 错误处理

**MUST** 当方法通过错误参数的引用返回错误时，必须先检查方法的返回值，一定不要先检查错误参数。

**推荐：**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // 处理错误
}
```

**反对：**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // 处理错误
}
```

一些苹果的 API 在成功的情况下会写一些垃圾值给错误参数（如果非空），所以针对错误变量可能会造成虚假结果（以及接下来的崩溃）。

## 方法

* **SHOULD** 在方法签名中，在 `-`/`+` 符号后应该有一个空格。
* **SHOULD** 方法片段之间也应该有一个空格。

**推荐：**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
* **SHOULD** 方法超过 80 个字符应该折行显示，每行一个参数。

**推荐：**
```objc
- (void)setExampleText:(NSString *)text
                 image:(UIImage *)image
                 color:(UIColor *)color
       alternativeText:(NSString *)altText;
```

## 变量

**SHOULD** 变量的命名应该具有描述性，能清楚地表示这个变量是什么，以及开发者要正确使用它应该知道的相关信息。

**例如**

* `NSString *title`: 从 “title” 这个名字可以推断出它是一个字符串。
* `NSString *titleHTML`: 它表示标题可能包含需要解析的 HTML。开发者知道 HTML 这一信息可以更有效地使用该变量。
* `NSAttributedString *titleAttributedString`: 一个带格式的标题。`AttributedString`表示它不只是一个文本标题，添加带格式的标题可能需要依赖上下文。
* `NSDate *now`: 无需额外说明。
* `NSDate *lastModifiedDate`: 只有`lastModified`是意思不明确的；可被理解为多种类型。
* `NSURL *URL` vs. `NSString *URLString`: 当变量名可以表示另一种类型，应该加入类型信息来消除歧义。
* `NSString *releaseDateString`: 同上。

**NOT RECOMMENDED** 除了循环中简单的计数变量外，其他情况都应该避免使用单字母的变量名。

**MUST** 星号表示的指针类型必须跟随变量，**例如**：`NSString *text` **不要写成** `NSString* text` 或者 `NSString * text`，常量除外（`NSString * const NYTConstantString`）。

**SHOULD** 尽量定义属性来代替直接使用实例变量。除了初始化方法（`init`， `initWithCoder:`，等），`dealloc` 方法和自定义的 setters 和 getters 内部，应避免直接访问实例变量。更多有关在初始化方法和 dealloc 方法中使用访问器方法的信息，参见[这里][Variables_1]。

**推荐：**

```objc
@interface NYTSection : NSObject

@property (nonatomic) NSString *headline;

@end
```

**反对：**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

[Variables_1]:https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6

#### 变量限定符

**SHOULD** 当涉及到[在 ARC 中被引入][Variable_Qualifiers_1]变量限定符时，限定符 (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) 应该位于星号和变量名之间，如：`NSString * __weak text`。

[Variable_Qualifiers_1]:(https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4)

## 命名

**SHOULD** 尽可能遵守苹果的命名约定，尤其那些涉及到[内存管理规则][Naming_1]（[NARC][Naming_2]）的。

使用长的、描述性的方法名和变量名是好的做法。

**推荐：**

```objc
UIButton *settingsButton;
```

**反对：**

```objc
UIButton *setBut;
```

**MUST** 类名和常量必须使用三个字母的前缀（例如 `TBK`），但 Core Data 实体名可以省略前缀。常量必须使用驼峰命名法，并以相关类的名字作为前缀。两个字母的前缀（如`NS`）由[Apple 保留使用权][Naming_3]。

**推荐：**

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**反对：**

```objc
static const NSTimeInterval fadetime = 1.7;
```

**MUST** 属性和局部变量应该使用驼峰命名法并且首字母小写。

**MUST** 为了保持一致，实例变量应该使用驼峰命名法命名，并且首字母小写，以下划线为前缀。这与 LLVM 自动合成的实例变量相一致。
**如果 LLVM 可以自动合成变量，那就让它自动合成。**

**推荐：**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**反对：**

```objc
id varnm;
```

[Naming_1]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html

[Naming_2]:http://stackoverflow.com/a/2865194/340508

[Naming_3]:https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW12

### Categories

**RECOMMENDED** Category 应该用于将功能分段，它的名字应该描述这部分功能。

**For example:**

```objc
@interface UIViewController (NYTMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Not:**

```objc
@interface NYTAdvertisement (private)
@interface NSString (NYTAdditions)
```

**MUST** Category 中的方法和属性的名字必须带有 app 或 organization 的前缀。这样可以避免覆盖已存在的方法，而且降低了来自不同库的 category 添加同名方法的可能性。（对于后者，Objective-C 运行时不指定调用哪个方法，这会导致意外的后果。）

**For example:**

```objc
@interface NSArray (NYTAccessors)
- (id)nyt_objectOrNilAtIndex:(NSUInteger)index;
@end
```

**Not:**

```objc
@interface NSArray (NYTAccessors)
- (id)objectOrNilAtIndex:(NSUInteger)index;
@end
```

## 注释

**SHOULD** 当需要的时候，注释应该被用来解释 **为什么** 特定代码做了某些事情。
**MUST** 所使用的任何注释必须保持最新否则就删除掉。

**NOT RECOMMENDED** 通常应该避免一大块注释，代码就应该尽量作为自身的文档，只需要隔几行写几句说明。但这不适用于那些用来生成文档的注释。

## init 和 dealloc

**SHOULD** `dealloc` 方法应该放在实现文件的最上面，接在 `@synthesize` 和 `@dynamic` 语句的后面。在任何类中，`init` 都应该直接放在 `dealloc` 方法的下面。

`init` 方法的结构应该像这样：

```objc
- (instancetype)init {
    self = [super init]; // 或者调用指定的初始化方法
    if (self) {
        // Custom initialization
    }
    return self;
}
```

## 字面量

**SHOULD** 每当创建 `NSString`， `NSDictionary`， `NSArray`，和 `NSNumber` 类的不可变实例时，都应该使用字面量。要注意 `nil` 值不能传给 `NSArray` 和 `NSDictionary` 字面量，这样做会导致崩溃。

**推荐：**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**反对：**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect 函数

当访问一个 `CGRect` 的 `x`， `y`， `width`， `height` 时，应该使用[`CGGeometry` 函数][CGRect-Functions_1]代替直接访问结构体成员。苹果的 `CGGeometry` 参考中说到：

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**推荐：**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**反对：**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

[CGRect-Functions_1]:https://developer.apple.com/documentation/coregraphics/cggeometry

## 常量

**RECOMMENDED** 常量首选内联字符串字面量或数字，因为常量可以轻易重用并且可以快速改变而不需要查找和替换。常量应该声明为 `static` 常量而不是 `#define` ，除非非常明确地要当做宏来使用。

**推荐：**

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**反对：**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 枚举类型

**MUST** 当使用 `enum` 时，必须使用新的固定底层类型的规范，因为它具有更强的类型检查和代码补全功能。现在 SDK 包含了一个宏来鼓励使用固定底层类型：`NS_ENUM()`。

**推荐：**

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## 位掩码

**MUST** 当用到位掩码时，使用 `NS_OPTIONS` 宏。

**举例：**

```objc
typedef NS_OPTIONS(NSUInteger, NYTAdCategory) {
    NYTAdCategoryAutos      = 1 << 0,
    NYTAdCategoryJobs       = 1 << 1,
    NYTAdCategoryRealState  = 1 << 2,
    NYTAdCategoryTechnology = 1 << 3
};
```

## 私有属性

**SHALL** 私有属性应该声明在类实现文件的类扩展（匿名 category）中。

**推荐：**

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 图片命名

**SHOULD** 图片名称应该被统一命名以保持组织的完整。它们应该被命名为一个说明它们用途的驼峰式字符串，其次是自定义类或属性的无前缀名字（如果有的话），然后进一步说明颜色 和/或 展示位置，最后是它们的状态。

**推荐：**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` 和 `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` 和 `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`.

**SHOULD** 用于类似目的的图片应该被分组，放入 Images 文件夹或 Asset Catalog。

## 布尔

**MUST NOT** 永远不要直接和 `YES` 进行比较，因为 `YES` 被定义为 `1`，而 `BOOL` 在 Objective-C 中是一个 `CHAR` 类型，多达 8 位（所以如果 `11111110` 和 `YES` 比较会返回 `NO`）。

**对于对象指针：**

```objc
if (!someObject) {
}

if (someObject == nil) {
}
```

**对于 `BOOL` 值：**

```objc
if (isAwesome)
if (!someNumber.boolValue)
if (someNumber.boolValue == NO)
```

**反对：**

```objc
if (isAwesome == YES) // Never do this.
```

**MAY** 如果一个 `BOOL` 属性的名称是一个形容词，属性可以省略 “is” 前缀，但为 get 访问器指定一个惯用的名字，例如：

```objc
@property (assign, getter=isEditable) BOOL editable;
```

内容和例子来自 [Cocoa 命名指南][Booleans_1] 。

[Booleans_1]:https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE

## 单例

**SHOULD** 单例对象应该使用线程安全的模式创建共享的实例。

```objc
+ (instancetype)sharedInstance {
    static id sharedInstance = nil;

    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[self alloc] init];
    });

    return sharedInstance;
}
```

这将会预防[有时可能产生的许多崩溃][Singletons_1]。

[Singletons_1]:http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html

## 导入   

**MUST** 如果有一个以上的 import 语句，就对这些语句进行[分组][Import_1]。
**MAY** 每个分组的注释是可选的。   

注：对于模块使用 [@import][Import_2] 语法。   

```objc
// Frameworks
@import QuartzCore;

// Models
#import "NYTUser.h"

// Views
#import "NYTButton.h"
#import "NYTUserView.h"
```

[Import_1]: http://ashfurrow.com/blog/structuring-modern-objective-c
[Import_2]: http://clang.llvm.org/docs/Modules.html#using-modules

## 协议

**SHOULD** 在 [委托或数据源协议][Protocols_1]中，各方法的第一个参数应该是发送消息的对象。

当一个对象是多个类型相似的对象的委托时，这有助于消除歧义；而且有助于明确一个类实现这些委托方法的意图。

**推荐：**

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**反对：**

```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```

[Protocols_1]: https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html

## Xcode 工程

**SHOULD** 为了避免文件杂乱，物理文件应该保持和 Xcode 项目文件同步。
**SHOULD** Xcode 创建的任何组（group）都必须在文件系统有相应的映射。
**SHOULD** 为了更清晰，代码不仅应该按照类型进行分组，也可以根据功能进行分组。

**SHOULD** 如果可以的话，尽可能一直打开 target Build Settings 中 "Treat Warnings as Errors" 以及一些[额外的警告][Xcode-project_1]。如果你需要忽略指定的警告,使用 [Clang 的编译特性][Xcode-project_2] 。

[Xcode-project_1]:http://boredzo.org/blog/archives/2009-11-07/warnings

[Xcode-project_2]:http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas

# 其他 Objective-C 风格指南

* [Google](https://google.github.io/styleguide/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
