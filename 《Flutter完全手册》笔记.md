## 《Flutter完全手册》笔记

### 大纲

[**1. Flutter 介绍篇**](#Flutter介绍篇)

```
了解 Flutter 是什么，它的发展历史、特性以及未来。掌握 Flutter 的基本开发，包括： 环境搭建、工程类型、HotReload、Dart 语法及各种 Widget 的使用。
```

[**2. Flutter 实战篇**](#Flutter实战篇)

```
通过仿写豆瓣电影 APP，掌握使用 Flutter 开发 APP 的关键技术，
包括：布局实现、第三方库使用、数据请求、异步编程、Route、声明式 UI 的编程思维、PlatformChannel、性能监控及 App 打包
```

[**3. Flutter 开发进阶篇**](#Flutter开发进阶篇)

```
详细介绍 Flutter 的状态管理，
并且对开发过程中使用到的内容进行深入分析和解读，
构建 Flutter 开发的高级知识图谱，
包括 InheritedWidget、Scoped model、Redux、BLoC，还有 BuildContext 及 Isolate。
```

[**4. Flutter 底层进阶篇**](#Flutter底层进阶篇)

```
剖析 Flutter 架构，了解 Flutter 底层实现细节，
包括 Thread Model 和 Evnet Loop 等。
```

### Flutter介绍篇

**Flutter的四种版本**

Flutter 源代码在 git 上有四个分支，对应着 Flutter 的四种版本，这四种版本，稳定性依次提高，但新特性却依次减少：

```
master
master 分支上的代码是最新的，所以包含新的特性，
但是 master 分支上的代码没有经过测试，很可能会出现各种各样的 bug。

dev
Google 工程师会定期将 master 分支的代码滚动到 dev 分支上，
在执行滚动的过程中，会对代码进行 Google 的内部测试，所以 dev 渠道是通过测试的最新的构建。
但并不意味着 dev 版本不会有 bug，因为这个测试只是最基础的测试，
一旦发现有严重的阻塞性的 bug，就会把这个 dev版本列为Bad Builds ，而且这个版本也不会被选为 beta 版本。

beta
beta 渠道的更新频率是一个月，
每个月初，Flutter 团队会从前一个月左右的 dev 版本中选取最佳版本作为 beta 版发布，
这个版本最起码在 dev 分支上使用一周的时间，且没有新的严重的 bug。

stable
stable 渠道的更新频率是一个季度，每个季度发布一次或几次版本，但 stable 的发布频率是不确定的。
stable 版本从 beta 分支中选出，只有当 Flutter 团队确定 beta 分支中有一个非常稳定非常好的构建时，
而且通常 stable 版本的发布会在对外的公共活动中公布，例如 Flutter Live。

目前 stable 三个版本：1.0 、 1.2.1 和 。
当我们用于正式的生产环境时，一定要选择 stable 的版本。
```

**移动端跨平台开发方案的演进**

```
1. WebView流 
	- 纯H5
	- Hybrid (JSBridge, JSBridge 是 Native 代码与 JavaScript 代码的通信桥梁)
	- 基于 Hybrid 的优化改进方案

2. ReactNative流
	- 典型的方案是 ReactNative 和 Weex
	- 跨平台的布局引擎, 使用 Native 原生组件渲染, JavaScript 引擎
	- 采用 JavaScript + JavaScript 引擎+ Native 的技术方案，利用了 JavaScript 的跨平台特性实现了移动端的跨平台方案

3. 编译流/虚拟机流
4. 游戏引擎流
```

**跨平台方案的新发展方向 —— Flutter**

```
Flutter 开辟了一种全新的思路
利用 Dart 语言，同时支持 JIT 和 AOT 两种编译方式的特性
在不同场景下使用不同的编译方式，达到最高效的开发和运行体验

在 Debug 模式下，为了保证开发体验，
采用 JIT 这种动态编译的方式，将代码运行在 Dart 虚拟机上，
使得我们编写的代码可以实时更新，实现 HotReload 的特性，提升开发体验

而在 Release 模式下，又需要保证运行速度和渲染流程度，
则会采用 AOT 的编译方式，将代码直接编译成各自平台的 Native 代码，以此提高使用体验
```
```
在 UI 渲染方面， 
Flutter的渲染不依赖于平台，
基于自带的 Skia 渲染引擎 ，构建了一套完整的跨平台 UI 渲染框架；

在和平台交互方面，
Flutter 提供了 Platform Channel 的通道，可以方便的和 Native 交互
```

**Flutter 工程类型介绍**

 Flutter 有四种工程类型，这四种工程类型有不同的应用场景。

```
1. Flutter Application
如果想让 Flutter 直接运行在 Android 或 iOS 上给用户使用，
就用 Application 类型。

2. Flutter Plugin
如果你想让 Flutter 调用 Android 或 iOS 的 API，
并想将这个功能封装其起来供第三方使用，那么就用 Plugin 类型。

Plugin 是 Flutter 的一个插件包，例如，
在 Android 上有播放音乐的功能，为了在 Flutter 中使用，就可以使用 Plugin 的模式：
播放音乐的功能在 Android 上用 Java 实现，
然后 Flutter 的代码通过 PlatformChannel 使用 Android 的播放音乐的功能，
这就是一个完整的插件包，提供了播放音乐的功能。
其他 Flutter APP 想要使用播放音乐的功能，就可以依赖这个插件包。

Flutter 想要使用 Android 或 iOS 的功能，都可以使用 Plugin 的模式。

3. Flutter Package
Flutter Package 是纯 Dart 模块，只能实现 Flutter 的相关功能，
例如实现一个 Widget，然后给第三方使用。

4. Flutter Module
如果要将 Flutter 添加到 Android APP 或 iOS APP 中，
实现 Flutter 和 Native 的混合开发，就使用 Module 类型。

Flutter 的混合开发模式就采用的这种类型，
在 Android 上打包成 aar，在 iOS 上打包成 Framework，
可以很方便的集成进原有的 Native 工程里，
实现 Flutter 和 Native 的混合开发。
```

```
Plugin 和 Package
Flutter 的 Plugin 和 Package 可以用于组件化开发。

可以在 pub.dartlang.org/ 网站上看到所有的 Flutter 库。
或者在国内镜像 pub.flutter-io.cn/ 上查看 Flutter 库。
```

#### Hot Reload

**工作原理**

```
Hot Reload 只能在 Debug 模式下使用，
是因为 Debug 模式下，Flutter 采用的是 JIT 动态编译，代码是运行在 Dart VM 上，
JIT 将 Dart 编译成可以运行在 Dart VM 上的 Dart Kernel，
Dart Kernel 可以动态更新，所以就实现了代码的实时更新功能
```

当调用 Hot Reload 时：

```
1. 首先会扫描代码，找到上次编译之后有变化的 Dart 代码。
2. 在将这些变化的 Dart 代码转化为增量的 Dart Kernel 文件。
3. 将增量的 Dart Kernel 文件发送到正在移动设备上运行的 Dart VM。
4. Dart VM 会将发来的增量 Dart Kernel 文件和原有的 Dart Kernel 文件合并，然后重新加载全新的 Dart Kernel。
5. 这个时候，虽然重新加载了 Dart Kernel，却不会重新执行代码，而是通知 Flutter Framework 重建 Widget。
```

```
所以 Flutter 的 Hot Reload 并不会重新执行一遍代码，
而是触发 Flutter 重新绘制，并且会保留 Flutter 之前的状态，
所以 Hot Reload 也被称为有状态的热重载。
```

**不能使用 Hot Reload 的场景**

```
1. 代码出现编译错误的不能使用 Hot Reload
2. 代码更改会影响 APP 状态的不能使用 Hot Reload
3. 全局变量（ global variables）和静态字段（static fields）的更改不能使用 Hot Reload
4. main() 方法里的更改不能使用 Hot Reload
5. 枚举类型更改为常规的类或者常规的类变为枚举类型也不能使用 HotReload
6. 修改通用类型声明也不能使用 HotReload
```

**Hot Reload VS Hot Restart**

```
针对上面不能使用 Hot Reload 的情况，就需要使用 Hot Restart
Hot Restart 可以完全重启您的应用程序，但却不用结束调试会话

在 VS Code 里，打开命令面板，输入 **Flutter: Hot Restart ** 或者 直接快捷键 Ctrl+F5，就可以使用 Hot Restart
```

#### Dart 简介及基础语法

- Dart 中的所有东西都是对象，包括数字、函数等，它们都继承自 Object，并且对象的默认值都是 null（包括数字）。
- Dart 既可以支持 JIT（动态编译），也可以支持 AOT（静态编译）。
- Dart 是强类型语言，但是由于 Dart 可以推断类型，所以也可以支持动态类型，例如 var、dynamic。
- Dart 有强大的异步编程能力。

**变量声明**

```d
1. 使用 var 来声明变量，不需要特别指定变量的数据类型
因为 Dart 会自动推断其数据类型，所以可以使用var来定义任何的变量

var content = 'Dart 语法'; // Declare and initialize a variable.
var switchOn = false;
var current = 0;

2. 明确数据类型
就是在声明变量的时候，使用明确的数据类型

String name = "by 小德";
int count = 0;
```

```d
3. dynamic, 是数据类型是动态可变的，也可以定义任何变量
但是和 var 不同的是，var 一旦赋值后，就不能改变数据类型了
dynamic example = 'example';

var content = 'Dart 语法';
content = 1; //❌ 错误的使用方法，content为String，不能赋值数字类型

dynamic example = 'example';
example = 1;//✅  这个使用方法正确，因为 dynamic 的类型是动态可变的

4. Object, Dart 里所有东西都是对象, 是因为 Dart 的所有东西都继承自 Object
因此Object可以定义任何变量，而且赋值后，类型也可以更改

Object index = 100;
index = 'string';//✅ 因为  'String' 也是 Object

注意：
请不要滥用 dynamic，一般情况下都可以用 Object 代替 dynamic。

那什么情况下可以用 dynamic 呢？
当这个变量没法用 Dart 的类型来表示时，
比如 Native 和 Flutter 交互，从 Native 传来的数据。
所以你会看到 PlatformChannel 里有很多地方使用到了 dynamic。
```

**变量：final 和 const**

如果你不想更改变量的值，那么你可以用 final 和 const

```d
final content = 'Dart 语法'; 
static const bool switchOn = false;
```

- 使用 final 和 const 的时候可以把 var 省略
- final 和 const 变量只能赋值一次，而且只能在声明的时候就赋值
- const 是隐式的 final
- 在使用 const 的时候，如果变量是类里的变量，必须加 static ，是全局变量时不需要加

final 和 const 有什么区别呢？

```d
const 是编译时常量，在编译的时候就初始化了，
但是 final 变量是当类创建的时候才初始化
```

#### 函数

在 Dart 中函数也是对象，函数的类型是 Function

```d
返回类型 函数名(函数参数){
}

bool say(String msg , String from, int clock){
  print(msg+" from " + from + " at " + clock?.toString());
  return true;
}

// 判断函数 say 的类型是不是 Function
print(say is Function);
```

**可选参数** 

可选命名参数

```d
使用 {} 包起来的参数是可选命名参数
使用 {} 来赋值的数据类型是 Map，所以可选的命名参数的类型也是 Map
因此调用函数时，可选参数的赋值必须是 paramName: value 这种 key: value 格式的
```

```d
bool say(String msg , {String from, int clock}){
    print(msg+" from " + from + " at " + clock.toString());
    return true;
}

say('Hello Flutter');//✅ 因为 from 和 clock 是可选参数，所以可以不填
say('Hello Flutter',from: 'XiaoMing');//✅ 用命名参数格式 paramName: value 为 from 赋值
say('Hello Flutter',clock: 11);//✅
say('Hello Flutter',from: 'XiaoMing',clock: 11);//✅
```

```d
还可以给命名参数加 @required ，意思是这个也是必填参数
bool say(String msg , {@required String from, int clock}){
    print(msg+" from " + from + " at " + clock.toString());
    return true;
}

say('Hello Flutter');//❌  错误调用方式，因为 from 是必选参数，不填的话会报错
```

可选位置参数

```d
使用 [] 包起来的参数是可选位置参数
=使用 [] 来赋值的数据类型是 List，所以可选的命名参数的类型也是 List
所以赋值和参数是一一对应的
```

```d
bool say(String msg , [String from , int clock]){
    print(msg+" from " + from + " at " + clock.toString());
    return true;
}

say('Hello Flutter');//✅ 因为 from 和 clock 是可选参数，所以可以不填
say('Hello Flutter','XiaoMing',1);//✅ 为可选位置参数赋值，只能一个参数一个参数对应的赋值，所以要全部赋值
say('Hello Flutter','XiaoMing')//✅
say('Hello Flutter',1)//❌ 因为  1 赋值给了 from,但是 from 是String，所以会报错
```

可选参数的默认值 ：=

```d
因为参数是可选的，那么参数的值很可能没有赋值，就是 null
用到的时候就有可能会触发 NPE，所以可以给可选参数赋默认值
```
```d
bool say(String msg , {String from = 'empty', int clock = 0}){
    print(msg+" from " + from + " at " + clock.toString());
    return true;
}
```

#### => ：箭头语法

```d
=> 语法是 { return expr; } 的简写
因为 => 酷似箭头，也称箭头语法,也是 Lambda 表达式
```
```d
main() 函数这里:
void main() => runApp(MyApp());

就等价于：

void main(){
    return runApp(MyApp());//runApp() 返回的是 void
}
```


#### 类

Dart 中每个对象都是一个类的实例，所有类都继承自 Object

```d
class Point {
  num x, y;

  Point(num x, num y) {
    // There's a better way to do this, stay tuned.
    this.x = x;
    this.y = y;
  }
}
```

**构造函数**

默认构造函数

```
默认的构造函数就是使用类名作为函数名的构造函数
例如上面写的类 Point 的构造函数：Point(num x,num y)
```

构造函数的语法糖

```d
class Point {
  num x,y;
  // 语法糖, 简化构造函数的赋值操作
  Point(this.x, this.y);
}
```

Widget 构造函数参数采用的是可选命名参数

**创建实例**

```d
Point point = Point(0,0);
print(p.x);
```

**操作符**

```
算术运算符
比较操作符
类型判断符
赋值操作符
逻辑运算符
按位与移位运算符
条件运算符
级联操作符
其他操作符
```

类型判断操作符

```d
as 	类型转换
(emp as Person).firstName = 'Bob';

is 判断是否是某个类型,如果是的话，就返回 true
if (emp is Person) {
// 如果 emp 是 Person 类型
emp.firstName ='Bob';
}

is! 判断是否不是某个类型，如果不是的话，就返回 true
if (emp is! Person) {
// 如果 emp 不是 Person 类型
}

上面的例子中，如果 emp 是 null 的话，as 的例子就会抛异常，
is 和 isn't 的例子会返回 false.
```

赋值操作符

```d
赋值操作符是 =
如果只想当被赋值的变量为空的时候才赋值，可以使用??=

var a,b;
a = 1; //使用 = 赋值
b ??= 1; // 当 b 是空的话才赋值，否则不会赋值
```

条件运算符

```d
1. condition ? expr1 : expr2
如果 condition 是 true，返回 expr1，否则返回 expr2
var visibility = isPublic ? 'public' : 'private';

2. expr1 ?? expr2
如果 expr1 为 null，就返回 expr2 的值，否则返回 expr1 的值
String playerName(String name) => name ?? 'Guest';
```

级联操作符

```d
级联操作符是 ..,允许你对同一对象进行一系列的操作。
除了函数调用，您还可以访问同一对象上的字段。
这通常可以为您节省创建临时变量的步骤，并允许您编写更多流畅的代码。

querySelector('#confirm') // Get an object.
  ..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
  
querySelector() 返回一个 selector 对象
后面的 ..text、..classes、..onClick就是在 selector 对象上进行的
```

其他操作符

```d
1. ?. 
想要访问表达式的某个属性时，就可以使用这个，可以有效避免 NPE
var yourName = user?.name;

2. ?? 
在赋值时，可以使用 ??，若发现为空，可以为其赋默认值
var yourName = name ?? "Bob";

3. ??= 
expr1 ??= expr2 等效于 expr1 = expr1 ?? expr2 
就是判断 expr1 是否为null，如果为null的话，就使用默认值 expr2
user ??= User();
```

#### Flutter 的基础 —— Widget

**Widget 是什么？**

```
在 Flutter 中要用 Widget 构件 UI
当 Widget 状态发生变化，需要更新界面时，
框架会先计算从上一个状态转换到下一个状态所需的最小更改，
然后再去刷新界面
```

**Flutter Framework 里的 Widget**

Flutter Framework 层的架构图

```
 	Material Cupertino
		 Widgets
		 Rendering
Animation Painting Gestures
		 Foundation
```

在 Widgets 层下面，有：

- Rendering（渲染层）
- Animation、Painting、Gestures（动画、绘制、手势）
- Foundation（基础库层）

在开发中使用的都是封装好的东西，也就是 Widgets 上面的那层：

- Material & Cupertino

	```
	Widget 的风格
	- Material 用于 Android
	- Cupertino 用于 iOS
	```

**Widget 的结构：Widget树**

Widget 组合的结构是树，所以叫做 Widget树

父Widget 和 子Widget

```
在 Widget 树里，Widget 有包含和被包含的关系
- 父Widget：包含其他 Widget 的就叫 父Widget。
- 子Widget：被 父Widget 包含的 Widget 就叫 子Widget。
```

根 Widget

```
根 Widget 也叫 Root Widget
	
void main() => runApp(MyApp());
	
runApp（MyApp()里的参数 MyApp() 就是一个 Widget
MyApp 的作用只是封装一下，实际使用的 Widget 是 MaterialApp
这里的 MaterialApp 就是 根(Root)Widget
Flutter会默认把 根Widget 充满屏幕。
```

在 Flutter 中，根Widget 只能是以下三个：

```
WidgetsApp
是可以自定义风格的 根Widget。

MaterialApp 
是在 WidgetsApp 上添加了很多 material-design 的功能
Material Design 风格的根Widget

CupertinoApp
也是基于 WidgetsApp 实现的 iOS 风格的根Widget

这三个中最常用的是 MaterialApp，因为 MaterialApp 的功能最完善
MaterialApp 经常与 Scaffold 一起使用
```

**Widget 的标识符：Key**

```
因为 Flutter 采用的是 react-style 的框架
每次刷新 UI 的时候，都会重新构建新的 Widget树，
然后和之前的 Widget树 进行对，计算出变化的部分, 这个计算过程叫做 diff

在 diff 过程中，如果能提前知道哪些 Widget 没有变化，无疑会提高 diff 的性能，
这时候就需要使用到标识符
```

在 diff 过程中，如何知道哪些 Widget 没有变化呢？

```
为了在 diff 过程中，知道 Widget 有没有变化，
就需要给 Widget 添加一个唯一的标识符

在 Widget树 的 diff 过程中，查看刷新前后的 Widget树 有没有相同标识符的 Widget，
如果标识符相同，则说明 Widget 没有变化，否则说明 Widget 有变化。

假设 UI 刷新前，Widget树 是 A，在 A 里有一个标识符为 a 的 Widget,
在 UI 刷新后，重建的 Widget树 是 B，如果 B 里还有标识符为 a 的 Widget，则说明这个 Widget 没变，
但是如果 B 里没有标识符为 a 的 Widget，那么说明这个 Widget 发生了变化。
```

> 这个标识符在 Flutter 中就是 Key，所有 Widget 都有 Key 这一个属性。

Flutter 中如何在 diff 过程中判断哪些 Widget 没有变化？

```
Flutter 在 diff 过程中判断哪些 Widget 没有变化，稍微有些复杂，有两种情况：

- 默认情况下（ Widget 没有设置 Key）
当没有给 Widget 设置 Key 时，Flutter 会根据 Widget 的 runtimeType 和显示顺序是否相同来判断 Widget 是否有变化。
runtimeType 是 Widget 的类型，例如 Text 和 RaisedButton 就是不同的类型。

- Widget 有 Key
当给 Widget 设置了 Key 时，Flutter 是根据 Key 和 runtimeType 是否相同来判断 Widget 是否有变化。
```

**Key 的分类**

Local Key（局部Key）

```
在有相同父级的 Widget 中，Key 必须是唯一的，这样的 Key 叫做 局部Key。

局部Key 在 Flutter 中对应的抽象类是 LocalKey。
LocalKey 有不同的实现，主要的区别就是使用什么值来作为 Key 的值：

ObjectKey: 
将对象作为 Key 的值。

ValueKey: 
使用特定类型的值来作为 Key 的值。

UniqueKey: 
使用 UniqueKey 自己的对象作为 Key 的值，所以只与自身相等，称为 唯一Key。
```

Global Key（全局Key）

```
全局Key 是在整个APP中唯一的 Key。

全局Key 在 Flutter 中对应的抽象类是 GlobalKey。
GlobalKey 有不同的实现，主要区别是使用的场景不同：

LabeledGlobalKey
LabeledGlobalKey用于调试，不会用来比较 Widget 是否有变化。

GlobalObjectKey
将对象作为 Global Key 的值。
```

一般情况下我们不需要使用 Key，但是当页面比较复杂时，就需要使用 Key 去提升渲染性能。

**Widget 的分类：StatelessWidget 和 StatefulWidget**

```
因为渲染是很耗性能的，为了提高 Flutter 的帧率，就要尽量减少不必要的 UI 渲染，
所以 Flutter 根据 UI 是否有变化，将 Widget 分为：

StatefulWidget
StatefulWidget 是 UI 可以变化的 Widget，创建完后 UI 还可以在更改。

StatelessWidget
StatelessWidget 是 UI 不可以变化的 Widget，创建完后 UI 就不可以在更改。
```

**Widget 大全**

```
Flutter官网 上将 Widget 分为14类，总共有上百个 Widget：

Accessibility
Animation and Motion
Assets, Images, and Icons
Async
Basics
Cupertino (iOS-style widgets)
Input
Interaction Models
Layout
Material Components
Painting and effects
Scrolling
Styling
Text
```

**Flutter 中 Widget 的使用**

我们在 Flutter 中使用 Widget 的时候，有以下两点：

```
StatefulWidget 与 StatelessWidget
Flutter 的大部分 Widget 都可以分为 StatefulWidget 和 StatelessWidget 
这两类，所以要弄懂 StatefulWidget 与 StatelessWidget 的区别和使用范围。

MaterialApp 与 Scaffold
MaterialApp 大部分情况下要作为 Flutter 的 根Widget，
并且 MaterrialApp 经常和 Scaffold 搭配一起使用。
```

**StatefulWidget 及 State**

StatefulWidget 是 UI 可以变化的 Widget

实现 StatefulWidget，需要两部分组成：

```d
1. StatefulWidget
StatefulWidget 的主要功能就是创建 State

- 首先继承 StatefulWidget
- 实现 createState() 的方法，返回一个 State

class MyApp extends StatefulWidget {
  // This widget is the root of your application.

  String content;

  MyApp(this.content);

  @override
  State<StatefulWidget> createState() {
    // TODO: implement createState
    return MyAppState();
  }
}

2. State
- 首先继承 State，State 的泛型类型是上面定义的 Widget 的类型
- 实现 build() 的方法，返回一个 Widget
- 需要更改数据，刷新 UI 的话，调用 setState()

class MyApState extends State<MyApp> {

  void increment(){
    setState(() {
      widget.content += "d";
    });
  }
  
  @override
  Widget build(BuildContext context) {
    return ...
  }
}
```

State 的定义和功能

```d
State 用到了泛型，它的定义是这样子的：
State<T extends StatefulWidget>
```

```d
State 有两个功能：
1. build() —— 创建 Widget

2. setState() —— 刷新 UI

setState() 在源码里的定义如下
 @protected
 void setState(VoidCallback fn) {
 	// 执行无参函数 fn()，并把结果类型转换为 dynamic，并赋值给 result。
    final dynamic result = fn() as dynamic;
    // 触发 Widget 创建。
    _element.markNeedsBuild();
}

setState() 里要传入一个无参的函数，所以使用方法如下：
setState(() {
    widget.content += "d";
});

注意: 更改数据的代码必选在 setState() 之前写，
或者在 setState() 内的无参函数里写，才能刷新数据，否则是没有用的。
```

State 的成员变量

```
State 里面有三个重要的成员变量

widget
context

mounted
mounted 是 bool 类型，表示当前 State 是否加载到树里。
State 对象创建之后，initState() 创建之前，framework 通过与 BuildContext 相关联，来将 State 对象加载到树中，
此时 mounted 会变为 true，当 State dispose 之后, mounted 就变为 false。
```
> mounted 属性很有用，因为 setState() 只有在 mounted 为 true 的时候才能用，当 moundted 为 false 时调用会抛异常。

```d
因为 State 的状态比较复杂，如果 setState() 使用不注意，很容易抛异常，所以保险起见，mounted 一般这么用：

if(mounted){
    setState((){
        ...
    })
}

只有在确定 State mounted 之后，才调用 setState()。
```

**StatefulWidget 的生命周期**

因为 StatefulWidget 由 StatefulWidget 和 State 两部分组成，
所以也有 StatefulWidget 的生命周期 和 State 生命周期。

StatefulWidget的生命周期

```d
StatefulWidget 的生命周期很简单，只有一个，即 createState 函数：
createState()
```

State 的生命周期

```d
1. moundted is true
mounted 是 boolean，只有当mounted 为 true 时，才能使用 setState()。

2. initState

initState()方法是在创建 State 对象后要调用的第一个方法（在构造函数之后）。
一旦 initState()方法完成，State 对象就初始化完成了，BuildContext 也可以用了。
所以如果你要用 BuildContext，那么需要在 initState()之后的生命周期里用到。

可以在这里执行其的他初始化，例如执行依赖于 BuildContext 或 Widget 的初始化，
或者 animations、 controllers 等动画相关的初始化。

如果你要重写此方法，需要首先调用 super.initState() 方法。

3. didChangeDependencies
initState()方法运行完后，就立即运行 didChangeDependencies() 方法。
当 Widget 依赖的数据被调用时，此方法也会被调用。
此外，请注意，如果您的 Widget 链接到 InheritedWidget，则每次重建此窗口小部件时都会调用此方法。
如果重写此方法，则应首先调用 super.didChangeDependencies()。

4. build

build()方法在 didChangeDependencies()（或者 didUpdateWidget() ）之后调用。 这是构建Widget的地方。

每次 State 对象更改时（或者当 InheritedWidget 需要通知“已注册”的小部件时）都会调用此方法

为了强制重建，需要调用 setState()方法。

至此，一个 Widget 从创建到显示的声明周期就完成了

5. setState()
当状态有变化，需要刷新UI的时候，就调用 setState()，会触发强制重建 Widget

6. didUpdateWidget()

当 Widget 重建后，新的 Widget 会和旧的 Widget 进行对比，
如果新的 Widget 和旧的 Widget 的 runtimeType 和 Widget.key 都一样，
那么就会调用 didUpdateWidget()

在 didUpdateWidget() 里，会把新的 Widget 的配置赋值给 State，
相当于重新 initState() 了一次

调用完这个方法之后，再去调用 build() 方法

至此 setState() 的生命周期也完成了

7. deactive

当 State 从树中移除时，就会触发 deactive。
但是如果在这帧结束前，如果有其他地方使用到了这个Widget，
就会重新把 Widget 插入到树里，这就涉及到了 Widget 的重用，
Widget 的重用和 Key 有关。

这里使用不同的方法重用，会有不同的生命周期，所以这里使用的是虚线表示的。

8. dispose

当 StaefulWidget 从树中移除时调用 dispose()方法。
可以在这里执行一些清理逻辑（例如侦听器），重写此方法时，需要首先调用 super.dispose()。
至此完成了 Widget 销毁的生命周期

9. mounted is false

State 对象不能 remounted，所以一旦 mounted is false，就不能在使用 setState() ，会抛异常
```

```
State HotReload 的生命周期 -- reassemble

在开发期间，执行 HotReload，就会触发 reassemble()，这提供了重新初始化在 initState() 方法中准备的任何数据的机会，包括全局变量。

前面讲了，全局变量不能用 HotReload，但是可以在 reassemble() 里改值，但是并没有卵用，因为这个只会在 Debug 阶段 Hot Reload 的时候触发。

更改 reassemble() 里的 content 的值，然后执行 Hot Reload
```

State 的生命周期在代码中对应的方法如下：

```d
class XXXState extends State<XXX> {

  @override
  void initState() {
    // TODO: implement initState
    super.initState();
    print("initState");
    context.runtimeType;
  }

  @override
  void didChangeDependencies() {
    // TODO: implement didChangeDependencies
    super.didChangeDependencies();
    print("didChangeDependencies");
  }
  
  @override
  void didUpdateWidget(MyApp oldWidget) {
    // TODO: implement didUpdateWidget
    super.didUpdateWidget(oldWidget);
    print("didUpdateWidget");
  }


  @override
  Widget build(BuildContext context) {
    print("build");
    return ...
  }

  @override
  void dispose() {
    // TODO: implement dispose
    super.dispose();
    print("dispose");
  }

  @override
  void reassemble() {
    // TODO: implement reassemble
    super.reassemble();
    print("reassemble");
  }

}
```

**StatelessWidget**

StatelessWidget 是没有 State（状态）的 Widget，当 Widget 在运行时不需要改变时，就用 StatelessWidget。

```d
class MyApp extends StatelessWidget {
 
 @override
 Widget build(BuildContext context) {
   return ...
   );
 }
}
```
```d
1. 首先继承 StatelessWidget。
2. 必须要实现 build 函数，返回一个 Widget。
```

immutable（状态不可变）

```
StatelessWidget 是没有 State（状态）的，
而且 StatelessWidget 只能在加载/构建 Widget 时才绘制一次，
这意味着无法基于任何事件或用户操作重绘 StatelessWidget，
所以 StatelessWidget 是 immutable 的。
```

StatelessWidget 的生命周期

```d
StatelessWidget 的生命周期就只有一个，即 build 函数
```

StatelessWidget 使用注意事项

```
如果你想要为 StatelessWidget 赋值，
只能在 StatelessWidget 初始化的时候，通过构造函数传递一些额外的参数。
但是请记住，这些参数不会在以后阶段发生变化，即使发生变化，也只能按原值使用。
因为 StatelessWidget 只会渲染一次。
```

#### Flutter 写 UI 的方式 —— 声明式

```
在 Android 中通常使用 XML 来写 UI，XML 也是一种声明式，
但使用 XML 写 UI，是不能在代码里直接操作 UI，还需要一步 findViewById 的步骤；

在 iOS 中通常使用 命令式 的代码来写 UI ，
命令式 可以直接在代码里操作 UI，但是要写复杂的界面的时候，就会很痛苦。
```

```d
Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: Scaffold(
          body: Center(
              child: GestureDetector(
                child: Text(widget.content),
                onTap: increment,
              )
          ),
        )
    );
}

UI层级:
MaterialApp -> Scaffold -> Center -> GestureDetector -> Text
```

#### Widget 的深度理解

**组合大于继承**

```
在 Android和iOS中，要想实现自定义的 View 时，
都是实现一个 View 的子类，或者继承已有的 View。

但在 Flutter 中，要实现一个自定义的 Widget，
最好通过组合其他 Widget 来实现，而不是用继承
因为在 Flutter 中，Widget 组合大于继承
```

**Widget 是 UI 的配置信息**

```
Widget 并不会直接用于渲染，而只是 UI 的配置信息。
```

**Widget 是 一次性的**

```
当 Flutter App 启动时，会 build 一个 Widget 树，然后读取 Widget 树的配置去渲染 UI，
当你想要改变 UI 时，并不能通过更改 Widget 的值来刷新 UI，
而是通过更改状态，触发 build ，重新新建一个全新的 Widget 树，
通过和原来的 Widget 树进行对比，来刷新 UI，
所以每次刷新 UI，Widget 都会重建，所以 Widget 是 一次性的。
```

**Widget 是 不可变的**

```
根据 Flutter UI 的渲染过程，会首先建立一个 Widget 树，而且这个 Widget 树只会创建一次，当状态发生变化时，不是更改原来的 Widget，而是重新创建，所以 Widget 是 不可变的。
```

**Widget 是 轻量的**

```
如果要改变 UI ，就调用方法重新 build 一个 Widget 树，
虽然 Widget 树重新创建了，但是并不会引起 UI 的全部刷新，
而是会对比前后 Widget 树变化的部分，只刷新变的部分，
因此即使 Widget 随便创建消耗，也不会影响性能，
所以 Widget 是 轻量的
```

**如果 StatefulWidget 的 子Widget 里有 StatelessWidget，那么能不能让这个 StatelessWidget 的 UI 发生改变？**

```
当然可以。当一个 StatelessWidget 的 父Widget 是 StatefulWidget 时，
StatefulWidget 的状态发生变化触发 build Widget 的时候，
StatelessWidget 也可以重建，所以当然是可以的。
```

### Widget 的使用方法

经常用到的 Widget 可以分为以下五类

```
基础 Widget
手势识别 Widget
布局 Widget
容器类 Widget
可滚动 Widget
```

使用 Widget 的步骤

```
1. 先找到 Widget 的构造函数。
2. 看构造函数的哪些参数是必选的，哪些参数是可选的，必选的参数必须要赋值，可选的根据需要来赋值。
3. 为了更灵活的使用 Widget，需要知道 Widget 每个参数的作用和使用方法。
```



### Flutter实战篇

### Flutter开发进阶篇

### Flutter底层进阶篇
