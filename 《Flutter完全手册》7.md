## Flutter Isolate

Flutter 的代码都是默认跑在 root Isolate 上

### 创建自己的 Isolate

#### dart:isolate

```d
 external static Future<Isolate> spawn<T>(
      void entryPoint(T message), T message,
      {bool paused: false,
      bool errorsAreFatal,
      SendPort onExit,
      SendPort onError});
      
/*
spawn 方法，必传参数有两个，
函数 entryPoint 和参数 message

函数: 函数必须是顶级函数或静态方法
参数: 参数里必须包含 SendPort
*/
```

```d
import 'dart:async';
import 'dart:io';
import 'dart:isolate';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

//一个普普通通的Flutter应用的入口
//main函数这里有async关键字，是因为创建的isolate是异步的
void main() async{
  runApp(MyApp());
  
  //asyncFibonacci函数里会创建一个isolate，并返回运行结果
  print(await asyncFibonacci(20));
}

//这里以计算斐波那契数列为例，返回的值是Future，因为是异步的
Future<dynamic> asyncFibonacci(int n) async{
  //首先创建一个ReceivePort，为什么要创建这个？
  //因为创建isolate所需的参数，必须要有SendPort，SendPort需要ReceivePort来创建
  final response = new ReceivePort();
  //开始创建isolate,Isolate.spawn函数是isolate.dart里的代码,_isolate是我们自己实现的函数
  //_isolate是创建isolate必须要的参数。
  await Isolate.spawn(_isolate,response.sendPort);
  //获取sendPort来发送数据
  final sendPort = await response.first as SendPort;
  //接收消息的ReceivePort
  final answer = new ReceivePort();
  //发送数据
  sendPort.send([n,answer.sendPort]);
  //获得数据并返回
  return answer.first;
}

//创建isolate必须要的参数
void _isolate(SendPort initialReplyTo){
  final port = new ReceivePort();
  //绑定
  initialReplyTo.send(port.sendPort);
  //监听
  port.listen((message){
    //获取数据并解析
    final data = message[0] as int;
    final send = message[1] as SendPort;
    //返回结果
    send.send(syncFibonacci(data));
  });
}

int syncFibonacci(int n){
  return n < 2 ? n : syncFibonacci(n-2) + syncFibonacci(n-1);
}
```

#### Isolate有什么用？

```d
因为 Root Isolate 会负责渲染，还有 UI 交互，
如果我们有一个很耗时的操作呢？

前面知道 Isolate 里是一个 Event loop（事件循环），
如果一个很耗时的 task 一直在运行，
那么后面的UI操作都被阻塞了，
所以如果我们有耗时的操作，就应该放在 Isolate 里
```

#### 使用 Isolates 的方法

使用 Isolates 的方法种：

- 高级API：Compute 函数 (用起来方便)
- 低级API：ReceivePort

**Compute 函数**

```d
void main() async{
  //调用compute函数，compute函数的参数就是想要在isolate里运行的函数，和这个函数需要的参数
  print( await compute(syncFibonacci, 20));
  runApp(MyApp());
}

int syncFibonacci(int n){
  return n < 2 ? n : syncFibonacci(n-2) + syncFibonacci(n-1);
}
```
compute 函数里用到的函数别名：

```d
// Q R是泛型，ComputeCallback是一个有参数Q，返回值为R的函数
typedef ComputeCallback<Q, R> = R Function(Q message);
```
源码

```d
//compute函数 必选参数两个，已经讲过了
Future<R> compute<Q, R>(ComputeCallback<Q, R> callback, Q message, { String debugLabel }) async {
  //如果是在profile模式下,debugLabel为空的话，就取callback.toString()
  profile(() { debugLabel ??= callback.toString(); });
  final Flow flow = Flow.begin();
  Timeline.startSync('$debugLabel: start', flow: flow);
  final ReceivePort resultPort = ReceivePort();
  Timeline.finishSync();
  //创建isolate,这个和前面讲的创建isolate的方法一致
  //还有一个，这里传过去的参数是用_IsolateConfiguration封装的类
  final Isolate isolate = await Isolate.spawn<_IsolateConfiguration<Q, R>>(
    _spawn,
    _IsolateConfiguration<Q, R>(
      callback,
      message,
      resultPort.sendPort,
      debugLabel,
      flow.id,
    ),
    errorsAreFatal: true,
    onExit: resultPort.sendPort,
  );
  final R result = await resultPort.first;
  Timeline.startSync('$debugLabel: end', flow: Flow.end(flow.id));
  resultPort.close();
  isolate.kill();
  Timeline.finishSync();
  return result;
}

@immutable
class _IsolateConfiguration<Q, R> {
  const _IsolateConfiguration(
    this.callback,
    this.message,
    this.resultPort,
    this.debugLabel,
    this.flowId,
  );
  final ComputeCallback<Q, R> callback;
  final Q message;
  final SendPort resultPort;
  final String debugLabel;
  final int flowId;

  R apply() => callback(message);
}

void _spawn<Q, R>(_IsolateConfiguration<Q, R> configuration) {
  R result;
  Timeline.timeSync(
    '${configuration.debugLabel}',
    () {
      result = configuration.apply();
    },
    flow: Flow.step(configuration.flowId),
  );
  Timeline.timeSync(
    '${configuration.debugLabel}: returning result',
    () { configuration.resultPort.send(result); },
    flow: Flow.step(configuration.flowId),
  );
}

```

**ReceivePort**	

```d
import 'dart:async';
import 'dart:io';
import 'dart:isolate';

import 'package:flutter/foundation.dart';
import 'package:flutter/material.dart';

//一个普普通通的Flutter应用的入口
//main函数这里有async关键字，是因为创建的isolate是异步的
void main() async{
  runApp(MyApp());
  
  //asyncFibonacci函数里会创建一个isolate，并返回运行结果
  print(await asyncFibonacci(20));
}

//这里以计算斐波那契数列为例，返回的值是Future，因为是异步的
Future<dynamic> asyncFibonacci(int n) async{
  //首先创建一个ReceivePort，为什么要创建这个？
  //因为创建isolate所需的参数，必须要有SendPort，SendPort需要ReceivePort来创建
  final response = new ReceivePort();
  //开始创建isolate,Isolate.spawn函数是isolate.dart里的代码,_isolate是我们自己实现的函数
  //_isolate是创建isolate必须要的参数。
  await Isolate.spawn(_isolate,response.sendPort);
  //获取sendPort来发送数据
  final sendPort = await response.first as SendPort;
  //接收消息的ReceivePort
  final answer = new ReceivePort();
  //发送数据
  sendPort.send([n,answer.sendPort]);
  //获得数据并返回
  return answer.first;
}

//创建isolate必须要的参数
void _isolate(SendPort initialReplyTo){
  final port = new ReceivePort();
  //绑定
  initialReplyTo.send(port.sendPort);
  //监听
  port.listen((message){
    //获取数据并解析
    final data = message[0] as int;
    final send = message[1] as SendPort;
    //返回结果
    send.send(syncFibonacci(data));
  });
}

int syncFibonacci(int n){
  return n < 2 ? n : syncFibonacci(n-2) + syncFibonacci(n-1);
}
```

## Flutter 底层进阶篇

Flutter 框架是一个多层架构，每层构建在前一层之上，下图展示了 Flutter架构的各个部分：

![image](https://user-gold-cdn.xitu.io/2019/2/11/168dc4556a12d7b9?imageslim)

总共有三层，从下到上依次为：

- Embedder

Embedder 是平台指定的语言实现，主要处理平台相关的操作，是为了 Flutter 能适配各种平台的嵌入层。Embedder 有 Android 的实现，也有 iOS 的实现，也有 Linux、MacOS、Windows 的实现。

- Engine

Engine 层由 C/C++ 实现，Flutter Engine 为 Flutter 应用提供了运行环境，是 Flutter 的核心。

- Framework

Framework 层由 Dart 实现，是 Flutter 开发的框架，开发 Flutter 的 APP ，大部分时间都是和这一层打交道。

### Flutter 架构 -- Engine

Engine 层由 C/C++ 实现，Flutter Engine 为 Flutter 应用提供了运行环境，是 Flutter 的核心。

![image](https://user-gold-cdn.xitu.io/2019/2/14/168eaaf6df4e900b?imageslim)


- 渲染相关：Composition 、 Rendering 、 Frame Scheduling 、 Frame Pipelining
- Dart 相关：Service Protocol 、 Dart Isolate Setup 、 Dart VM Managemnt
- 平台通道：Platform Channels
- 系统事件：System Events
- 资源解析：Asset Resolution
- 文字渲染：Text Layout

其中的核心功能，包括动画和图形渲染，文件和网络 I/O，Platform Channels ，插件体系结构，以及 Dart 的运行时环境和编译工具链等。

**渲染引擎：Skia**

Skia 是 Flutter 的图形引擎，是 Flutter 渲染过程中的重要一环。

Skia 是 Google 的跨平台 2D 向量图形库，而且已经发展的很成熟：在 2005 年被 Google 收购后，已经成为 Google Chrome，Chrome OS，Android, Mozilla Firefox, Firefox OS 等众多产品的图形引擎，支持的平台包括 Windows7+,macOS 10.5+,iOS8+,Android4.1+,Ubuntu14.04+ 等，并且 Skia 发展的已经很稳定了。

Android 是自带 Skia，所以 Android 端 Flutter 不需要打包 Skia，所以在 Android端，Flutter 的包大小会小一些；而 iOS 不带 Skia，所以 iOS 的 Flutter 包会大一些。

**Dart 运行时环境**

Dart 运行时环境包括 Dart VM 及其他 Dart 运行所需要的库。

- Dart VM

```
Dart VM 除了实现普通 Dart 的核心库之外，
还增加了一个 dart:ui 库，这个库是专门为 Flutter 定制的，
提供 Skia 和 Shell 功能的低级 API。

而且在 Flutter Debug 模式中，Flutter Engine 使用的是 Dart VM ，而 Dart VM 支持 JIT（即时编译），从而使 Flutter 在 Debug 阶段有 Hot Reload 的功能。
```

- Dart 相关库

```
在 Release 模式下，Flutter 不会带 Dart VM，
因为 Flutter 使用的是 AOT（静态编译），会编译成 Native Arm Code，
所以不在需要 Dart VM，
但是运行 Native Arm Code 也需要其他 Dart 相关库，
例如 Garbage Collection (GC，垃圾回收) 等，这些库不包含在 Dart VM 里。
```

为什么 Dart 可以运行在不同的平台上？

> 是因为 Dart 并不是直接运行在平台上，而是运行在 Flutter Engine 上，Flutter Engine 为 Dart 提供了运行环境。

**Platform Channel**

Platform Channel 是平台通道，用于 Flutter 与 Native 通信：

- 应用的 Flutter 部分通过平台通道（platform channel）将消息发送到 Native（iOS 或 Android）。

- Native 接收消息，然后调用 Native 的 代码处理， 然后将响应发送回 Flutter 。

Platform Channel 是一个很重要的功能，尤其是 Flutter 与 Native 混合开发的时候，后面会具体讲到怎么使用。

### Flutter 架构 -- Embedder

Embedder 是平台指定的语言实现，主要处理平台相关的操作，是为了 Flutter 能适配各种平台的嵌入层。Embedder 有 Android 的实现，也有 iOS 的实现，也有 Linux、MacOS、Windows 的实现。

![image](https://user-gold-cdn.xitu.io/2019/2/14/168e96377454197e?imageslim)

上图是 Embedder 层的截图，Embedder 下面标了 Platform Specific，表示 Embedder 是基于平台实现的，包含的功能有：

- Render Surface Setup ：渲染设置
- Native Plugins ：平台的插件
- Packaging ：包装 Flutter AOT 的产物 ：Native Arm Code，使 Native Arm Code 在平台上运行。
- Thread Setup ：Flutter 运行线程设置
- Event Loop Interop ：Flutter 事件循环

可以看到主要都是底层的机制，而且都是和平台相关的实现，除此之外 Embedder 还有另一个很重要的功能，就是 Embedder 是 Flutter Engine 和 Platform（平台）之间交互的桥梁。

#### Embedder API ：交互的桥梁

Flutter Engine 和 Platform（平台）靠什么交互呢？

就是 Embedder API。

在 Flutter Engine 和 Platform 中间有一层 API，就是 Embedder API。

Flutter Engine 通过这些 Embedder API 调用平台的能力，而平台通过实现这些 Embedder API，就可以在不同的平台上运行 Flutter Engine。

**Shell ：Embedder API的实现**

实现 Embedder API 的叫做 Shell，Google 为 Flutter 实现了不同平台的 Shell，比如就有 Android Shell 和 iOS Shell ，也有 Linux Shell、MacOS Shell、Windows Shell，这些 Shell 在不同平台上，用平台指定的语言实现，并提供相关 IME（例如：屏幕）和系统应用程序声明周期事件的通信、渲染、插件、线程创建和管理、事件循环等。

### Flutter 架构 -- Framework

Framework 层由 Dart 实现，是 Flutter 开发的框架，开发 Flutter 的 APP ，大部分时间都是和这一层打交道。

![image](https://user-gold-cdn.xitu.io/2019/2/14/168eb1ddc1f9a6c0?imageslim)

上图是 Framework 层的截图，Framework 下面标了 Dart，表示了 Framework 层由 Dart 语言实现，Frameworl 层也是一个多层架构，从上到下分别是：

- Foundation（基础库层）

```d
Foundation 库即是 dart:ui 库，为 Flutter 提供了基本的类和函数，
包括处理与 Flutter Engine 层的通信，以及用于 Flutter 框架的最低级别服务，
例如驱动输入、图形文本、布局和渲染等。

使用这一层的功能也能构建 Flutter APP,但是因为这一层没有封装，
你只能手动计算布局坐标，手动捕捉用户输入和混合动画，
用 Foundation 去写 APP 将非常复杂，成本也很大。
所以需要对 Foundation 层进行封装。
```

- Rendering（渲染层）

```d
渲染层包括 Rendering、Animation、Painting、Gestures，
是 Foundation 之上的第一个抽象层，是对 Foundation 层的封装。

这一层主要是完成 UI 的布局和绘制，
为了优化这一复杂的过程，
采用了智能的算法去缓存那些昂贵的计算，
使得每次的迭代量最小，可以提高渲染和绘制的性能。
```

- Widget（组件层）

```d
Widget 就是 Flutter UI 的基本元素，
提供了可以在 Flutter 中使用的 UI 组件，
是我们实际开发中最常用的元素，所有的 Widget 可以分成以下四类：

- Structural Widget（布局组件），例如 Column 和 Row 等，控制布局的。
- Visual Widget（绘制组件），例如 Text 和 Image 等,在屏幕上显示内容的。
- Interaction Widget（交互组件），例如 GestureDetector 等，处理用户手势的。
- Platform Widget（平台组件），例如 AndroidView 等，将平台的 View 嵌入到 Flutter 中使用的

我们也可以用这些组件实现自己的组件，
但是是通过组合的方式，因为在 Flutter 中，组合大于继承。
```

- Material 和 Cupertino 风格的组件

```d
Flutter 为了减轻开发人员的工作量，
实现了两种不同风格的组件：Material 和 Cupertino 。

Material 用于 Android，Cupertino 用于 iOS。
有了这些组件，开发人员不需要再做额外的工作，
就可以让 Flutter 的 UI 风格适应不同的平台，
让 Flutter UI 获得和 Native UI 一样的使用体验。
```

总之，层级越高，更易于处理，但低层级的可以提供更复杂的细粒度的控制。

## Thread Model（线程模型）

### 创建和管理线程

在 Flutter Engine 中定义了四种 Task Runners（任务运行器），Task Runners 是需要运行在平台提供的线程上的， 所以 Flutter Engine 是不创建和管理线程的，是由 Embedder 为 Flutter Engine 创建和管理线程（包括线程里的消息循环），这四种 Task Runners 分别是：

- Platform Task Runner
- UI Task Runner
- GPU Task Runner
- IO Task Runner

理解这四种 Task Runner ，可以让我们知道我们写的代码是运行在哪个线程上，从而知道可以做什么，不可以做什么。

#### Platform Task Runner

**所在的线程**

Platform Task Runner 运行所在的线程叫 Platform Thread，Platform Thread 必须要运行在平台的主线程上。

**功能**

处理平台（Android/iOS）的消息

**Q&A**

- 为什么一定要是平台的主线程？

```
因为 Platform Task Runner 的功能是要处理平台的消息，
但是平台的 API 都是只能在主线程调用，
所以 Platform Task Runner 运行所在的平台的线程必须是主线程
```

- 一个 Flutter Engine 有一个还是多个 Platform Thread？

```
一个 Flutter Engine 对应一个 Platform Thread
（一个 Flutter 应用启动的时候会创建一个 Engine 实例，
Engine创建的时候会创建一个 Platform Thread 供 Platform Task Runner 使用）
```

- 如果阻塞 Platform Thread 会有什么后果？

```
阻塞 Platform Thread 不会直接导致 Flutter 应用的卡顿
（跟 iOS 、Android 主线程不同）。

尽管如此，也不建议在这个 Runner 执行繁重的操作，
长时间卡住 Platform Thread 应用有可能会被系统 Watchdog 强杀。
```

#### UI Task Runner

**所在的线程**

UI Task Runner 运行所在的线程叫 UI Thread，UI Thread 对必须运行在平台的哪个线程没有特别要求，但在 Android 和 iOS 上都是子线程。

这里很容易让大家误会，因为一看名字，UI Thread，第一反应，UI ? 那肯定是主线程，其实并不是，因为这里的 UI 指的是 Flutter 的 UI，Flutter 的 UI 不需要在平台的主线程里渲染，相反 Flutter UI Thread 为了不阻塞平台的主线程，提高 Flutter UI 渲染的性能，反而创建了新的线程，所以在 Android 和 iOS 上，UI Task Runner 都是运行在子线程的，所以 UI Task Runner 可以理解成是 Flutter 的主线程，但却运行在平台的子线程上。

**功能**

1）用于执行 Dart Root Isolate 代码

2）渲染逻辑，告诉 Engine 最终的渲染

3）处理来自 Native Plugins 的消息

4）timers

5）microtasks

6）异步 I/O 操作( sockets、file handles 等)

**Q&A**

UI Task Runner 和 Root Isolate 的关系？

```
Isoloate 是 Dart 里的概念，
它是类似于线程(thread)但不共享内存的独立运行的 worker，
是一个独立的 Dart 程序执行环境。
在 Flutter 中默认执行 Dart 代码的就是 Root Isolate。

Root Isolate 是运行在 UI Thread 上的，
所以这就是为什么 isolate 就可以理解为单线程，
而且 UI Thread 上有和平台主线程一样的 event loop 架构，
是为了处理 Root Isolate 里的消息。
```

- 如果阻塞 UI Thread 会有什么后果？

```
阻塞 UI Thread 会直接导致 Flutter 应用卡顿掉帧.
```

#### GPU Task Runner

**所在的线程**

GPU Task Runner 运行所在的线程叫 GPU Thread，GPU Thread 对必须运行在平台的哪个线程没有特别要求，但在 Android 和 iOS 上是子线程。

**功能**

GPU Task Runner 主要用于执行设备 GPU 的指令。在 UI Task Runner 创建 Layer Tree，在 GPU Task Runner 将 Layer Tree 提供的信息转化为平台可执行的 GPU 指令。

**Q&A**

- UI Task Runner 和 GPU Task Runner 都有负责渲染，他们是跑在同一个子线程上吗？

```
UI Task Runner 和 GPU Task Runner 跑在不同的线程。
GPU Runner 会根据目前帧执行的进度去向 UI Task Runner 要求下一帧的数据，
在任务繁重的时候可能会告诉 UI Task Runner 延迟任务。

这种调度机制确保 GPU Task Runner 不至于过载，
同时也避免 了UI Task Runner 不必要的消耗。
```

- 如果阻塞 GPU Thread 会有什么后果？

```
在此线程耗时太久的话，会造成 Flutter 应用卡顿，
所以在 GPU Task Runner 尽量不要做耗时的任务，
例如加载图片的时候，去读取图片数据，就不应该放在 GPU Task Runner，
而是放在接下来要讲的 IO Task Runner。
从性能考虑，建议为 GPU Task Runner 单独开一个线程。
```

#### IO Task Runner

**所在的线程**

IO Task Runner 运行所在的线程叫 IO Thread，IO Thread 对必须运行在平台的哪个线程没有特别要求，但在 Android 和 iOS 上是子线程。

**功能**

1）主要功能是从图片存储（比如磁盘）中读取压缩的图片格式，将图片数据进行处理为 GPU Runner 的渲染做好准备。IO Runner 首先要读取压缩的图片二进制数据（比如 PNG，JPEG），将其解压转换成 GPU 能够处理的格式然后将数据上传到 GPU。

2）加载其他资源文件

**Q&A**

- 如果阻塞 IO Thread 会有什么后果？

```
阻塞 IO Thread，不会影响 Flutter UI 的渲染，
所以阻塞 IO Thread 不会影响 Flutter，
但加载图片和资源的时候可能会延迟，
所以从性能角度考虑，还是建议为 IO Task Runner 单独开一个线程。
```

### 各个平台的线程配置

#### iOS

为每个引擎实例的 UI，GPU 和 IO 任务运行程序创建专用线程。所有引擎实例共享相同的 Platform Thread 和 Platform Task Runner。

#### Android

为每个引擎实例的 UI，GPU 和 IO 任务运行程序创建专用线程。所有引擎实例共享相同的 Platform Thread 和 Platform Task Runner。

## Event Loop(事件循环)及代码运行顺序

Flutter 是用 Dart 来写的，Dart 没有进程和线程的概念，所有的 Dart代码 都是在 Isolate上 运行的，那么 Isolate 到底是什么？

### 同步代码和异步代码

- 异步代码：就是以 Future 等修饰的代码
- 同步代码：除了异步代码，平常写的代码就是同步代码

在 Dart 中这两类代码是不同的：

**1.运行顺序不同**

同步代码和异步代码运行的顺序是不同的：

```
先运行同步代码，在运行异步代码
```

就是，即使我异步代码写在最前面，同步代码写在最后面，不好意思，我也是先运行后面的同步代码，同步代码都运行完后，在运行前面的异步代码。

**2.运行的机制不同**

异步代码是运行在 event loop 里的，这是一个很重要的概念，这里可以理解成 Android 里的 Looper 机制，是一个死循环，event loop 不断的从事件队列里取事件然后运行。

### event loop 架构

event loop 大致的运行图：

![image](https://user-gold-cdn.xitu.io/2019/1/8/168297301229dbb9?imageslim)

这个很好理解，事件 events 加到 Event queue 里，Event loop 循环从 Event queue 里取 Event 执行。

event loop 详细的运行图：

![image](https://user-gold-cdn.xitu.io/2019/1/8/1682974dd5b630bf?imageslim)

从这里看到，启动 app（start app）后：

- 先查看 MicroTask queue 是不是空的，不是的话，先运行 microtask
- 一个 microtask 运行完后，会看有没有下一个 microtask，直到 Microtask queue 空了之后，才会去运行 Event queue 3.在 Evnet queue 取出一个 event task 运行完后，又会跑到第一步，去运行 microtask

MicroTask 和 Event ，这代表了两个不同的异步 task

#### 1. MicroTask

dart:async 提供的异步方法，使用方式：

```d
// Adds a task to the 先查看MicroTask queue.
scheduleMicrotask((){
  // ...code goes here...
}); 
```

或

```d
new Future.microtask((){
    // ...code goes here...
});
```

> 如果想让任务能够尽快执行，就用 MicroTask

#### 2.Event

Future 修饰的异步方法，使用方式：

```d
// Adds a task to the Event queue.
new Future(() {
  // ...code goes here...
});
```

### 代码运行顺序

```d
import 'dart:async';
void main() {
  print('main #1 of 2');
  scheduleMicrotask(() => print('microtask #1 of 3'));

  new Future.delayed(new Duration(seconds:1),
      () => print('future #1 (delayed)'));

  new Future(() => print('future #2 of 4'))
      .then((_) => print('future #2a'))
      .then((_) {
        print('future #2b');
        scheduleMicrotask(() => print('microtask #0 (from future #2b)'));
      })
      .then((_) => print('future #2c'));

  scheduleMicrotask(() => print('microtask #2 of 3'));

  new Future(() => print('future #3 of 4'))
      .then((_) => new Future(
                   () => print('future #3a (a new future)')))
      .then((_) => print('future #3b'));

  new Future(() => print('future #4 of 4'))
  .then((_){
    new Future(() => print('future #4a'));
  })
  .then((_) => print('future #4b'));
  scheduleMicrotask(() => print('microtask #3 of 3'));
  print('main #2 of 2');
}
```

- 首先运行同步代码
- 接下来是异步代码

```d
Dart 的 Event Loop 是
先判断 microtask queue 里有没有task，有的话运行 microtask， 
microtask 行完后，在运行 event queue 里的 event task,
一个 event task 运行完后，再去运行 microtask queue，
然后在运行 event queue。
```

- microtask queue
- event queue event queue 还有有特殊的情况需要考虑：

```d
Future.delayed
需要延迟执行的，Dart 是怎么执行的呢，
是在延迟时间到了之后才将此 task 加到 event queue 的队尾，
所以万一前面有很耗时的任务，那么你的延迟 task 不一定能准时运行

Future.then

Future.then 里的 task 是不会加入到 event queue 里的，
而是当前面的 Future 执行完后立即掉起，
所以你如果想保证异步 task 的执行顺序一定要用 then，
否则 Dart 不保证 task 的执行顺序

scheduleMicrotask

一个 event task 运行完后，
会先去查看 Micro queue 里有没有可以执行的 micro task。
没有的话，在执行下一个 event task
```

最后的结果就是：

```d
main #1 of 2
main #2 of 2
microtask #1 of 3
microtask #2 of 3
microtask #3 of 3
future #2 of 4
future #2a
future #2b
future #2c
microtask #0 (from future #2b)
future #3 of 4
future #4 of 4
future #4b
future #3a (a new future)
future #3b
future #4a
future #1 (delayed)
```
