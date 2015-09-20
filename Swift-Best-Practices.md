
# Swift 最佳实践

使用 Swift 进行软件开发的最佳实践.

本文档的[英文版在这里][SwiftCommunityBestPractices],感谢[Swift社区][SwiftCommunity](频道为 #bestpractices )为我们提供如此优质的文档。

[SwiftCommunityBestPractices]: https://github.com/schwa/Swift-Community-Best-Practices
[SwiftCommunity]: http://swift-lang.schwa.io/


## 前言

这个文档的产生得益于我在创作[Swift Graphics][SwiftGraphics]时做的一系列的手记。本指南中的大部分建议也考量了是否可以为其它的观点和论点。当然，感觉其他的方法必须存在时除外。

[SwiftGraphics]: https://github.com/schwa/SwiftGraphics/blob/develop/Documentation/Notes.markdown

这些最佳实践没有规定或推荐 Swift 是否应该在一个程序上以面向对象的或者函数式的方式来使用。

本文档更多的是关注 Swift 语言及其标准库。也就是说，以一个纯粹的 Swift 的角度提供可提供的关于在 Mac OS, iOS, WatchOS 和 TVOS 上如何使用 Swift 的具体建议。 同时也会提供一些如何在 Xcode 和 LLDB中有效利用 Swift 的提示和技巧。

这项工作正在进行中，非常欢迎大家通过 Pull Request 或 Issues 的方式来贡献内容。

你也可以在 [Swift-Lang slack][Swift-LangSlack](位于 #bestpractices 频道) 上参与讨论。

[Swift-LangSlack]: http://swift-lang.schwa.io/


### 贡献者注意事项

请确保所有的例程是可运行的 (这可能不适用于现有的例程)。这个 markdown 文件会转化成一个 Mac OS X 的 playground.


### 黄金法则

* Apple 通常是对的。应紧随苹果所推荐的或他的 Demo 中所展示的方式。您应该尽可能地遵守 Apple 在 [The Swift Programming Language][The Swift Programming Language] 一书中所定义的代码风格。但我们还是可以看到他们的示例代码中有不符合这些规则的地方，毕竟 Apple 是一家大公司嘛。
* 不要仅仅为了减少字符的键入数量而使用模棱两可的简短命名，较长的命名都可以依赖自动完成、自我暗示、复制粘贴来减低键入的难度。命名的详细程度往往对代码维护者很有帮助。但过于冗长的命名却会绕过Swift的主要特性之一: 类型推导,所以命名的原则应该是简洁明了。

[The Swift Programming Language]: https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/index.html


## 最佳实践


### 命名

按照 [The Swift Programming Language][The Swift Programming Language] 所推荐的命名法则，类型名称应该使用[首字母大写的驼峰命名法][upper camel case] (例如: "VehicleController")。

变量与常量应该使用首字母小写的驼峰命名法(例如: " vehicleName " )。

推荐使用 Swift 模块来定义代码的命名空间，而非在 Swift 代码上使用 Objective-C 样式的类前缀(除非接口要与 Objective-C 交互)。

不推荐使用任何形式的[匈牙利命名法][Hungarian notation]（比如：k 代表常量，m 代表方法）,取代代之我们应该使用短而简洁的名字并使用 Xcode 的类型快速帮助 (⌥ + 左击)。同样我们也不要使用类似 `SNAKE_CASE` 这样的名字。

这些法则之上，唯一例外的情况就是枚举值了，枚举值在这里应该首字母大写(这是 Apple 的 [The Swift Programming Language][The Swift Programming Language] 中的规范)：

```Swift
enum Planet {
    case Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Nepture
}
```

有必要的话命名不要缩写，实际上在 Xcode 的"文本自动补全"功能下你可以轻而易举地键入 类似 `ViewController` 的长命名。极为常见的缩写，例如: URL， 是很好的。缩写应该是全部大写 ( "URL" )或者酌情全部小写( "url" )。URL 的类型和变量命名推荐的规则： 如果 url 是一个类型，它应该被大写，如果是一个变量，那么应该小写。

[upper camel case]: http://www.wikiwand.com/en/Studly_caps
[Hungarian notation]: http://www.wikiwand.com/en/Hungarian_notation


### 注释

不应该使用注释来禁用代码。被注释掉的代码会污染你的源代码。如果你当前想要删除一段代码，但将来又可能会用到，推荐你依赖 git 或你的 bug 追踪系统来管理。

(TODO: 追加一个关于文档注释的小节，使用 nshipster 的链接)


### 类型推导

如果可能的话，使用 Swift 的类型推导，以避免冗余的类型信息。例如：

推荐：

```Swift
var currentLocation = Location()
```

而非：

```Swift
var currentLocation: Location = Location()
```

### 内省

让编译器自动推断所有的情况，这是可以做到的。在一些领域 self 应该被显式地使用，包括在 init 中设置参数，或者 `non-escaping`闭包。例如：

```Swift
struct Example {
    let name: String
    
    init(name: String) {
        self.name = name
    }
}
```

### 捕获列表的类型推导

在一个捕获列表( capture list )中指定参数类型会导致代码冗余。如果需要的话，仅指定类型即可。

```Swift
let people = [
    ("Mary", 42),
    ("Susan", 27),
    ("Charlie", 18),
]

let strings = people.map() {
    (name: String, age: Int) -> String in
    return "\(name) is \(age) years old"
}
```

如果编译器可以推导出来的话，完全可以把类型删掉：

```Swift
let strings = people.map() {
    (name, age) in 
    return "\(name) is \(age) years old"
}
```

使用编号的参数名 ("$0") 进一步降低冗长，往往能彻底消除捕获列表的代码冗余。在闭包中当参数名没有附带任何更多信息时仅使用编号形式即可( 如非常简单的映射和过滤器 )。

Apple 能够并且将会改变闭包的参数类型，通过他们的 Objective-C 框架的 Swift 变种提供出来。例如，`optionals` 被删除或更改为 `auto-unwrapping` 等。故意 under-specifying 可选并依赖 Swift 来推导类型，可以减少在这些情况下代码被破译的风险。

你应该避免指定返回类型，例如这个捕获列表( capture list )就是完全多余的:

```Swift
dispatch_async(queue) {
    ()->Void in
    print("Fired.")
}
```

(以上内容也可以参考:[这里][swiftCaptureLists])

[swiftCaptureLists]: http://www.russbishop.net/swift-capture-lists

### 常量

类型定义中使用的常量应当被申明成静态类型。例如:

```Swift
struct PhysicsModel {
    static var speedOfLightInAVacuum = 299_792_458
}

class Spaceship {
    static let topSpeed = PhysicsModel.speedOfLightInAVacuum
    var speed: Double
    
    func fullSpeedAhead() {
        speed = Spaceship.topSpeed
    }
}

```

将常量标示为 static ，允许它们可以被无类型的实例引用。

一般应该避免生成全局范围的常量，单例除外。


### 计算属性

如果你只是为了实现一个 getter，请使用短版 (short version) 的计算属性。例如：

推荐这样：

```Swift
class Example {
    var age: UInt32 {
        return arc4random()
    }
}
```

不要：

```Swift
class Example {
    var age: UInt32 {
        get {
            return arc4random()
        }
    }
}
```

如果你在属性里添加一个 `set` 或者 `didSet`， 那么你需要显示提供一个 `get`。

```Swift
class Person {
    var age: Int {
        get {
            return Int(arc4random())
        }
        set {
            pint("That's not your age.")
        }
    }
}
```

### 实例的转换

将一种类型的实例转换为到另一种类型实例时的`init()`方法：

```Swift
extension NSColor {
    convenience init(_ mood: Mood) {
        super.init(color: NSColor.blueColor)
    }
}
```

现在在 Swift 标准库中实现这种转换 `init` 方法似乎是首选。

"to" 方法是另一种合理的方法(虽然你应该遵循 Apple 的指引使用 `init` 方法)。

```Swift
struct Mood {
    func toColor() -> NSColor {
        return NSColor.blueColor()
    }
}
```

虽然你可能会使用 getter, 例如：

```Swift
struct Mood {
    var color: NSColor {
        return NSColor.blueColor()
    }
}
```

一般来说 getter 应该被限定返回接收类型的组件。例如，返回一个 `Circle` 实例的面积就很适合使用 getter,但是将一个 `Circle` 转换为一个 `CGPath` 使用 "to" 函数或者一个 `CGPath` 的 `init()` 扩展会更好。

### 单例

在 Swift 中，生成单例很简单：

```Swift
class ControversyManager {
    static let sharedInstance = ControversyManager()
}
```
Swift 的 runtime 将确保以一种线程安全的方式来创建和访问单例。

单例一般仅通过 `sharedInstance` 静态属性来访问，除非你有一个令人信服的理由不把它命名为 `sharedInstance`。不要使用静态函数或者全局函数来访问单例。

( 因为在 Swift 中生成单例是如此简单，并且因为统一的命名为您节省了大量的时间，您将有更多的时间去抱怨单例如何如何 '反模式' 并且应该不惜一切代价避免使用。您的开发伙伴们会感激你的 <译者注:反话?> 。)

### 代码组织的扩展

扩展应该被用于帮助组织代码。

当方法和属性是一个实例的外围延伸时，应该被迁移到某个扩展。注意，目前并不是所有的属性类型都可以被迁移到扩展 --- 在这个限制范围内尽你所能。

你应该使用扩展来帮助组织你的实例的定义。这方面很好的一个例子就是：一个实现了表视图数据源和委托协议的视图控制器。它没有将所有的表视图代码混成一个类，而是把数据源和委托方法放到了相关的协议扩展中。

可以根据你的觉得最好的方式将一个源文件随意分解并定义成任何的扩展，以重新组织这堆问题代码。别担心主类中的方法或扩展里指向方法和属性的结构定义。只要它们在一个 Swift 文件中就没事。

相反，主实例定义不应该指向在主 Swift 文件之外的主扩展中定义的元素。。。

### 链式 Setters

不要为了图方便而使用链式 setter 来替代简单的属性 setter ：

推荐：

```Swift
instance.foo = 42
instance.bar = "xyzzy"
```

不推荐：

```Swift
instance.setFoo(42).setBar("xyzzy")
```
比起链式 setter 传统的 setter 更简单并且需要的样板代码更少。


### 错误处理

Swift 2 的 `do/ try/ catch` 机制非常棒，推荐使用！(TODO: 拟定并提供示例)

### 避免使用 `try!`

通常推荐：

```Swift
do {
    try somethingThatMightThrow()
}
catch {
    fatalError("Something bad happened.")
}
```

不推荐：

```Swift
try! somethingThatMightThrow()
```

虽然这种形式显得有点啰嗦，但它为其他的开发者进行代码评审提供了清晰的上下文语境。

在没有更好的错误处理策略进化出来之前，使用 `try!` 作为一种临时的错误处理也是 OK 的。但是建议你定期审查你的代码是否错误的使用了 `try!`，因为它很可能上次悄悄地躲过了代码审查。


### 尽量避免 `try?`

`try?`是用来屏蔽(译者注：产生了错误但你不想做任何错误处理相关的事情)错误的，只有在你真的不关心错误的产生时，对你而言才有用。通常情况下你应该捕获错误并至少打印错误日志。


### 提前返回与Guards

如果可能的话，使用 `guard` 申明来处理提前返回或退出 (例如： 致命错误(fatal errors) 或 抛出错误 (thrown errors))。

推荐：

```Swift
guard let safeValue = criticalValue else {
    fatalError("criticalValue cannot be nil here")
}
someNecessaryOperation(safeValue)
```

不推荐：

```Swift
if let safeValue = criticalValue {
    someNecessaryOperation(safeValue)
} else {
    fatalError("criticalValue cannot be nil here")
}
```

也不推荐：

```Swift
if criticalValue == nil {
    fatalError("criticalValue cannot be nil here")
}
someNecessaryOperation(criticalValue!)
```

这让代码的逻辑扁平化，如果不符合则进入 `if let` 代码块，并且提前退出的语句放置 在接近他们相关条件的地方，而非下放到一个 `else` 代码块中。

即使你没有捕捉值( `guard let` ),这种模式也会在编译时强制执行提前退出。在第二个(不推荐的) `if` 例程中，虽然代码扁平得跟 `guard` 一样，但一个致命错误或其他返回的一些非退出操作无意中的改变将导致崩溃 (亦或状态无效，这取决于确切的情况)。从一个 guard 申明的 `else` block 中移除提前退出将会立即显示错误(编译器提示错误)。


### "超前地" 控制访问

即使你的代码没有分解为各个独立的模块，你也应该总是考虑其访问控制。标记一个定义为 "`private`" 或者 "`internal`" 可以轻量化代码文档。任何阅读这代码的人将知道这些元素是可以暂时放一边不予考虑的。相反，标记一个定义为 "`public`"就表明其他的代码可以访问这个标记元素。最好能有明确的指示而非依赖 Swift 的默认访问权限 ("`internal`")。

如果你的代码库慢慢膨胀，最终它可能被拆分为 N 个子模块。(而拆分为 N 个子模块这种工作如果)在一个已经分配了访问控制权限信息的代码库上做会更快更容易。


### "限制性地" 控制访问

在感觉使用 "`private`" 好过 "`internal`" ，或使用 "`internal`" 比 "`public`" 更合适的时，给代码添加更严格的访问控制权限通常会更好。

这将使得之后放宽代码的访问控制权限变得更容易(权限从窄到宽: "`private`" ---> "`internal`" ---> "`public`")。代码的访问控制权限如果放得太宽就有可能会被其他的代码不恰当地使用。给代码更严格的访问控制能够排除一些不恰当或不正确的调用从而提供出更好的接口。这是一种在天马行空之后关上一扇稳定之门的风格尝试。公开地暴露一个内部缓存正是这方面的一个例子。

此外，限制代码的访问权限可以限制 "曝光面积" 并且代码在重构时对其他代码的影响更少。另外的技术像 "协议驱动开发" 也能提供帮助。

## TODO sections

将来可能会展开讨论的一系列主题。

### 协议 & 协议驱动开发

### 隐式 Unwrapped Optionals

### 参考 VS 值类型

### 异步闭包

### `unowned` VS `weak`

### Cocoa Delegates

### 不可变的结构体

###  实例初始化

### 日志与打印

### 计算属性 vs 功能

### 值类型和相等性


