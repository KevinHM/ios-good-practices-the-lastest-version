
跟随iOS开发技术发展的潮流，我将持续维护本文档的中文版，如果你喜欢这个译本，也请给个 Star 鼓励下~~
本文档的[英文原版在这里](https://github.com/futurice/ios-good-practices)，感谢[Futurice](https://github.com/futurice)团队卓越的工作，为我们提供这么优质的文档。

> 知识是人类进步的阶梯

*翻译，喵 ~~*

# iOS开发的最佳实践
-
*就像一个软件项目一样，这份文档如果我们不持续维护就会逐渐失效，我们鼓励大家参与到这个项目中来---仅需提交一个 issue 或发送一份 pull request*

对其他移动平台感兴趣？我的[Andriod开发最佳实践][58]以及[Windows App开发最佳实践][59]可能会帮到你哦。

## 为什么写这个文档

iOS开发要上手比较困难，因为无论是 Objective-C 还是 Swift 在别处都没有广泛被应用，iOS 这个平台似乎对一切都有一套不同的叫法。当你尝试在真机上跑程序时难免会磕磕碰碰。这份持续更新的文档就是你的救星！无论你是Cocoa王国的新手，或是老练到只想知道"最佳做法"是什么，这份文档都值得一读。当然，内容仅供参考，你有理由采取不同的做法只要你愿意！

## 目录

如果你想阅读指定的小节，可以通过目录直接跳转

1. [开始吧](#开始吧!)
2. [常用库](#常用库)
3. [Architecture 架构](#架构)
4. [网络请求](#网络请求)
5. [Stores 存储](#存储)
6. [Assets 资源](#资源)
7. [编码风格](#编码风格)
8. [Security 安全](#安全)
9. [诊断](#诊断)
10. [Analytics 统计分析](#统计分析)
11. [编译构建](#编译构建)
12. [Deployment部署](#部署)
13. [App内购(IAP)](#内购)
14. [License](#授权)
15. [More Ideas(计划)](#计划)
16. [译者](#译者)

## 开始吧！


### Xcode

[Xcode][60]是绝大多数 iOS 开发者选择的 IDE，也是 Apple 唯一一个官方支持的 IDE. 也有一些其他的选择，最著名的可能就是 [AppCode][61]了。但除非你已经对 iOS 游刃有余，否则还是用 Xcode 吧，尽管 Xcode 有一些缺点，但它现在还算是相当实用的！

要安装 Xcode ，只需在 [Mac 的 AppStore][62] 上下载即可。它自带最新版的 SDK 和 iOS 模拟器，其他版本可以在 *Preferences > Downloads*处安装。

### 创建工程

开始一个新的 iOS 项目时，一个常见的问题是：界面用代码写还是用 Storyboard、xib来画？现有的 App 中两种方式都占有相当的市场。就此我们需要考虑以下几点：

**用代码写界面有啥好处？**

* Storyboard 的 XML 结构很复杂，所以如果用 Storyboard ，合并代码时很容易冲突，比起用代码来写，麻烦许多。
* 用代码更容易构建和复用视图，从而使你的代码库更容易遵循 [(Don't Repeat Yourself)DRY原则][63]
* 用代码可以让所有的信息都集中在一处。但使用 Interface Builder 你得到处找对应的检查器各种点，才能找到你要设置的属性！

**用 Storyboard 画界面有啥好处？**

* 对技术不太熟悉的人也可以画：调整颜色、布局约束等等，为项目作出直接贡献。不过做这些时，需要工程已经建好并了解一些基本的知识。
* 开发迭代更快，因为不需要 build 工程就能预览到作出的改动，所见及所得：
  * Xcode 6中，在Storyboard里终于能看到自定义的字体和 UI 控件的样式了。这让你在设计时能更好地了解界面的最终外观。
  * 从 iOS8 开始（iOS7 部分兼容），你可以用[SizeClasses][64] 来设计同时支持各种屏幕尺寸的界面，省去了很多重复的工作。

### gitignore文件

要为一个项目添加版本控制，最好第一步就添加一个恰当的`.gitignore`文件。这样一来不需要的文件(如用户配置、临时文件等)就不会进入 repository（版本仓库）了。可喜的是，Github 已经帮我们准备了 [Objective-C版][65] 和 [Swift版][66]。

### Cocoapods

如果你准备在工程中引入外部依赖(例如第三方库)，[Cocoapods][67]提供了快速而便捷的集成方法。安装方法如下：

```
sudo gem install cocoapods
```
首先进入你的工程目录，然后运行

```
pod init
```

这样会创建一个 Podfile 文件，在这里集中管理所有的依赖。添加你所需要的依赖然后运行

```
pod install
```
来安装这些库，并把它们和你的工程一起放进一个 `workspace` 里。在 commit 的时候,[推荐把依赖库在你的 repo 里安装好之后再 commit ][68],最好不要让每个开发者 `checkout`后还要自己跑一下 `pod install`。

注意：从此以后要用`.workspace`打开工程，不要再用`.xcproject`打开，否则代码编译不通过。

下面这条指令：

```
pod update
```
会把所有的 pod 都更新到 Podfile 允许的最新版本。你可以通过一系列的 [语法][69]来准确指定你对版本的要求。

### 项目结构：

把这些数以百计的源文件都保存在同一目录下，不根据工程结构来构建一个目录结构是无法想象的。你可以使用下面的结构：

```objective-c
|- Models
|- Views
|- Controllers
|- Stores
|- Helpers
```

首先，在 Xcode 的 Project Navigator (左边栏)里，把这些目录建立为group (小小的黄色"文件夹")，建在工程的同名 group 下。然后，把每一个 group 与工程路径下实际的文件夹链接起来，方法是：

* Step 1、选中 group
* Step 2、打开右边栏的 File Inspector
* Step 3、点击小小的灰色文件夹 icon
* Step 4、在工程目录下创建一个新的子文件夹，名称与 group 相同。

### 本地化

一开始就应该把所有的文案放在本地化文件里，这不仅有利于翻译，也能让你更快地找到面向用户的文本。你可以在 build scheme 里添加一个 launch 参数，指定在某种语言下启动 App，例如：

```
-AppleLanguages (Finnish)
```

对于更复杂的翻译，比如与名词的数量有关的复数形式(如 "1 person" 对应 "3 people"),你应该使用 [`.stringsdict`][70]格式来替换普通的 `localizable.strings` 文件。只要你能习惯这种奇葩的语法，你就拥有了一个强大的工具，你可以根据需要([如俄语或阿拉伯语的规则][71])把名词变为"一个"、"一些"、"少数"和"许多"等复数形式。

更多关于本地化的信息，请参考2012年2月的[Helsink iOS会议的幻灯片][1]。其中大部分演讲至少到2014年10月为止仍然不过时！

### 常量

创建被 prefix header 引入的一个 `Constants.h` 文件
不要用宏定义( `#define` ),用实际的常量定义
```objective-c
static CGFloat const XYZBrandingFontSizeSmall = 12.0f;
static NSString * const XYZAwesomenessDeliveredNotificationName = @"foo";
```
常量类型安全并有更明确的作用域(在所有没有引入的文件中不能使用)，不能被重定义，并且可以在调试器中使用。

### 分支模型

App发布的时候把 Release 代码从原有的分支上隔离出来，并且加上适当的tag，是很好的做法，对于向公众分发（比如通过Appstore）的 app 这一点尤其重要。同时，涉及大量 commit 的 feature 应该在独立的分支上完成。 [`git-flow`](https://github.com/nvie/gitflow) 是一个帮助你遵守这些规则的工具。它只是在 git 的分支和 tag 命令上简单加了一层包装，就可以帮助维护一套适当的分支结构，对于团队协作尤为有用。所有的开发都应该在 feature 对应的分支上完成（小改动在 develop 分支上完成），给 release 打上 app 版本的 tag，然后 commit 到 master 分支时只能用下面这条命令：

`git flow release fininsh <version>`

## 常用库

一般来说，在工程里添加外部依赖要谨慎。当然，眼下某个第三方库能漂亮地解决你的问题，但或许不久之后就陷入了维护的泥淖，最后随着下一版 OS 的发布全线崩溃。另一种情况是，原先只能通过引用外部库来实现的 feature，突然官方 API 也支持了。在设计良好的项目里，把第三方库替换为官方的实现花不了多少功夫，但在将来会大有裨益。永远要优先考虑用苹果官方的框架（也是最好的框架）来解决问题！

因此，这一章有意写得比较简短。下面介绍的第三方库主要用来减少模板代码（例如 Auto Layout）或者用来解决复杂的、需要大量测试的问题，例如计算日期。随着你对 iOS 越来越精通，务必要四处看看它们的源码，熟悉它们所使用的底层框架。你会发现做好这些就能减轻许多重担了。

#### AFNetworking 
99.95% 的 iOS 开发者使用这个库，当 NSURLSession 自己本身也非常完善的时候， AFNetworking 仍然能凭借很多 App 需求的队列请求管理能力立于不败之地。

#### DateTools 日期工具
总的来说，[不要自己计算日期][72]。[DateTools][75] 是一个经过彻底测试的开源库，你可以放心使用它来做这种事情。

#### Auto Layout 库
如果你更喜欢用代码写界面，你会用过 Apple 难用的 `NSLayoutConstraint`的工厂方法或者 [`Visual Format Language`][2]。前者很啰嗦，后者基于字符串不利于编译检查。

[masonry][3] 通过他自己的 DSL 来创建、更新和替换约束，利用语言丰富的操作符重载特性较优雅地实现了 AL。Swift 中一个类似的库是 [Cartography][4]。如果更加保守的话， [FLKAutoLayout][5] 是一个好的选择，它为原生API添加了一层简洁而不奇异的包装。

## 架构

* [Model-View-Controller-Store][6] (MVCS)
  * 这是苹果默认的架构(MVC)上增加了一个 Store 层，用来吐出 Model， 处理网络请求、缓存等。
  * 每个 Store 暴露给 View Controller 的或是 `RACSignal`,或是返回值为 `void`,参数中带有自定义的 `completion block`的方法。

* [Model-View-ViewModel][7](MVVM)
  * MVVM 是为了解决“巨大的 view controller”而生，它把 UIViewController 的子类看做 View 层的一部分， 用 ViewModel 维护所有的状态来给 ViewController 瘦身。
  * 对于 Cocoa 开发者是一个很新的概念，但[正在引起][17][越来越多的关注][18]。想了解更多请参考：Bob Spry 的 [fantastic introduction][8].

* [View-Interactor-Presenter-Entity-Routing (VIPER)][9].
  * 颇为奇特的架构，但项目大到即使使用 MVVM 都会太凌乱并且需要重点考虑项目可测性的情况下值得参考。

### "通知"模式

以下是组建之间互发通知的一些常见手段：

* Delegate (一对一) ： Apple 官方经常用的模式(有些人认为用得太泛滥了)。主要用于回传，比如从模态框回传数据。
* Callback blocks(一对一) ： 耦合更松，同时能让相关联的代码在一起，并且消息发出者数量很多时比 Delegate 更方便。
* Notification Center（一对多）：可能是最常见的对象发送 `events` 给多个观察者的方法。耦合性非常松 - 没有任何对当前派发对象的引用的情况下，通知也能够在全局范围内被观察到。
* Key-Value-Observing (KVO) : (一对多)。不需要被观测的对象主动"发出通知",只需要被观测的键(属性)支持 Key-Value Coding （KVC）。这种模式比较含混，而且标准API比较繁复，所以一般不推荐使用。
* Signal : (一对多)。是ReactiveCocoa的核心，它允许结合你的关键内容进行链式调用，用这种方法逃离[回调深渊(嵌套过多的回调)][14]


### Models

要确保你的 Model 是不可变的，他们用来把远程 API 的语义和类型转换为 App 适用的语义和类型。Github的[Mantle][10]是个不错的选择。

### Views

在自定义视图中使用 AutoLayout 时，[推荐在初始化方法中创建并激活你的约束][11]。如果你需要动态地改变你的约束，hold住(保留)他们(约束)的引用并在必要的时候关闭或激活他们。

只有在极少数情况下你需要重写 `UIViewController`的`updateViewConstraints`.如果这么做，要记得在View 类中加上如下代码：

Swift：

```swift
override class func requiresConstraintBasedLayout() -> Bool {
    return true;
}
```

Objective-C:

```objective-c
+ (BOOL)requiresConstraintBasedLayout {
    return YES;
}
```
不然，系统可能不会如期调用 `-updateConstraints`,而导致奇怪的 bug 。这一点上 Edward Huynh 提供的[这个博客][12]有更详细的解释。

### Controllers

要使用依赖注入，也就是说，应该把  controller 需要的数据用参数传进来，而非把所有的状态都保持在单例中。后者仅当这些状态的确是全局状态的情况下才适用。

Swift:

```swift
let fooViewController = FooViewController(viewModel: fooViewModel)
```

Objective-C

```objective-c
FooViewController *fooViewController = [[FooViewController alloc] initWithViewModel:fooViewModel];
```
尽量避免在 view controller 中引入大量的本可以安全地放在其他地方实现的业务逻辑，这会让 view Controller 变得十分臃肿。Soroush Khanlou 有一篇 [很好的博客][13] 介绍了如何实现这种机制,而类似 [MVVM][7] 这样的程序架构将 view controller 当 views 对待，因此大大地减少了 view controller 的复杂度。

## 网络请求

### 传统方法：使用自定义回调 block

```objective-c
//GigStore.h
typedef void (^FetchGigsBlock)(NSArray *gigs, NSError *error);

- (void)fetchGigsForArtist:(Artist *)artist completion:(FetchGigsBlock)completion;

//GigStore.m
[GigStore sharedStore] fetchGigsForArtist:artist completion:^(NSArray *gigs, NSError *error) {
    if(!error) {
        //Do something with gigs
    }
    else {
        // :(
    }
};
```

这样虽可行，但如果要发起几个链式请求，很容易导致回调深渊。

### Reactive 的方法：使用 RACSignal

如果你身陷回调深渊，可以看看 [ReactiveCocoa(RAC)][19].这是一个多功能、多用途的库，它可以改变[整个 App][20] 的写法。但你也可以仅在适合用它的时候，零散地用一下。

[Teehan+lax][21]以及[NSHipster][22]很好地介绍了 RAC 概念(以及整个 FRP 的概念)。

```objective-c
//GigStore.h

- (RACSignal *)gigsForArtist:(Artist *)artist;

//GigsViewController.m
[[[GigStore sharedStore] gigsForArtist:artist] subscribeNext:^(NSArray *gigs) {
                            // Do something with gigs
                        } error:^(NSError *error) {
                            // :(
                        }];
```

在这里我们可以把 gig(演出) 信号与其他信号结合，因此可以在展示 gig 之前做一些修改、过滤等处理。

## 存储

作为一个可以"在地面上移动"的移动应用，通常有某种存储模型把数据保存在某个地方，如硬盘上、本地数据库中或者远程的服务器上。在把模型对象的任意活动抽象出来的方面，Store 层也非常有用。

抓取数据通常是异步进行的，但它是意味着关闭后台请求还是从硬盘反序列化一个大文件呢？你的 Store 层的 API 必须通过提供某种延期机制反映出这种情况，就像同步返回数据将引起线程阻塞那样。

如果你使用 [ReactiveCocoa][14], 通常会选择 `SignalProducer` 作为返回类型。举个栗子，获取某个艺术家的演出信息将会产生下面这样 Signature：

Swift + RAC 3:

```swift
func fetchGigsForArtist(artist: Artist) -> SignalProducer<[Gig], NSError> {
    //...
}
```

Objective-C + RAC 2:

```objective-c
- (RACSignal *)fetchGigsForArtist:(Artist *)artist {
    //...
}
```

这里，返回的 `SignalProducer` 仅仅是获取演出列表的一个"配方"。仅当被订阅者（如：一个 viewModel ）启动时才会执行获取演出列表的实际的动作，在数据返回前取消订阅将会取消该网络请求。

如果你不想使用信号、"期货"或类似的机制来代表你未来的数据，你也可以使用常规的 block 回调。但要记住，block 块嵌套地进行链式调用，如在某个网络请求依赖于另一个的结果的情况下，就会迅速变得非常笨重 --- 这种情况通常被称为“**回调深渊**"。

## 资源

Asset catalogs是管理你所有项目可视化资源的最好方式，他们可以同时管理通用的以及设备相关的(iPhoen4-inch,iPhone Retina,iPad 等)资源，并且会通过他们的名字自动分组。告诉你的设计师如何添加它们，(Xcode有内建的 Git 客户端)可以节省很多时间，否则你会很多时间从邮件或者其他渠道把它们复制到代码库中。同时，这样也可以让设计师即刻看到自己的改动，可以根据需求进行迭代。

### Using Bitmap Images 使用位图

Asset catalog 只会暴露出一套图片的名字，省略了每张图片实际的文件名。这样类似 `button_large@2x.png`这类文件的命名空间仅限于 asset 内部，很好地避免了 asset 的命名冲突。然而，命名 asset 时遵循一些原则可以让生活更轻松：

```objective-c
IconCheckmarkHighlighted.png // Universal, non-Retina
IconCheckmarkHighlighted@2x.png // Universal, Retina
IconCheckmarkHighlighted~iPhone.png // iPhone, non-Retina
IconCheckmarkHighlighted@2x~iPhone.png // iPone, Retina
IconCheckmarkHighlighted-568@2x~iPhone.png // iPhone, Retina, 4-inch
IconCheckmarkHighlighted~iPad.png // iPad, non-Retina
IconCheckmarkhighlighted@2x~iPad.png // iPad, Retina
```

其中的 `-568h`、`@2x`、`~iPhone`以及`~iPad`这些标示符本省并不是必需的，但如果在文件名里加上它们，把文件拖动到  asset 时就能自动落到正确的"格子"上，因此能避免难以察觉的错误拖放。

### Using Vector Images 使用矢量图

你可以把设计师设计的[原始图矢量图(PDFs)][16]放进 Asset catalog,让 Xcode 来自动生成位图。这样能减少工程的复杂度(减少文件的个数)。

## 编码风格

### 命名

Apple 非常注意在 API 中保持命名一致性，即便是非常冗长的命名也如此。做 cocoa 开发时要遵循 [Apple的命名规范][23], 这样能让加入项目的新人轻松许多。

以下是几条看了就能用上的基本规则：
以*动词开头*的方法，表示它执行的操作会造成一些影响( 译者注：有时候是函数副作用 )，但是不返回任何值。
`- (void)loadView;` 或者 `- (void)startAnimating;`

以下注释来自["维基魔杖"][24]
> 在计算机科学中，函数副作用指当调用函数时，除了返回函数值之外，还对主调用函数产生附加的影响。例如修改全局变量（函数外的变量）或修改参数。

> 函数副作用会给程序设计带来不必要的麻烦，给程序带来十分难以查找的错误，并且降低程序的可读性。严格的函数式语言要求函数必须无副作用。

任何以名字开头的方法，应该返回一个对象并且不能造成额外的影响 (即不带函数副作用)。
`- (UINavigationItem *)navigationItem;` + `- (UILabel *)labelWithText:(NSString *)text;`

尽可能地区分这两种方法有很多好处。比如当您转换数据的时候就不应该造成额外的影响 ( 译者注：即函数副作用。数据转换的时候即上面那些使用名字开头的方法，实际上是一种数据转换的方法)，反过来也一样(没有函数副作用的函数应该返回某个对象，具体可参考严格意义上的函数式语言的要求)。这样的话可以让具有函数副作用的代码保持在一个小的比较集中的区域内，可以帮助理解代码并有利于 Debug.（类似我们的初始化全局变量的方法或者那些设置控制属性的方法等）

### 代码结构

[Pragma marks][25]是给方法分组很好的方法，特别是在 ViewController 中。下面是 swift/Objective-C 语言的一个在 viewController 中常见的结构：

Swift MARK 风格：

```swift
import someExternalFramework

class FooViewController : UIViewController, FoobarDelegate {
    let foo: Foo
    
    private let fooStringConstant = "FooConstant"
    private let floatConstant     = 1234.5
    
    //MARK: LifeCycle
    
    //Custom initializers go here
    
    //MARK: View LifeCycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // ...
    }
    
    //MARK: Layout
    private func makeViewConstaints() {
        // ...
    }
    
    //MARK: User Interaction
    
    func foobarButtonTapped () {
        // ...
    }
    
    //MARK:FoobarDelegate
    func foobar(foobar: Foobar didSomethingWithFoo foo: Foo) {
        // ...
    }
    
    //MARK: Additional Helpers
    private func displayNameForFoo(foo: Foo) {
        // ...
    }
}

```

Objective-C MARK风格：

```objective-c
#import "someModel.h"
#import "someView.h"
#import "someController.h"
#import "someStore.h"
#import "someHelper.h"
#import <someExternalLibrary/someExternalLibraryHeader.h>

static NSString * const XYZFooStringConstant = @"FoobarConstant";
static CGFloat const XYZFooFloatConstant = 1234.5;

@interface XYZFooViewController () <XYZBarDelegate>
@property (nonatomic, readonly, copy) Foo *foo;
@property (nonatomic, strong) UILabel *label; //译者加

@end

@implementation XYZFooViewController

#pragma mark - LifeCycle

- (instancetype)initWithFoo:(Foo *)foo;
- (void)dealloc;

#pragma mark - View LifeCycle

- (void)viewDidLoad;
- (void)viewWillAppear:(BOOL)animated;

#pragma mark - Layout

- (void)makeViewConstraints;

#pragma mark - Public Interface

- (void)startFooing;
- (void)stopFooing;

#pragma mark - User Interface

- (void)foobarButtonTapped;

#pragma mark - XYZFoobarDelegate

- (void)foobar:(Foobar *)foobar didSomethingWithFoo:(Foo *)foo;

#pragma mark - Internal Helpers

- (NSString *)displayNameForFoo:(Foo *)foo;

#pragma mark - Setter / Getter (译者加)

- (UILabel *)label;

@end

```

最重要的是让这些分块标记在工程里所有的类里保持一致!

### 其他的编程风格

Futurice（作者所在的公司）并没有公司范围的编码风格指南。不过，仔细研究一下其他开发社区的 Objective-C 风格指南会非常有用，尽管有些部分可能只对特定公司有效或比较主观。

* [Github][26]
* [Google][27]
* [The New York Times][28]
* [Ray Wenderlich][29]
* [Sam Soffers][30]
* [Luke Redpath][31]

## 安全

即使在这样一个时代，我们信任我们的便携设备，让其携带自己最私有的数据，但 app 的安全性仍然是一个经常被忽视的主题。尝试对数据安全性的设定找到一个良好的权衡，以下有一些简单的经久耐用的法则。另外，Apple 的 [iOS安全指南][35]是一个很好的入门教程。

### 数据存储

如果你的 app 需要存储敏感数据，比如用户名、密码、认证 "Token" 或者一些个人的用户信息，你需要将它们保存在本地且不允许从 App 外部进行读取。绝不能用 `NSUserDefaults`或别的存放在闪存的 plist 文件，也不能用 CoreData 来做，因为他们没有加密！绝大多数类似的情况，`iOS KeyChain`是你的救星。如果不习惯直接使用 C 的 APIs，你可以使用像 [SSKeyChain][36]或者 [UICKeyChainStore][37] 这样的一些封装。

在保存文件和密码时，确保正确而谨慎地选择恰当的安全等级。如果在设备锁定时(比如后台任务)你还需要访问文件，使用 "accessible after first unlock" 选项即可。其他的情况下，你应该要求设备在解锁之后才能访问数据。仅在需要使用敏感数据时才读取。

### 网络

确保任何时候与服务端的 HTTP 通信都是 TLS 加密的。为避免中间人攻击窃听你的加密数据，你可以设置[证书约束(certificate pinning)][38]，像 [AFNetworking][39]或[Alamofire][40]这种流行的网络库都支持这样通信。

### Logging(日志打印)

发布你的 app 之前，应特别小心地设置好合适的日志级别。构建的产品(ipa文件)绝不能(日志)记录登录密码、API的Tokens等类似的敏感信息，因为这很容易导致将他们泄露给公众。另一方面，记录基本的控制流程可以帮你定位用户所遇到的问题。

### UserInterface(用户界面)

当使用 `UITextField`做密码输入时，记住设置它们的 `secureTextEntry` 属性为 `true`,以免明文显示密码。同时也应该关闭其"输入自动校正"的功能，并在任何合适的时刻清空密码，比如当 app 退到后台时。

当 app 退到后台时，清空剪切板可以避免密码或其他敏感数据被泄露。由于 iOS 可能需要你 app 的屏幕截图，以显示在 app 切换器中，所以在 `applicationDidEnterBackground` 方法返回前，应该确保 UI 上显示的所有敏感数据被清空。

## 诊断

### 编译警告

建议把编译警告都打开，并且像对待 error 一样对待 warning。[这份幻灯片][32]论证了这一点。幻灯片里同时还讲到了如何在特定文件或特定代码段中忽略特定的 warning。
一句话，在 build setting 的 "Other Warning Flags"中至少要加入以下两个值：

* `-Wall` （开启非常多的额外的 warning）
* `-Wextra` （开启许多额外的 warning）

同时打开 build setting 里的 "<u>*Treat warnings as errors*</u>"

### Clang静态分析器

Clang 编译器 (也就是 Xcode 适用的编译器) 有一个*静态分析器(static analyer)*,用来执行代码控制流和数据流的分析，可以发现许多编译器检查不出来的问题。

你可以在 Xcode 的 *Product ---> Analyze*里手动运行分析器。

分析器可以运行"*shallow*"和"*deep*"两种模式。后者要慢很多，但是有跨方法的控制流分析以及数据流分析，因此能发现更多问题。

建议：

* 开启分析器的 *全部检查*（方法是在 build setting 的 "Static Analyzer" 部分开启所有选项）
* 在 build setting 里，对 release 的 build 配置开启 "*Analyzer during 'Build'*"。（真的，一定要这样做 --- 你不会记得手动跑分析器的。）
* 把 build setting 里的 "*Model of Analysis for 'Analyze'*"设为 *Shallow(faster)*
* 把 build setting 里的 "*Model of Analysis for 'Build'*"设为 *Deep*

### [Faux Pas][33]
由我们员工[Ali Rantakari][34]创作的 Faux Pas 是一个出色的静态 Error 检测工具。它能分析你的代码库，找出你全然不知的错误。在发布任何iOS （或 Mac）App之前务必要运行它一次！

### Debugging

当 App 崩溃时，默认情况下 Xcode 不会进入 Debugger。要想进入 Debugger，需要添加一个 Exception Breakpoint （点击 Xcode 的 Debug Navigator 底部的"+"号），遇到 Exception 时就会暂停执行。在大部分情况下，你都能看到导致 Exception 的那行代码。 这种方法会捕捉到任何 Exception，包括已经做了处理的 Exception。如果Xcode经常停在良性的 Exception 上(比如第三方库)，选择 *Edit Breakpoint*然后在 *Exception* 下拉菜单中选择 *Objective-C*可以减少这种情况的出现。

在 View 的 Debug 方面， [Reveal][41]和[Spark Inspector][42]是两个强大的可视化检查器，可以节约你大量的时间，尤其是用 AutoLayout  时想知道消失的视图去哪儿了的情况。 Xcode 也免费提供了一个类似的东西，不过只支持 iOS8 + ，并且还不够完善。

### Profiling评测

Xcode 自带一套评测工具 "Instruments"。它包含了众多的评测工具：评测内存使用、CPU、网络连接、图像等等。它本身是个庞然大物，但一个比较简单直接的用途是用 Allocations Instrument 来检测内存泄露。只需在 Xcode 中选择 *Product ---> Profile*,选择 *Allocations instrument*,点击 *Record*按钮，然后从 *Allocation Summary*中过滤一些有用的字符串，比如 app 中你自己写的类的类名前缀。在 *Persistant*一栏中的计数显示了每个对象有多少个实例。如果某个类的实例个数一直胡乱增长，说明有内存泄露。

众所周知的是 Instruments 有一个 Automation 工具可以把 UI 交互录制为 JavaScript 文件并重放。[UI Auto Monkey][43]是一个脚本，它可以借助 Automation 在你的 App 上随机点击、清扫、旋转，这对压力测试/浸泡测试很有帮助。

要格外注意的是，你在哪里以何种方式创建了巨耗资源的类。举个栗子，`NSDateFormatter`创建起来非常耗资源，当快速而连续这么做时，比如在 `tableView:cellForRowAtIndePath:`方法中，会真正减慢 App 的响应速度。你应该创建一个它的 static 实例，并在需要格式化日期时直接使用该实例。

## 统计分析

强烈推荐在你的 App 中添加一个统计分析的框架，它能帮助你看到用户实际上是怎么用你的 App 的。X 功能有价值吗？按钮 Y 太难找到了吗？要回答这些问题，可以把点击事件、计时以及其他可测的信息发送到一个能收集并可视化这些信息的服务上，比如 [Google Tag Manager][44]。Google Tag Manager 比 Google Analytics 更灵活一些，它在 App 和 Analytics 之间插了一个数据层，因此不须更新 app 就可以通过 web service 更改数据逻辑。

一种好的做法是加一个轻量的辅助类，比如 `XYZAnalyticsHelper`,用来把 App 内部的 model 和数据格式（XYZModel， NSTimeInterval等）翻译成以字符串为主的数据层。

Swift :

```swift
fun pushAddItemEventWithItem(item: Item, editMode: EditMode) {
    let editModeString = nameForEditMode(editMode)
    
    pushToDataLayer([
        "event" : "addItem",
        "itemIdentifier" : item.identifier,
        "editMode" : editModeString
    ])
}
```
Objective-C :

```Objective-C
- (void)pushAddItemEventWithItem:(XYZItem *)item editMode:(XYZEditMode)editMode {
    NSString *editModeString = [self nameForEditMode:editMode];
    
    [self pushToDataLayer:@{
        @"event" : @"addItem",
        @"itemIdentifier" : item.identifier,
        @"editMode" : editModeString
    }];
}
```

这样做有一个额外的好处：在有必要时，可以清除掉整个统计分析框架，而 App 其余的部分不受任何影响。

### CrashLogs崩溃日志

首先应该让 App 把崩溃日志发送到某个服务器上，这样你才能看得到。可以使用 [PLCrashReporter][45]结合自己的后台实现这个功能，但推荐使用已有的第三方服务，比如下面这些：

* [Crashlytics][46]
* [HockeyApp][47]
* [Crittercism][48]
* [Splunk MINTexpress][49]

 设置好这些之后，每次发布都要确保保存了 *Xcode archive(.xcarchive)*.Archive 里包含编译出的二进制文件以及 Debug symbol（ dSYM ），你需要这些数据来解析这个版本 App 的崩溃报告。
 
## 编译构建

### 编译配置

即使最简单的 App 也有不同的构建方式。 Xcode 提供的最基本的区别是*Debug*和*Release*模式。后者的编译时优化要强很多，代价是损失了 Debug 的可能性。苹果建议你开发时使用 *Debug*模式，提交到 AppStore 的包用 *Release*模式编译。默认的模式（在 Xcode 里的运行/停止按钮旁边的下拉菜单可以更改）就是这么设置的，Run 用 *Debug*， Archive 用 *Release*。

不过对于真实的应用，这样还是过于简单。你可以--- 不！是应该 --- 有几套不同的环境，分别用于测试、更新和其他与服务相关的操作。每套环境都可以有自己的 base URL、log 级别、bundle identifier （这样就可以同时安装）、provision profile 等。因此，简单的 Debug/Release 不能满足需求。你可以在 Xcode 工程设置的 "Info" 一栏里添加更多的编译配置。

### 编译配置的xcconfig文件

编译配置一般是在 Xcode 的界面里设置的，不过你也可以使用*配置文件*（"*.xcconfig 文件*"）来设置。这样做的好处是：

* 你可以添加注释来进行解释；
* 你可以 `#include`其他编译文件，帮助避免重复：
  * 如果你有一些所有配置通用的设置，添加一个 `Common.xcconfig` 文件，然后把它 `#include` 到其他文件里；
  * 比如你想要加一个在 "Debug" 基础上开启编译优化的配置，只需 `#include "MyApp_Debug.xcconfig"`,然后覆盖相应的设置
* 合并和解决冲突更简单一些

更多关于本话题的信息，可以[参考这些幻灯片][50]。

### Targets 

Targets 的概念比 project 低一个级别，即一个 project 可以有多个 targets，这些 targets 的设置 可以覆盖它的 project 的设置。粗略地说，每一个 target 对应着代码库上下文中的一个 app。举个栗子，你可能针对不同国家的 Appstore 有不同的 App （都是从同一个代码库编译出来的）。每一个 App 都需要 开发/staging(阶段性成果)/发布 的编译配置，因此用编译配置(build configurations)会比 target 更好一些。一个 App 对应只有一个 target 非常常见。

### Schemes

Schemes 告诉 Xcode 在 Run、Test、Profile、Analyze 和 Archive 时分别应该干什么。基本上，以上每个操作的 Scheme 对应一个 target 和 一套编译配置。你也可以传递启动参数，比如 App 运行的语言（对于测试本地化很方便）或者设置一些 Debug 用的诊断标记。

Scheme 推荐的命名方式是 `MyApp(<language>) [Environment]`:

```
MyApp (English) [Development]
MyApp (German) [Development]
MyApp [Testing]
MyApp [Staging]
MyApp [AppStore]

```
大部分环境下，语言是不需要标明的，因为 App 有可能通过 Xcode 之外的途径安装，比如 TestFlight，这样启动参数就会被忽略，这种情况下，只能手动设置设备语言来测试本地化。

## 部署

将 app 安装到 iOS 设备上并不简单。那么我们在这里会介绍几个核心的概念，理解了这些概念会对你部署 app 有很大帮助。

### Signing签名

只要你想把应用跑在真机上，你就需要在编译时用一个 Apple 颁发的 *证书*来签名。每一个证书对应一对公钥/私钥，私钥保存在你Mac的钥匙串中。证书有两种：

* **开发证书**：团队里的每个开发者都可以通过请求获得自己的开发证书。Xcode 可以自动完成这项工作，不过最好还是不要点击那个神奇的 "Fix issue"按钮，而是自己做一遍来理解这个过程到底做了什么。要把开发环境打的包安装到设备上就需要开发证书。
* **分发证书**：可以有多个，不过最好还是限制为每个组织一个，然后通过内部渠道分享它相关联的密钥。要发布到 AppStore 或者企业的内部 "Appstore"，需要这个证书。

### Provisioning(证书)配置

除了证书之外，还有 *Provisioning profiles*（配置文件），它是关联证书与设备的一环。同样有两类，分别用于开发和发布：

* **Development provisioning profile** (开发配置文件):它包括被授权安装/运行 App 的设备列表。同时它与一个或多个开发证书相关联，每一个开发证书对应一个可以使用这个 profile （配置文件）的开发者。这种 profile 可以与特定的 App 绑定，但对于开发的用途，大部分用通配的 profile 即可（AppID 以星号`*`结尾，比如 "net.senink.*"）。
* **Distribution provisioning profile** (分发配置文件):有三种分发途径，每一种的使用情景都不同。每个 distribution profile 与一个分发证书相关联，证书过期即失效。
  * **Ad-Hoc**：与开发证书相同，它包含可以安装 App 的设备白名单。这种 profile 可以用来再每年最多100个设备上做 beta 测试（译者注：最近 Apple 放宽了限制：同种设备每年可以各有100个，即iPhone 100 ;iPad 100 ;iPhone touch 100 ...）,如果想通过规模更大的测试来改善设计及用户体验，可以使用 Apple 新推出的 [TestFlight][51]服务。Supertop 上对它的优势和问题做了[很好的总结][52]。
  * **AppStore**:它没有包含设备列表，因为任何人都可以通过 Apple 的官方分发渠道安装。发布到 Appstore需要这种 profile。
  * **Enterprise**：和 Appstore 属于同一类型，没有设备白名单，任何人都可以通过企业内部的 "AppStore"来安装 App。

要把所有的证书和 profile 同步到你的设备上，在 Xcode 的 Preference 中的 Accounts里添加你的 Apple ID，然后双击团队(team)名称。底部有一个刷新按钮，但有时需要重启 Xcode 才能正常刷新。

### DebuggingProvisioning配置文件的调试

有时你需要 Debug 一个 provisioning 问题。比如，Xcode 可能拒绝把包安装到设备上，因为设备不在(development 或 ad-hoc 的) profile 的设备列表上。这种情况下，你可以使用 CraigHockenberry 优秀的 [Provisioning][53]插件定位到`~/Library/MobileDevice/Provisioning Profiles`中，选择`.mobileprovision`文件然后按*空格*键启动 Finder 的快速搜索功能，它会展示出非常丰富的信息，包括：设备、授权、证书和 App ID 等。

### 上传

[iTunes Connect][54]是苹果 AppStore 上的 App 管理平台。上传一个包，Xcode 需要一个开发者账户的 Apple ID 来签名。如果你有多个开发者账户，想要分别上传他们的 App，可能遇到一些麻烦，因为不知道为什么 *一个特定的 Apple ID只能与一个 iTunes Connect 账户相关联*。替代的方法是，为每个 iTunes Connect 账户都创建一个新的 Apple ID，然后使用 Application Loader 代替 Xcode 来上传包。这样就把打包签名与上传 `.ipa` 文件的过程解耦了。

上传包之后，保持耐心，可能一个小时后这个版本的 App 才会出现在 Builds 一栏，当它出现后，你可以把它与 App 的版本信息关联起来，然后提交审核。

## 内购

验证 App 内购的收据时，请记得进行以下检查：

* **真伪性**： 购买收据是否确实来自 Apple；
* **完整性**： 收据有没有被篡改；
* **应用匹配**： 收据中的 Bundle ID 是否与你的 App 的 Bundle ID 相符；
* *产品匹配*： 收据的 product ID 是否与你预期的 product ID 相符；
* *是否最新*： 在这之前有没有见过相同的收据ID；

设计你的 IAP 系统时，尽量把售卖的内容存储再 server 端，然后仅当收到有效的、通过以上所有检查的收据后才把内容提供给 client 端。这样的设计防止了常规的盗版机制，并且--- 既然验证是在 server 端进行的 --- 你可以利用 Apple 的 HTTP 收据验证服务，而不是自己解析收据的 `PKCS #7 / ASN.1`格式文件。

关于这个问题，更多的信息请参考[Futurice blog:Validating in-app purchases in your iOS app][55]

## 授权

[Futurice][56] 知识共享国际署名4.0 （CC BY 4.0）

## 计划

* 添加一个常用的编译警告列表
* 问 Jenkins 一些关于自动构建的技术信息
* 添加一个跟测试相关的小节
* 添加注意事项

## 译者

[KevinHM][57],喜欢类似这种项目就 Follow 他吧，更多精彩将分享给您！
文档的翻译也参考了[Dai Yue][74]早期的版本[iOS-good-practices-in-Chinese][73],非常感谢大神卓越的工作！

[1]: https://speakerdeck.com/hasseg/localization-practicum
[2]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/AutolayoutPG/VisualFormatLanguage/VisualFormatLanguage.html#//apple_ref/doc/uid/TP40010853-CH3-SW1
[3]: https://github.com/SnapKit/Masonry
[4]: https://github.com/robb/Cartography
[5]: https://github.com/floriankugler/FLKAutoLayout
[6]: http://programmers.stackexchange.com/questions/184396/mvcs-model-view-controller-store
[7]: http://www.objc.io/issues/13-architecture/mvvm/
[8]: http://www.sprynthesis.com/2014/12/06/reactivecocoa-mvvm-introduction/
[9]: http://www.objc.io/issues/13-architecture/viper/
[10]: https://github.com/Mantle/Mantle
[11]: https://developer.apple.com/videos/wwdc/2015/?id=219
[12]: http://www.edwardhuynh.com/blog/2013/11/24/the-mystery-of-the-requiresconstraintbasedlayout/
[13]: http://khanlou.com/2014/09/8-patterns-to-help-you-destroy-massive-view-controller/
[14]: https://github.com/ReactiveCocoa/ReactiveCocoa
[15]: http://elm-lang.org/learn/escape-from-callback-hell
[16]: http://martiancraft.com/blog/2014/09/vector-images-xcode6/
[17]: http://cocoasamurai.blogspot.de/2013/03/basic-mvvm-with-reactivecocoa.html
[18]: http://www.raywenderlich.com/74106/mvvm-tutorial-with-reactivecocoa-part-1
[19]: https://github.com/ReactiveCocoa/ReactiveCocoa
[20]: https://github.com/jspahrsummers/GroceryList
[21]: http://www.teehanlax.com/blog/getting-started-with-reactivecocoa/
[22]: http://nshipster.com/reactivecocoa/
[23]: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html
[24]: https://www.wikiwand.com/zh/%E5%87%BD%E6%95%B0%E5%89%AF%E4%BD%9C%E7%94%A8
[25]: http://nshipster.com/pragma/
[26]: https://github.com/github/objective-c-style-guide
[27]: http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml
[28]: https://github.com/NYTimes/objective-c-style-guide
[29]: https://github.com/raywenderlich/objective-c-style-guide
[30]: https://gist.github.com/soffes/812796
[31]: http://lukeredpath.co.uk/blog/2011/06/28/my-objective-c-style-guide/
[32]: https://speakerdeck.com/hasseg/the-compiler-is-your-friend
[33]: http://fauxpasapp.com/
[34]: https://twitter.com/AliRantakari
[35]: https://www.apple.com/business/docs/iOS_Security_Guide.pdf
[36]: https://github.com/soffes/sskeychain
[37]: https://github.com/kishikawakatsumi/UICKeyChainStore
[38]: https://possiblemobile.com/2013/03/ssl-pinning-for-increased-app-security/
[39]: https://github.com/AFNetworking/AFNetworking
[40]: https://github.com/Alamofire/Alamofire
[41]: http://revealapp.com/
[42]: http://sparkinspector.com/
[43]: https://github.com/jonathanpenn/ui-auto-monkey
[44]: http://www.google.com/tagmanager/
[45]: https://www.plcrashreporter.org/
[46]: https://try.crashlytics.com/
[47]: http://hockeyapp.net/features/
[48]: https://www.crittercism.com/
[49]: https://mint.splunk.com/
[50]: https://speakerdeck.com/hasseg/xcode-configuration-files
[51]: https://developer.apple.com/testflight/
[52]: http://blog.supertop.co/post/108759935377/app-developer-friends-try-testflight
[53]: https://github.com/chockenberry/Provisioning
[54]: https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa
[55]: http://futurice.com/blog/validating-in-app-purchases-in-your-ios-app
[56]: http://futurice.com/
[57]: https://github.com/KevinHM
[58]: https://github.com/futurice/android-best-practices
[59]: https://github.com/futurice/windows-app-development-best-practices
[60]: https://developer.apple.com/xcode/
[61]: https://www.jetbrains.com/objc/
[62]: https://itunes.apple.com/us/app/xcode/id497799835
[63]: http://www.wikiwand.com/en/Don%27t_repeat_yourself
[64]: http://futurice.com/blog/adaptive-views-in-ios-8
[65]: https://github.com/github/gitignore/blob/master/Objective-C.gitignore 
[66]: https://github.com/github/gitignore/blob/master/Swift.gitignore
[67]: https://cocoapods.org/
[68]: https://www.dzombak.com/blog/2014/03/including-pods-in-source-control.html
[69]: http://guides.cocoapods.org/syntax/podfile.html#pod
[70]: https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/StringsdictFileFormat/StringsdictFileFormat.html
[71]: http://www.unicode.org/cldr/charts/latest/supplemental/language_plural_rules.html
[72]: https://www.youtube.com/watch?v=-5wpm-gesOY
[73]: https://github.com/DaiYue/iOS-good-practices-in-Chinese
[74]: https://github.com/DaiYue
[75]: https://github.com/MatthewYork/DateTools
