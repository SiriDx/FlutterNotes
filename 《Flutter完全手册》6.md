## Flutter 开发进阶篇

### Flutter UI 渲染过程 —— Widget，Element，RenderObject

前面讲了 Flutter 采用了 react-style 的框架，并不是直接将 Widget 绘制在屏幕上。Flutter 将 Widget 渲染到屏幕上，总共经过了三个步骤：

- 创建 Widget 树
- 根据 Widget 树创建 Element 树
- 根据 Element 树创建 RenderObject 树

![image](https://user-gold-cdn.xitu.io/2019/3/8/1695dd3ad9e9ac16?imageslim)

```d
void main() => runApp(MyApp());

class MyApp extends StatefulWidget {

  MyApp();

  @override
  State<StatefulWidget> createState() {
    // TODO: implement createState
    return MyAppState();
  }
}

class MyAppState extends State<MyApp> {

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        title: 'Flutter Demo',
        theme: ThemeData(
          primarySwatch: Colors.blue,
        ),
        home: Scaffold(
          appBar: AppBar(title: Text("Flutter渲染 -- Widget，Element，RenderObject"),),
          body: Column(
            children: <Widget>[
              RaisedButton(onPressed: (){},child: Text("Button"),),
              Text("Hello Flutter!")
            ],
          )
        )
    );
  }

}
```

对应的是如下的三种视图树：

![image](https://user-gold-cdn.xitu.io/2019/3/8/1695dc61558264df?imageslim)

在 runApp 运行之后，就开始执行 Flutter UI 的渲染流程：

**1. 首先会创建一个 Widget 树**

在 Flutter 中 Widget 只是为 Element 提供配置信息，并不用于真正的渲染。而且当 StatefulWidget 调用 setState() 之后，Widget 都会重新创建；特别是当你使用动画的时候，Widget 甚至每帧都会重建。

那就有一个问题，Widget每帧都重建，不会有性能问题吗？

答案是不会，因为 Widget 只包含配置信息，不包含渲染，是很轻量的，所以即使每帧都重建，所以也不会有性能问题。

在 Flutter 开发 UI 界面都是使用 Widget。

**2. 创建 Element 树**

遍历 Widget 树时，通过调用 Widget 的 createElement() 方法，会创建对应的 Element 树。

在上面的视图树的图中，MaterialApp 对应的 Element 我写得是 MaterialApp 内的所有 Element，是因为 MaterialApp 有很多 Widget 组合而成，因此包含很多 Element，所以这里省略成了 MaterialApp 内的所有 Element，Scaffold 同理。这里每一个 Widget对应一个 Element。

而且由上面的视图树可知，Element 与 Widget 和 RenderObject 都有联系，Element 持有 Widget 和 RenderObject 的实例。

Element 树是不会重建的，只会进行更新，因为旧的 Widget 保存在 Element 里，当状态发生变化 Widget树 重建的时候，新生成的 Widget树 就会与保存在 Element 里旧的 Widget树 进行对比，发现变化的时候会更新 Element，Element 在更新 RenderObject。Element 的主要工作就是对比，是三个视图树里的核心部分。这个机制保证了虽然 Widget 每帧都重建，但是 Element 却只会更新变化的部分，因此保证了性能没有问题。

**3. 创建 RenderObject 树**

当Element mount 的时候，就会创建 RenderObject，RenderObject 就是实际负责渲染的部分，因此 RenderObject 的操作是很昂贵的，所以要尽可能的复用，而不是创建新的。

RenderObject 是通过 Widget 的 createRenderObject() 方法创建的，但并不是每一个 Widget 都有 RenderObjcet，只有 RenderObjectWidget(Widget的一个子类) 类型的 Widget 才有 RenderObjcet，例如上面视图树的 MyApp Widget 就没有 RenderObject，因为 MyApp 只是一层包装而已，如果自己没 有RenderObject,就会使用子类的 RenderObject。

### 总结

Flutter 使用三级视图树来渲染，就是为了提升渲染的性能。

- Widget 只含有配置信息，不负责渲染，非常轻量，因此可以大量频繁的创建。
- Element 负责 diff，只更新变化的部分。
- RenderObject 负责渲染。

## Flutter 的状态管理

### 1. 本地状态

当一个状态的作用范围在只在一个 Widget 里，其他 Widget 不需要关心这个 状态时，这种状态就是本地状态。

例如 BottomNavigationBar 里的 _index 字段，_index 用来保存当前选中的项:

```d
class MyHomepage extends StatefulWidget {
  @override
  _MyHomepageState createState() => _MyHomepageState();
}

class _MyHomepageState extends State<MyHomepage> {
  int _index = 0;

  @override
  Widget build(BuildContext context) {
    return BottomNavigationBar(
      currentIndex: _index,
      onTap: (newIndex) {
        setState(() {
          _index = newIndex;
        });
      },
      // ... items ...
    );
  }
}
```


这里的 \_index 就是本地状态，App 的其他部分完全不会关心当前在哪个页面，所以也不需要 \_index 的值，只有 \_MyHomepageState 关心 \_index 的值，所以 \_index 只存在于 \_MyHomepageState 里，而且 \_index 也不需要存储，因为当你在关闭 MyHomepage 后再打开，也不介意 \_index 重置为0。

所以这里可以看出本地状态的特点，就是：

- 私有的
- 暂时的

类似的，还有其他几个本地状态的例子：

- PageView 里表示当前是哪个页面的数据
- 复杂动画的播放进度

### 2. 全局状态

当状态在 App 的全部或部分使用时，这种状态就是全局状态。

例如用户的登录信息，整个 App 都需要用到，用户的登录信息就是全局状态；又例如电商 App 的购物车数据，在商品页面需要用到，在购物车页面也要用到，在订单页面也需要用到，因为 App 里的好几个部分都要用到购物车数据，所以购物车数据也是全局状态。

全局状态的特点就是：

- 共享的

类似的，还有其他几个全局状态的例子：

- 用户的数据信息
- 新闻 App 的文章的已读、未读状态数据

### 如何确定你的状态是本地状态还是全局状态？

要区分本地状态和全局状态，要看状态的作用范围。可以根据下图来划分：

![image](https://user-gold-cdn.xitu.io/2019/4/25/16a551aced2ee54f?imageslim)

当要确定一个数据是属于本地状态还是全局状态时，先看 这个数据需要被哪些部分使用，如果只有一个 Widget 使用这个数据，那么这个数据就是本地状态；如果有多个 Widget 使用，那么这个数据就是全局状态。

同时状态也不是一成不变的，可能随着 App 越来越复杂，原来是本地状态的会变为全局状态，就需要不断重构。比如 BottomNavigationBar 的 \_index，如果需要从外部去更改 \_index，那么 \_index 就从本地状态变为全局状态了。

### 状态管理框架

对本地状态和全局状态管理的框架就叫状态管理框架。

状态管理是一个复杂的主题，而且一直是 Flutter 的热门话题，目前有多种实现状态管理的框架，但一个好的状态管理框架应该具有如下的条件：

- 能管理好全局状态和本地状态
- UI 逻辑和业务逻辑应该是分离的
- 在框架的帮助下可以写出高质量的代码
- 框架应该提升 App 的性能
- 框架要容易理解，便于扩展

基于这些条件，我们将探索如下的状态管理框的优劣，从而找到适合的状态管理框：

- StatefulWidget 和 setState()
- InheritedWidget
- Scoped model
- BLoC
- Redux

#### StatefulWidget 和 setState() 的分析

StatefulWidget 就是有状态的 Widget，其内部管理状态用的就是 setState()。上一篇写的豆瓣电影 APP 用的就是 StatefulWidget 和 setState()，但是有两个很大的缺陷：

- UI 逻辑和业务逻辑没有分离
- 只能管理本地状态

如果使用 StatefulWidget 和 setState()，代码会随着 App 的增长，变得越来越难以维护，所以强烈推荐不要使用。

#### InheritedWidget

使用方法：

1. 定义 InheritedWidget，InheritedWidget 里存储全局的数据。
2. InheritedWidget 必须是父 Widget。
3. InheritedWidget 的子 Widget 通过特定的方法取到 InheritedWidget 的实例，然后就可以访问 InheritedWidget 的数据。
4. 当全局数据发生变化时，需要重新创建 InheritedWidget，重建的同时会根据条件通知 InheritedWidget 的子 Widget。


```d
class ShareDataInheritedWidget extends InheritedWidget {

  // 要存储的全局数据，通过构造函数传入
  String curCity;

  ShareDataInheritedWidget(this.curCity, {Widget child}): super(child:child);

  //定义一个便捷方法，方便子树中的 Widget 获取 ShareDataInheritedWidget 实例
  static ShareDataInheritedWidget of(BuildContext context) {
    return context.inheritFromWidgetOfExactType(ShareDataInheritedWidget);
  }

  /*
   * 当全局数据发生变化，InheritedWidget 发生重建，
   * 判断需不需要通知依赖 InheritedWidget 数据的子 Widget，
   * 返回 true 是通知，返回 false 是不通知
   */
  @override
  bool updateShouldNotify(InheritedWidget oldWidget) {
    return (oldWidget as ShareDataInheritedWidget).curCity != curCity;
  }
}
```

#### scopedModel

ScopedModel 是一个可以对状态进行管理的第三方库，该库是从 Fuchsia 的代码库中提取的。

**ScopedModel 的使用**

使用 ScopedModel 进行状态管理，要用到这个库提供的三个类，掌握这三个类的使用，就掌握了 ScopedModel。这三个类分别是：

- Model
- ScopedModel
- ScopedModelDescendant

Model

```d
Model 是类，用来存储全局状态，
当状态发生变化时，调用 notifyListeners() 方法，
就会通知给依赖这个状态的子 Widget，引起子 Widget 的创建。

我们需要继承 Model 类来写自己的 Models，
例如 我们写一个 UserModel，里面可以用来存储用户的用户名和 token。
```

ScopedModel

```d
ScopedModel 是 Widget，用来共享全局状态，
可以将 Model 里的状态传递给它的子 Widget。
为了将状态传递给子 Widget，我们需要使用 ScopedModel 来包 Model。
```

ScopedModelDescendant

```d
ScopedModelDescendant 也是 Widget，
用于需要依赖 Model 里状态的子 Widget 中，
而且会自动订阅 Model 里状态的变化，
当 Model 里状态发生变化时，就会触发 ScopedModelDescendant 的重建。
```

ScopedModelDescendant 有三个参数：

```d
1. child

child 的类型为 Widget，是可选的，
这个 child 是不需要依赖 Model 的 Widget，将会传递给 builder。

2. builder

builder 的类型为 ScopedModelDescendantBuilder，
是一个函数，是必选的，定义为：

typedef Widget ScopedModelDescendantBuilder<T extends Model>(
  BuildContext context,
  Widget child,
  T model,
);
函数的参数有 context、child、model，在函数的内部可以访问这三个参数，
builder 里的 child 就是上面提到的 child，
在 builder 通过 modle 参数访问状态数据，例如 modle.curCity， 
最后返回一个 Widget。

3. rebuildOnChange

rebuildOnChange 是 bool 类型，是可选的，
表示当状态发生变化的时候是否需要重建，
默认是 true，除非特别情况，这里应该永远是 true。
```

**子Widget 中获取 Model 的方法**

有两种方法可以在子 Widget 中获取 Model：

- 使用 ScopedModelDescendant，可以获取 Model，并且在 Model 变化时会重建。
- 使用 ScopedModel.of 的静态方法获取 Model 实例，但是无法收到 Model 变化的通知。如果需要频繁使用，可以在 Model 里写一个静态方法，下面会讲到。

#### BLoC

BLoC 的全称是 Business Logic Component，这个单词可以拆成两部分来看，第一部分是 Business Logic，就是业务逻辑，第二部分是 Component，就是组件，BLoC 其实指的就是独立出来的业务逻辑，因为 BLoC 模式的核心思想就是将 UI 逻辑和业务逻辑分开。为了达到这一目的，BLoC 模式里使用了 响应式编程（Reactive Programming），先来介绍一下 响应式编程，看 响应式编程 是如何实现 UI 逻辑和业务逻辑分离的。

**响应式编程**

响应式编程 使用异步数据流进行编程。在响应式编程里，所有的变化，不管是被动的还是主动的，比如 UI的点击、变量的变化、数据请求等，都会向异步数据流管道里发送消息，同时，在其他地方会监听数据流，将会收到消息并产生适当的响应。

从这段描述里就可以看到，响应式编程 里的数据不是通过参数传递来实现的，而是通过数据流管道来传送，因此数据的发送方和接收方就不需要有依赖关系，实现了解耦，这也是 UI 逻辑和业务逻辑分离的基础。

**使用响应式编程开发**

Flutter 响应式编程的三元素是：

- StreamController：数据流管道
- StreamSink：发出消息
- Stream：收到消息

```d
class CounterState extends State<CounterWidget> {

  int _count = 0;

  // StreamController：数据流管道
  static final StreamController<int> _streamController = StreamController<int>();

  // StreamSink：发出消息
  static final StreamSink<int> _sink = _streamController.sink;

  // Stream：收到消息
  static final Stream<int> _stream = _streamController.stream;

  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(title: Text("Flutter 响应式编程")),
        body: StreamBuilder(
          stream: _stream,
          initialData: 0,
          builder: (context, snapShot) {
            return Center(
              child: Text('${snapShot.data}'),
            );
          }
        ),
        floatingActionButton: FloatingActionButton(
          child: Icon(Icons.add),
          onPressed: () {
            _calculate();
          },
        ),
      ),
    );
  }

  void _calculate() {
    _sink.add(++_count);
  }
}
```

**flutter_bloc 的使用**

flutter_bloc 实现了 BLoC 模式，暴露出几个类，我们只要使用这几个类变可以轻松实现 BLoC 模式，这几个类分别是：

- Bloc

```d

// CityEvent 用来更新当前选中的城市
class CityEvent {
  String _city;

  get city => _city;

  CityEvent(this._city);
}

/*
Bloc 类里有两个泛型：Event 和 State，
Event 是从外部接受到的事件，
State 是输出 Widget 关心的状态。

abstract class Bloc<event, State> {
  //...
}
*/

/*
代表的是接受的参数是 CityEvent，
抛出的参数是 String，这里的 String 指的是选中的城市。
*/
class CityBloc extends Bloc<CityEvent, String> {
  String _curCity;

  CityBloc() {
    initData();
  }

  void initData() async {
    final prefs = await SharedPreferences.getInstance(); //获取 prefs
    String city = prefs.getString('curCity'); //获取 key 为 curCity 的值
    dispatch(CityEvent(city));
  }

  get curCity => _curCity;

  @override
  String get initialState => '';

  @override
  Stream<String> mapEventToState(CityEvent event) async* {
    _curCity = event.city;
    yield _curCity;
  }
}
```

```d

```

- BlocBuilder

```d
BlocBuilder 是一个 Widget，
它的功能类似于前面讲的 StreamBuilder，但是使用起来更简单，

BlocBuiler 监听 Bloc 的状态，
当状态发生变化时，就重建 Widget，
因此 BlocBuiler 有两个参数：Bloc 和 BlocWidgetBuilder，

我们可以看一下 BlocBuiler 的构造函数：

const BlocBuilder({
    Key key,
    
    // Bloc<E, S>	
    // 监听已经实现的 Bloc 类
    @required this.bloc,
    
    // BlocWidgetBuilder	
    // 监听 Bloc 类里状态的变化，重建 Widget
    @required this.builder,
  })
```

```d
BlocBuilder(
  bloc: BlocA(),
  builder: (context, state) {
    // return widget here based on BlocA's state
    return WidgetA();
  }
)
```

- BlocProvider

BlocProvider 是一个 Widget，可以将 Bloc 类提供给它的子 Widget。BlocProvider 经常用来作为依赖注入的部件，以便将单个 Bloc 类的实例，在多个 Widget 里共享。

```d
BlocA blocA = BlocA();

BlocProvider(
  bloc: blocA,
  child: ChildA(),
)

BlocProvider(
  bloc: blocA,
  child: ChildB(),
)
```

上面的使用方法，就将一个实例 blocA，分享到了 ChildA 和 ChildB 里。

然后如果想在 ChildA 或 ChildB 里拿到 BlocA 的实例，可以这么做：

```d
BlocProvider.of<BlocA>(context)
```

```d
class _MyHomePageState extends State<MyHomePage> {
  ...

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: BlocProvider<CityBloc>(
        bloc: CityBloc(),
        child: _widgetItems[_selectedIndex], //选中不同的选项显示不同的界面
      ),
      ...
    );
  }
  ...
}
```

- BlocProviderTree

BlocProviderTree 也是一个 Widget，前面讲的 BlocProvider 是把一个 Bloc 实例共享到多个 Widget 里，有时候一个 Widget 也需要用到多个 Bloc。

假设 ChildA 需要用到 BlocA、BlocB、BlocC，如果用 BlocProvider 实现的话，就是：

```d
BlocProvider<BlocA>(
  bloc: BlocA(),
  child: BlocProvider<BlocB>(
    bloc: BlocB(),
    child: BlocProvider<BlocC>(
      value: BlocC(),
      child: ChildA(),
    )
  )
)
```

使用 BlocProviderTree 可以把多个 BlocProvider 合成一个：

```d
BlocProviderTree(
  blocProviders: [
    BlocProvider<BlocA>(bloc: BlocA()),
    BlocProvider<BlocB>(bloc: BlocB()),
    BlocProvider<BlocC>(bloc: BlocC()),
  ],
  child: ChildA(),
)
```

- BlocListener

BlocListener 是一个 Widget，有两个参数：Bloc 和 BlocWidgetListener，类似于 BlocBuilder，需要接收一个 Bloc 类作为参数，但也有不同的地方， BlocBuilder 里的 BlocWidgetBuilder 需要返回 Widget，而 BlocWidgetListener 不用返回 Widget，它的返回类型是 void，所以用来做一些其他操作，例如：弹对话框、弹 SnackBar、跳转到新的页面等。

```d
BlocListener(
  bloc: _bloc,
  listener: (context, state) {
    if (state is Success) {
      Navigator.of(context).pushNamed('/details');
    }
  }
}
```

- BlocListenerTree

BlocListenerTree 是一个 Widget，用于将多个 BlockListener 合成一个 Widget。

例如：

```d
BlocListener<BlocAEvent, BlocAState>(
  bloc: BlocA(),
  listener: (BuildContext context, BlocAState state) {},
  child: BlocListener<BlocBEvent, BlocBState>(
    bloc: BlocB(),
    listener: (BuildContext context, BlocBState state) {},
    child: BlocListener<BlocCEvent, BlocCState>(
      bloc: BlocC(),
      listener: (BuildContext context, BlocCState state) {},
      child: ChildA(),
    ),
  ),
)
```

就可以用 BlocListenerTree 实现为：

```d
BlocListenerTree(
  blocListeners: [
    BlocListener<BlocAEvent, BlocAState>(
      bloc: BlocA(),
      listener: (BuildContext context, BlocAState state) {},
    ),
    BlocListener<BlocBEvent, BlocBState>(
      bloc: BlocB(),
      listener: (BuildContext context, BlocBState state) {},
    ),
    BlocListener<BlocCEvent, BlocCState>(
      bloc: BlocC(),
      listener: (BuildContext context, BlocCState state) {},
    ),
  ],
  child: ChildA(),
)
```

#### Redux

**redux.dart**

redux.dart 是用 Dart 语言写的 Redux 库

Redux 使用流程图：

![image](https://user-gold-cdn.xitu.io/2019/5/28/16afd93530ff8adb?imageslim)

- 如果 View 受到点击，就会抛出一个 Action，这里的 Action 就相当于 BloC 里的 Event。
- Action 会首先被 Middleware 接收，Middleware 用来处理一些耗时的操作，例如数据请求。在 Middleware 里的操作一般都是异步的。
- 当 Middleware 里的操作结束得到数据后，便会把 Action 和数据一起抛给 Reducer 处理，Reducer 会根据不同的业务逻辑，对数据在进行处理，生成一个新的 State，并抛给 Store。
- Store 是用来存取状态的，当 Store 接收到从 Reducer 传过来的最新状态后，便会用最新的状态去刷新 View。

在 Redux 中，Store 是用来存储和共享状态的，Action 和 Reducer 的作用是将业务逻辑和 UI 逻辑分离，Action 是将 View 和 业务逻辑分离，Reducer 是将业务逻辑和 Store 分离，Middleware 的作用是将耗时的操作异步化。

**flutter_redux**

- Reducer

Reducer 用于响应 Action，进行逻辑操作，然后生成新的 State。

```d
/* 
state	
State	表示的是前一个状态
*/

/*
action	dynamic	
action 表示一个操作，
可以是 Enum 或 Class，
如果 action 没有参数就是用 Enum，有参数就用 Class
*/

typedef State Reducer<State>(State state, dynamic action);
```

- Middleware
- Store
- StoreProvider
- StoreBuilder
- StoreConnector
