## 可滚动 Widget

> 前面介绍的 Flex、Row、Column 都是不可滑动的，如果当 子Widget 的大小超过 Flex、Row或Column 之后，就会报 OverLoad. 
为了显示这些长度超过屏幕范围的 Widget 和 列表项，Flutter 提供了多种 可滚动的Widget，这些 可滚动Widget 的使用范围都不同，虽然也有 ScrollView 和 ListView,但和 Android、iOS 里的用法都略有不同.

#### SingleChildScrollView

> SingleChildScrollView 是只能包含一个 子Widget 的可滚动Widget。
> 如果有很多 子Widget，那么需要用 ListBody 或者 Column 或者其他 Widget 来嵌套这些 子Widget，然后在 SingleChildScrollView 里使用。

#### ListView

> ListView 是可以线性排列 子Widget 的可滚动Widget。ListView 可以和数据绑定用来实现瀑布流。如果有很多 子Widget，能使用 ListView 的就不要使用 SingleChildScrollView，因为 ListView 的性能比 SingleChildView 好。

**ListView 的辅助 Widget**

```
ListTile：一个固定高度的行，通常包含一些文本，以及一个行前或行尾图标。
Stepper：一个 Material Design 步骤指示器，显示一系列步骤的过程
Divider：一个逻辑1像素厚的水平分割线，两边都有填充
```

#### CustomScrollView

> CustomScrollView 是可以使用 slivers 来自定义滑动效果的可滚动Widget。

**slivers**

```
slivers 指的是以 Sliver 开头的一系列 Widget，
例如：SliverList、SliverGrid、SliverAppBar 等，
Sliver 有“小片”的意思，在 Flutter 中，指的是具有特定滚动效果的可滚动块，
它们只能用在 CustomScrollView 里，
多个 Sliver 拼在 CustomScrollView 里来实现特定的效果。
```

**CustomScrollView 也可以实现 ListView 的功能**

```
CustomScrollView 不像 SingleChildScrollView 一样只能包含一个 子Widget，
CustomScrollView 可以实现比 ListView 更复杂的滑动效果，例如：吸顶，
所以当 ListView 不能实现一些滑动效果时，就应该使用 CustomScrollView，
但是如果没有特殊的效果，而是数据展示，就使用 ListView。
```

#### GridView

GridView 是一个可以构建二维网格列表的 可滚动Widget。

#### PageView

PageView 是可以一页一页滑动的 可滚动Widet。

### SingleChildScrollView

SingleChildScrollView 是只能包含一个 子Widget 的可滚动Widget。

**SingleChildScrollView 的构造函数及参数说明**

```d
class SingleChildScrollView extends StatelessWidget {
  const SingleChildScrollView({
    Key key,
    // Axis	滑动的方向
	// 默认为 Axis.vertical，垂直方向可滑动
    this.scrollDirection = Axis.vertical,
    /*
    控制 SingleChildScrollView 是从头开始滑，还是从尾开始滑，
    默认为 false，就是从头开始滑
    例如当 scrollDirection 为 Axis.vertical,即垂直方向可滑动，
    那么 reverse 为 false，就是从头部滑到底部，
    当 reverse 为 true 时，就是从底部滑到头部
    */
    this.reverse = false,
    
  	// 	SingleChildScrollView 插入 子Widget 时的内边距  
    this.padding,
    
    /*
    是否是与父级关联的主滚动视图
    当为 true 时，即使 SingleChildScrollView 里没有足够的内容也能滑动，
    当 scrollDirection 为 Axis.vertical，且 controller 为 null时，默认为 true
    */
    bool primary,
    
    /*
    设置 SingleChildScrollView 的滚动效果
    
    如果想让 SingleChildScrollView 里没有足够的内容也能滑动，
    则设置为AlwaysScrollableScrollPhysics()
    
    如果想让 SingleChildScrollView 在没有足够的内容的时候不能滑动，
    则设置为 ScrollPhysics()
    */
    this.physics,
    
    /*
    可以控制 SingleChildScrollView 滚动的位置
    当 primary 为 true 时，controller 必须为 null
    
    ScrollController 提供以下的几个功能：
    	1.设置 SingleChildScrollView 滑动的初始位置
    	2.可以控制 SingleChildScrollView 是否存储和恢复滑动的位置
    	3.可以读取、设置当前滑动的位置
    */
    this.controller,
    
    this.child,
    
    /*
    确定处理拖动开始行为的方式。
    
    如果设置为[DragStartBehavior.start]，则在检测到拖动手势时将开始滚动拖动行为
    如果设置为[DragStartBehavior.down]，它将在首次检测到向下事件时开始
    */
    this.dragStartBehavior = DragStartBehavior.down,
  }) 
  ...
}
```

**使用方法**

```d
SingleChildScrollView(
  scrollDirection: Axis.horizontal,
  child: Row(
    children: <Widget>[Text('Hello Flutter ' * 100)],
  )
```

### ListView

ListView 是可以线性排列 子Widget 的可滚动Widget。ListView 可以和数据绑定用来实现瀑布流。

#### 使用默认构造函数

**构造函数及参数说明**

```d
class ListView extends BoxScrollView {
  ListView({
    Key key,
    // 滑动的方向, 默认为 Axis.vertical，垂直方向可滑动
    Axis scrollDirection = Axis.vertical,
    
    /*
    控制 ListView 里列表项的排列顺序，
    是按照插入顺序排，还是按照插入顺序相反的方向排序。
    默认为 false，就是按照插入顺序排序，第一个插入的在头部
    当 reverse 为 true 时，第一个插入的会在底部
    */
    bool reverse = false,
    
    /*
    可以控制 ListView 滚动的位置
    
    ScrollController 提供以下的几个功能：
    	1.设置 ListView 滑动的初始位置
    	2.可以控制 ListView 是否存储和恢复滑动的位置
    	3.可以读取、设置当前滑动的位置
    	
	可以继承 ScrollController 实现自定义的功能
   	当 primary 为 true 时，controller 必须为 null
    */
    ScrollController controller,
    
    /*
    是否是与父级关联的主滚动视图
    当为 true 时，即使 ListView 里没有足够的内容也能滑动
    */
    bool primary,
    
    /*
    ScrollPhysics	设置 ListView 的滚动效果
    值必须为 ScrollPhysics 的子类，比如有如下的值：
    	AlwaysScrollableScrollPhysics():可以让 ListView 里没有足够的内容也能滑动
    	ScrollPhysics():ListView 在没有足够的内容的时候不能滑动
    */
    ScrollPhysics physics,
    
    /*
    是否根据列表项的总长度来设置 ListView的长度，默认值为 false。
    	当 shrinkWrap 为 false 时，ListView 会在滚动方向扩展到可占用的最大空间
    	当 shrinkWrap 为 true 时，ListView 在滚动方向占用的空间就是其列表项的总长度，
    	但是这样会很耗性能，因为当其列表项发生变化时，ListView 的大小会重新计算
    */
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    
    /*
    double
    itemExtent 指的是列表项的大小
    	如果滚动方向是垂直方向，则 itemExtent 代表的是 子Widget 的高度，
    	如果滚动方向为水平方向，则 itemExtent 代表的是 子Widget 的长度
    	如果 itemExtent 不为 null，则会强制所有 子Widget 在滑动方向的大小都为 itemExtent
	指定 itemExtent 会比较高效，
	因为 子Widget 的高度就不需要在去计算，ListView 也可以提前知道列表的长度
    */
    this.itemExtent,
    
    /*
    是否用 AutomaticKeepAlive 来包列表项,默认为 true
    
    在一个 lazy list 里，
    
    如果 子Widget 为了保证自己在滑出可视界面时不被回收，
    就需要把 addAutomaticKeepAlives 设为 true
    
    当 子Widget 不需要让自己保持存活时，
    为了提升性能，请把 addAutomaticKeepAlives 设为 false
    
    如果 子Widget 自己维护其 KeepAlive 状态，那么此参数必须置为false。
    */
    bool addAutomaticKeepAlives = true,
    
    /*
    是否用 RepaintBoundary 来包列表项，默认为 true
    
    当 addRepaintBoundaries 为 true 时，可以避免列表项重绘，提高性能
    
    但是当列表项重绘的开销非常小（如一个颜色块，或者一个较短的文本）时，
    不添加 RepaintBoundary 反而会更高效。
    */
    bool addRepaintBoundaries = true,
    
    /*
    是否用 IndexedSemantics 来包列表项,默认为 true
    使用 IndexedSemantics 是为了正确的用于辅助模式
    */
    bool addSemanticIndexes = true,
    
    /*
    double
    ListView 可见部分的前面和后面的区域可以用来缓存列表项，
    这部分区域的 item 即使不可见，也会加载出来，
    所以当滑动到这个区域的时候，缓存的区域就会变的可见，
    cacheExtent 就表示缓存区域在可见部分的前面和后面有多少像素
    */
    double cacheExtent,
    
    // ListView 的列表项
    List<Widget> children = const <Widget>[],
    
    // 提供语义信息的列表项的数量, 默认为 ListView 的 item 的数量
    int semanticChildCount,
    
    /*
    DragStartBehavior	确定处理拖动开始行为的方式。
    如果设置为[DragStartBehavior.start]，则在检测到拖动手势时将开始滚动拖动行为
    如果设置为[DragStartBehavior.down]，它将在首次检测到向下事件时开始
    */
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  }) 
    ...
}
```

**使用方法**

```d
// 需要给 children 属性赋值，但只适用于那些只有少量 子Widget 的 ListView
// ListView 创建的时候，其 子Widget 也会一起创建
ListView(
  children: <Widget>[
    ListTile(title: Text('Title1')),
    ListTile(title: Text('Title2')),
    ListTile(title: Text('Title3')),
  ],)
```

#### 使用ListView.builder

**构造函数及参数说明**

```d
  ListView.builder({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    this.itemExtent,
    
    /*
    itemBuilder 用于创建实际可见的 子Widget，
    只有索引大于或等于零且小于 itemCount 才会调用 itemBuilder。
    */
    @required IndexedWidgetBuilder itemBuilder,
    
    /*
    	代表 子Widget 的数量，虽然是可选的，但是还是建议赋值，
    	可以让 ListView 预估最大滑动距离，从而提升性能。
    	如果为null，则子节点数由[itemBuilder]返回null的最小索引确定。
    */
    int itemCount,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
    int semanticChildCount,
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  }) 
    ...
```

**使用方法**

```d
// 可用于和数据绑定实现大量或无限的列表
void main() => runApp(ListViewBuilderWidget(
      items: List<String>.generate(10000, (i) => "Item $i"),
    ));

class ListViewBuilderWidget extends StatelessWidget {
  final List<String> items;

  ListViewBuilderWidget({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Test',
      home: Scaffold(
        appBar: AppBar(title: new Text('Flutter 可滚动Widget -- ListView')),
        body: ListView.builder(
          itemCount: items.length,
          itemBuilder: (context, index) {
            return ListTile(
              title: Text('${items[index]}'),
            );
          },
        ),
      ),
    );
  }
}
```

要实现一个无限循环列表，只要不给 itemCount 赋值就行，如下：

```d
ListView.builder(
    padding: EdgeInsets.all(8.0),
    itemBuilder: (BuildContext context, int index) {
        return ListTile(title: Text('Title $index'),);
    },
)
```

#### 使用 ListView.separated

相比 ListView.builder 多了一个 separatorBuilder，separatorBuilder就是用于构建分割项的，而且 itemBuilder、separatorBuilder、itemCount 都是必选的。

**构造函数及参数说明**

```d
ListView.separated({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required IndexedWidgetBuilder itemBuilder,
    @required IndexedWidgetBuilder separatorBuilder,
    @required int itemCount,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
  })
  ...
```

**使用方法**

```d
ListView.separated(
  itemCount: items.length,
  itemBuilder: (context, index) {
    return ListTile(
      title: Text('${items[index]}'),
    );
  },
  separatorBuilder: (context, index) {
    return Container(
      constraints: BoxConstraints.tightFor(height: 10),
      color: Colors.orange,
    );
  },
)
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a003177d842417?imageslim)

#### 使用 ListView.custom，需要使用 SliverChildDelegate

**构造函数及参数说明**

```d
  const ListView.custom({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    this.itemExtent,
    
    /*
    childrenDelegate 为必选参数，在看如何实现 SliverChildDelegate，
    发现 SliverChildDelegate 是一个抽象类，
    SliverChildDelegate 的 build 方法可以对单个 子Widget 进行自定义处理，
    而且 SliverChildDelegate 有个默认实现 SliverChildListDelegate，
    所以我们用 SliverChildListDelegate 来实现 ListView.custom
    */
    @required this.childrenDelegate,
    double cacheExtent,
    int semanticChildCount,
  })
```

**使用方法**

```d
ListView.custom(
  childrenDelegate: SliverChildListDelegate(<Widget>[
    ListTile(title: Text('Title1')),
    ListTile(title: Text('Title2')),
    ListTile(title: Text('Title3')),
    ListTile(title: Text('Title4')),
    ListTile(title: Text('Title5')),
  ]),
),
```

### CustomScrollView

CustomScrollView 里面可以添加多个 Widget，而且可以为 Widget 提供复杂的滑动效果，需要为其 slivers 参数赋值，而且 slivers 参数只能接受特定的 Widget

**构造函数及参数说明**

```d
class CustomScrollView extends ScrollView {
  const CustomScrollView({
    Key key,
    Axis scrollDirection = Axis.vertical,
    
    /*
    控制 CustomScrollView 里列表项的排列顺序，
    是按照插入顺序排，还是按照插入顺序相反的方向排序。
    默认为 false，就是按照插入顺序排序，第一个插入的在头部
    当 reverse 为 true 时，第一个插入的会在底部
    */
    bool reverse = false,
    
    /*
    可以控制 CustomScrollView 滚动的位置
    ScrollController 提供以下的几个功能：
    1.设置 CustomScrollView 滑动的初始位置
    2.可以控制 CustomScrollView 是否存储和恢复滑动的位置
    3.可以读取、设置当前滑动的位置
    
    可以继承 ScrollController 实现自定义的功能
    当 primary 为 true 时，controller 必须为 null
    */
    ScrollController controller,
    
    /*
    是否是与父级关联的主滚动视图
    当为 true 时，即使 CustomScrollView 里没有足够的内容也能滑动
    */
    bool primary,
    
    /*
    设置 CustomScrollView 的滚动效果
    值必须为 ScrollPhysics 的子类，比如有如下的值：
    
    AlwaysScrollableScrollPhysics():
    可以让 CustomScrollView 里没有足够的内容也能滑动
    
    ScrollPhysics():CustomScrollView 
    在没有足够的内容的时候不能滑动
    */
    ScrollPhysics physics,
    
    /*
    是否根据列表项的总长度来设置 CustomScrollView 的长度，默认值为 false。
    
    当 shrinkWrap 为 false 时，
    CustomScrollView 会在滚动方向扩展到可占用的最大空间
    
    当 shrinkWrap 为 true 时，
    CustomScrollView 在滚动方向占用的空间就是其列表项的总长度，
    但是这样会很耗性能，因为当其列表项发生变化时，
    CustomScrollView 的大小会重新计算
    */
    bool shrinkWrap = false,
    
    /*
    放在 CustomScrollView 中间的 子Widget 的 key
    */
    Key center,
    
    /*
    CustomScrollView 开始滑动的偏移量
    如果 anchor 为 0.0，则 CustomScrollView 的 子Widget 从头开始排列
    如果 anchor 为 0.5，则 CustomScrollView 的 子Widget 从中间开始排列
    如果 anchor 为 1.0，则 CustomScrollView 的 子Widget 从底部开始排列
    */
    double anchor = 0.0,
    
    /*
    CustomScrollView 可见部分的前面和后面的区域可以用来缓存列表项，
    这部分区域的 item 即使不可见，也会加载出来，
    所以当滑动到这个区域的时候，缓存的区域就会变的可见，
    cacheExtent 就表示缓存区域在可见部分的前面和后面有多少像素
    */
    double cacheExtent,
    
    /*
    List<Widget>	CustomScrollView 的列表项
    */
    this.slivers = const <Widget>[],
    
    /*
    提供语义信息的列表项的数量
默认为 CustomScrollView 的 item 的数量
    */
    int semanticChildCount,
    
    /*
    DragStartBehavior	确定处理拖动开始行为的方式。
    如果设置为[DragStartBehavior.start]，则在检测到拖动手势时将开始滚动拖动行为
    如果设置为[DragStartBehavior.down]，它将在首次检测到向下事件时开始
    */
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  })
  ...
}
```

CustomScrollView 的 slivers 属性的值，只能是以 Sliver 开头的一系列 Widget：

- SliverList
- SliverFixedExtentList
- SliverGrid
- SliverPadding
- SliverAppBar

**使用方法**

```d
CustomScrollView(
  slivers: <Widget>[
    const SliverAppBar(
      pinned: true,
      expandedHeight: 250.0,
      flexibleSpace: FlexibleSpaceBar(
        title: Text('Demo'),
      ),
    ),
    SliverGrid(
      gridDelegate: SliverGridDelegateWithMaxCrossAxisExtent(
        maxCrossAxisExtent: 200.0,
        mainAxisSpacing: 10.0,
        crossAxisSpacing: 10.0,
        childAspectRatio: 4.0,
      ),
      delegate: SliverChildBuilderDelegate(
        (BuildContext context, int index) {
          return Container(
            alignment: Alignment.center,
            color: Colors.teal[100 * (index % 9)],
            child: Text('grid item $index'),
          );
        },
        childCount: 20,
      ),
    ),
    SliverFixedExtentList(
      itemExtent: 50.0,
      delegate: SliverChildBuilderDelegate(
        (BuildContext context, int index) {
          return Container(
            alignment: Alignment.center,
            color: Colors.lightBlue[100 * (index % 9)],
            child: Text('list item $index'),
          );
        },
      ),
    ),
  ],
)
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a0032d73000327?imageslim)

### GridView

GridView 是一个可以构建二维网格列表的 可滚动Widget。

**GridView 的构造函数及参数说明**

GridView 的构造函数，会发现 GridView 的大部分属性都和 ListView 一样：

```d
class GridView extends BoxScrollView {
  GridView({
    Key key,
    Axis scrollDirection = Axis.vertical,
    
    /*
    控制 GridView 里列表项的排列顺序，
    是按照插入顺序排，还是按照插入顺序相反的方向排序。
    默认为 false，就是按照插入顺序排序，第一个插入的在头部
    当 reverse 为 true 时，第一个插入的会在底部
    */
    bool reverse = false,
    
    /*
    可以控制 GridView 滚动的位置
    ScrollController 提供以下的几个功能：
    1.设置 GridView 滑动的初始位置
    2.可以控制 GridView 是否存储和恢复滑动的位置
    3.可以读取、设置当前滑动的位置
    
    可以继承 ScrollController 实现自定义的功能
    当 primary 为 true 时，controller 必须为 null
    */
    ScrollController controller,
    
    /*
    是否是与父级关联的主滚动视图
    当为 true 时，即使 GridView 里没有足够的内容也能滑动
    */
    bool primary,
    
    /*
    设置 GridView 的滚动效果
    值必须为 ScrollPhysics 的子类，比如有如下的值：
    
    AlwaysScrollableScrollPhysics()
    可以让 GridView 里没有足够的内容也能滑动
    
    ScrollPhysics()
    GridView 在没有足够的内容的时候不能滑动
    */
    ScrollPhysics physics,
    
    /*
    是否根据列表项的总长度来设置 GridView 的长度，默认值为 false。
    
    当 shrinkWrap 为 false 时，
    GridView 会在滚动方向扩展到可占用的最大空间
    
    当 shrinkWrap 为 true 时，
    GridView 在滚动方向占用的空间就是其列表项的总长度，
    但是这样会很耗性能，因为当其列表项发生变化时，
    GridView 的大小会重新计算
    */
    bool shrinkWrap = false,
    
    // GridView 的内边距
    EdgeInsetsGeometry padding,
    
    /*
    SliverGridDelegate	控制 GridView 中 子Widget 布局的委托。
    
    SliverGridDelegate 的实现有两个：
    SliverGridDelegateWithMaxCrossAxisExtent：
    横轴 子Widget 为固定长度的布局算法
    SliverGridDelegateWithFixedCrossAxisCount：
    横轴 子Widget 为固定数量的布局算法
    */
    @required this.gridDelegate,
    
    /*
    是否用 AutomaticKeepAlive 来包列表项,默认为 true
    
    在一个 lazy list 里，
    
    如果 子Widget 为了保证自己在滑出可视界面时不被回收，
    就需要把 addAutomaticKeepAlives 设为 true
    
    当 子Widget 不需要让自己保持存活时，
    为了提升性能，请把 addAutomaticKeepAlives 设为 false
    
    如果 子Widget 自己维护其 KeepAlive 状态，那么此参数必须置为false。
    */
    bool addAutomaticKeepAlives = true,
    
    /*
    是否用 RepaintBoundary 来包列表项，默认为 true
    当 addRepaintBoundaries 为 true 时，可以避免列表项重绘，提高性能
    但是当列表项重绘的开销非常小（如一个颜色块，或者一个较短的文本）时，
    不添加 RepaintBoundary 反而会更高效。
    */
    bool addRepaintBoundaries = true,
    
    /*
    是否用 IndexedSemantics 来包列表项,默认为 true
    使用 IndexedSemantics 是为了正确的用于辅助模式
    */
    bool addSemanticIndexes = true,
    
    /*
    GridView 可见部分的前面和后面的区域可以用来缓存列表项，
    这部分区域的 item 即使不可见，也会加载出来，
    所以当滑动到这个区域的时候，缓存的区域就会变的可见，
    cacheExtent 就表示缓存区域在可见部分的前面和后面有多少像素
    */
    double cacheExtent,
    // GridView 的列表项
    List<Widget> children = const <Widget>[],
    // 提供语义信息的列表项的数量 默认为 GridView 的 item 的数量
    int semanticChildCount,
  })
  ...
}
```

#### 使用默认的构造函数，给 children 属性赋值

```d
GridView(
	gridDelegate:
	    SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 3),
	children: <Widget>[
	  ListTile(title: Text('Title1')),
	  ListTile(title: Text('Title2')),
	  ListTile(title: Text('Title3')),
	  ListTile(title: Text('Title4')),
	  ListTile(title: Text('Title5')),
	],
	)),
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00350cf908d83?imageslim)

#### 使用 GridView.count

相比于默认构造函数，其实是将默认构造函数里的 gridDelegate 属性，拆分成了 crossAxisCount、mainAxisSpacing、crossAxisSpacing 和 childAspectRatio。

```d
  GridView.count({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required int crossAxisCount,
    double mainAxisSpacing = 0.0,
    double crossAxisSpacing = 0.0,
    double childAspectRatio = 1.0,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
    List<Widget> children = const <Widget>[],
    int semanticChildCount,
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  })
```

```d
ridView.count(
    crossAxisCount: 3,
    children: <Widget>[
      ListTile(title: Text('Title1')),
      ListTile(title: Text('Title2')),
      ListTile(title: Text('Title3')),
      ListTile(title: Text('Title4')),
    ],
  )),
```

#### 使用 GridView.extent

类似于 GridView.count, 因为 GridView.count 相当于 GridView+SliverGridDelegateWithFixedCrossAxisCount，
而 GridView.extent 相当于 GridView+SliverGridDelegateWithFixedCrossAxisCount。

```d
  GridView.extent({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required double maxCrossAxisExtent,
    double mainAxisSpacing = 0.0,
    double crossAxisSpacing = 0.0,
    double childAspectRatio = 1.0,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    List<Widget> children = const <Widget>[],
    int semanticChildCount,
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  })
```

```d
GridView.extent(
    maxCrossAxisExtent: 300,
    children: <Widget>[
      ListTile(title: Text('Title1')),
      ListTile(title: Text('Title2')),
      ListTile(title: Text('Title3')),
      ListTile(title: Text('Title4')),
    ],
  )),
```

#### 使用 GridView.builder,可用于和数据绑定实现大量或无限的列表

多了和 ListView.builder 类似的 itemCount 和 itemBuilder 属性，用法也是一样的

```d
  GridView.builder({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required this.gridDelegate,
    @required IndexedWidgetBuilder itemBuilder,
    int itemCount,
    bool addAutomaticKeepAlives = true,
    bool addRepaintBoundaries = true,
    bool addSemanticIndexes = true,
    double cacheExtent,
    int semanticChildCount,
  }) 
```

```d
void main() => runApp(GridViewBuilderWidget(
      items: List<String>.generate(10000, (i) => "Item $i"),
    ));

class GridViewBuilderWidget extends StatelessWidget {
  final List<String> items;

  GridViewBuilderWidget({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Test',
      home: Scaffold(
        appBar: AppBar(title: new Text('Flutter 可滚动Widget -- GridView')),
        body: GridView.builder(
          gridDelegate:
              SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 4),
          itemCount: items.length,
          itemBuilder: (context, index) {
            return ListTile(
              title: Text('${items[index]}'),
            );
          },
        ),
      ),
    );
  }
}
```

#### 使用 GridView.custom

增加了 childrenDelegate 的属性，类型为 SliverChildDelegate，具有定制 子Widget 的能力，和 ListView.custom 里的一样，所以用法也一样

```d
const GridView.custom({
    Key key,
    Axis scrollDirection = Axis.vertical,
    bool reverse = false,
    ScrollController controller,
    bool primary,
    ScrollPhysics physics,
    bool shrinkWrap = false,
    EdgeInsetsGeometry padding,
    @required this.gridDelegate,
    @required this.childrenDelegate,
    double cacheExtent,
    int semanticChildCount,
    DragStartBehavior dragStartBehavior = DragStartBehavior.down,
  }) 
```

```d
void main() => runApp(GridViewCustomWidget(
      items: List<String>.generate(10000, (i) => "Item $i"),
    ));

class GridViewCustomWidget extends StatelessWidget {
  final List<String> items;

  GridViewCustomWidget({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Test',
      home: Scaffold(
        appBar: AppBar(title: new Text('Flutter 可滚动Widget -- GridView')),
        body: GridView.custom(
          gridDelegate:
              SliverGridDelegateWithFixedCrossAxisCount(crossAxisCount: 3),
          childrenDelegate: SliverChildListDelegate(<Widget>[
            ListTile(title: Text('Title1')),
            ListTile(title: Text('Title2')),
            ListTile(title: Text('Title3')),
            ListTile(title: Text('Title4')),
          ]),
        ),
      ),
    );
  }
}
```

### PageView

PageView 是可以一页一页滑动的 可滚动Widet。其 子Widget 会占据当前屏幕的所有可见区域。

**构造函数及参数说明**

```d
class PageView extends StatefulWidget {
  PageView({
    Key key,
    this.scrollDirection = Axis.horizontal,
    
    /*
    控制 PageView 里列表项的排列顺序，
    是按照插入顺序排，还是按照插入顺序相反的方向排序。
    默认为 false，就是按照插入顺序排序，第一个插入的在头部
    当 reverse 为 true 时，第一个插入的会在底部
    */
    this.reverse = false,
    /*
   	PageController 可以控制滑动到哪一页，还有其他功能
    */
    PageController controller,
    /*
    ScrollPhysics	
    设置 PageView 的滚动效果
    应该使用 PageScrollPhysics
    */
    this.physics,
    /*
    默认值为 false
    设置为false以禁用页面捕捉，对自定义滚动行为很有用。
    */
    this.pageSnapping = true,
    /*
    ValueChanged<int>	当 PageView 当前页面切换的时候调用
    */
    this.onPageChanged,
    /*
    列表项
    */
    List<Widget> children = const <Widget>[],
    
    /*
    DragStartBehavior	确定处理拖动开始行为的方式。
    如果设置为[DragStartBehavior.start]，则在检测到拖动手势时将开始滚动拖动行为
    如果设置为[DragStartBehavior.down]，它将在首次检测到向下事件时开始
    */
    this.dragStartBehavior = DragStartBehavior.down,
  }) : 
    ...
}
```

**使用方法**

```
PageView 的使用有三种方式：
- 使用默认的构造函数
- 使用 PageView.builder
- 使用 PageView.custom
```

#### 使用默认的构造函数，给 children 属性赋值

使用默认构造函数写 PageView

只适用于那些只有少量 子Widget 的 PageView

```d
PageView(
    onPageChanged: (index){
      print('current page $index ');
    },
    children: <Widget>[
      ListTile(title: Text('Title0')),
      ListTile(title: Text('Title1')),
      ListTile(title: Text('Title2')),
      ListTile(title: Text('Title3')),
      ListTile(title: Text('Title4')),
    ],
  )),
```

#### 使用 PageView.builder,可用于和数据绑定实现大量或无限的列表

PageView.builder 可以和数据绑定，用于构建大量或无限的列表。而且只会构建那些实际可见的 子Widget。

```d
  PageView.builder({
    Key key,
    this.scrollDirection = Axis.horizontal,
    this.reverse = false,
    PageController controller,
    this.physics,
    this.pageSnapping = true,
    this.onPageChanged,
    @required IndexedWidgetBuilder itemBuilder,
    int itemCount,
    this.dragStartBehavior = DragStartBehavior.down,
  })
```

多了和 ListView.builder 类似的 itemCount 和 itemBuilder 属性，用法也是一样的:

```d
void main() => runApp(PageViewBuilderWidget(
      items: List<String>.generate(10000, (i) => "Item $i"),
    ));

class PageViewBuilderWidget extends StatelessWidget {
  final List<String> items;

  PageViewBuilderWidget({Key key, @required this.items}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Test',
      home: Scaffold(
        appBar: AppBar(title: new Text('Flutter 可滚动Widget -- PageView')),
        body: PageView.builder(
          onPageChanged: (index) {
            print('current page $index ');
          },
          itemCount: items.length,
          itemBuilder: (context, index) {
            return ListTile(
              title: Text('${items[index]}'),
            );
          },
        ),
      ),
    );
  }
}
```

#### 使用 PageView.custom

```d
  PageView.custom({
    Key key,
    this.scrollDirection = Axis.horizontal,
    this.reverse = false,
    PageController controller,
    this.physics,
    this.pageSnapping = true,
    this.onPageChanged,
    @required this.childrenDelegate,
    this.dragStartBehavior = DragStartBehavior.down,
  })
```

增加了 childrenDelegate 的属性，类型为 SliverChildDelegate，具有定制 子Widget 的能力，和 ListView.custom 里的一样，所以用法也一样:

```d
PageView.custom(
    onPageChanged: (index) {
      print('current page $index ');
    },
    childrenDelegate: SliverChildListDelegate(<Widget>[
      ListTile(title: Text('Title0')),
      ListTile(title: Text('Title1')),
      ListTile(title: Text('Title2')),
      ListTile(title: Text('Title3')),
      ListTile(title: Text('Title4')),
    ]),
  )),
```

## 功能类Widget

功能类Widget 指的是 非UI Widget，具有一定功能的 Widget。Flutter 中功能类Widget 有很多，本节主要讲如下的功能类Widget：

- Theme
- MediaQuery
- Navigator
- InheritedWidget

### Theme

Theme Widget 的功能是为 MaterialApp 定义主题数据，如导航栏颜色、标题字体、Icon样式。

**Theme 在 MaterialApp 中的位置**

在看代码的时候，你可能没有看到 Theme 的使用，实际上是在使用 MaterialApp 时，会自动将 Theme Widget 加上去:

![image](https://user-gold-cdn.xitu.io/2019/4/23/16a491ba82426b99?imageslim)

Theme 会嵌套 MaterialApp。

**在 Flutter 中获取 Theme 的实例**

要想在 Flutter 中获取 Theme 的实例，首先 根Widget 必须是 MaterialApp，然后在 MaterialApp 的 子Widget 里运行：

```d
// 返回的类型是 ThemeData 
// Theme 的所有数据都存储在 ThemeData 里
Theme.of(context);
```

**ThemeData**

*ThemeData 的构造函数为：*

```d
factory ThemeData({
    Brightness brightness,
    MaterialColor primarySwatch,
    Color primaryColor,
    Brightness primaryColorBrightness,
    Color primaryColorLight,
    Color primaryColorDark,
    Color accentColor,
    Brightness accentColorBrightness,
    Color canvasColor,
    Color scaffoldBackgroundColor,
    Color bottomAppBarColor,
    Color cardColor,
    Color dividerColor,
    Color highlightColor,
    Color splashColor,
    InteractiveInkFeatureFactory splashFactory,
    Color selectedRowColor,
    Color unselectedWidgetColor,
    Color disabledColor,
    Color buttonColor,
    ButtonThemeData buttonTheme,
    Color secondaryHeaderColor,
    Color textSelectionColor,
    Color cursorColor,
    Color textSelectionHandleColor,
    Color backgroundColor,
    Color dialogBackgroundColor,
    Color indicatorColor,
    Color hintColor,
    Color errorColor,
    Color toggleableActiveColor,
    String fontFamily,
    TextTheme textTheme,
    TextTheme primaryTextTheme,
    TextTheme accentTextTheme,
    InputDecorationTheme inputDecorationTheme,
    IconThemeData iconTheme,
    IconThemeData primaryIconTheme,
    IconThemeData accentIconTheme,
    SliderThemeData sliderTheme,
    TabBarTheme tabBarTheme,
    CardTheme cardTheme,
    ChipThemeData chipTheme,
    TargetPlatform platform,
    MaterialTapTargetSize materialTapTargetSize,
    PageTransitionsTheme pageTransitionsTheme,
    AppBarTheme appBarTheme,
    BottomAppBarTheme bottomAppBarTheme,
    ColorScheme colorScheme,
    DialogTheme dialogTheme,
    Typography typography,
    CupertinoThemeData cupertinoOverrideTheme
  }) {
  ...
 }
```

*ThemeData 的使用*

```d
MaterialApp(
    title: "Flutter Demo",
    theme: ThemeData(
        primaryColor: Colors.blue,
    ),
    ...
}
```

*Theme.of(context) 的使用*

```
Theme.of(context) 会获得 MaterialApp 的主题数据 ThemeData
这些数据都是 final 的，所以是只读的，不能修改。
```

```d
// 读取 MaterialApp 的 primaryColor 用于 Text 的 颜色。
class ThemeFeatureWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.red,
      ),
      home: Scaffold(
          appBar: AppBar(title: Text("Flutter 功能类Widget -- Theme")),
          body: ChildText()),
    );
  }
}

class ChildText extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Text("Hello Flutter",
        style: TextStyle(color: Theme.of(context).primaryColor));
  }
}
```

### MediaQuery

MediaQuery Widget 的功能是查询一些数据，例如整个屏幕的宽度、高度、设备像素比等数据

**MediaQuery 在 MaterialApp 中的位置**

MediaQuery 同 Theme 一样，只看代码，你可能没有看到 MediaQuery 的使用，但实际上在使用 MaterialApp 时，会自动将 MediaQuery Widget 加上去：

![image](https://user-gold-cdn.xitu.io/2019/4/23/16a494b6cfdc3ae3?imageslim)

MediaQuery 会嵌套 MaterialApp。

**在 Flutter 中获取 MediaQuery 的实例**

要想在 Flutter 中获取 MediaQuery 的实例，首先 根Widget 必须是 MaterialApp，然后在 MaterialApp 的 子Widget 里运行：

```d
// 返回的类型是 MediaQueryData。
MediaQuery.of(context);
```

#### MediaQueryData

**构造函数**

```d
class MediaQueryData {
  const MediaQueryData({
  	/*
  		屏幕的逻辑像素大小
  		size.width：屏幕的宽度（逻辑像素）
  		size.height：屏幕的高度（逻辑像素）
  	*/
    this.size = Size.zero,
    // 像素比：每个逻辑像素代表的设备像素数
    this.devicePixelRatio = 1.0,
    // 每个逻辑像素代表的字体像素数
    this.textScaleFactor = 1.0,
    // 平台的亮度模式,有两种模式:light和dark 默认为light
    this.platformBrightness = Brightness.light,
    // 表示系统状态栏或者刘海屏的padding
    this.padding = EdgeInsets.zero,
    // 表示设备键盘的padding
    this.viewInsets = EdgeInsets.zero,
    // 	格式化时间时是否使用24小时格式
    this.alwaysUse24HourFormat = false,
    // 是否使用TalkBack或 VoiceOver等辅助功能服务与应用程序进行交互
    this.accessibleNavigation = false,
    // 设备是否反转平台的颜色。现在只能在 iOS 上使用
    this.invertColors = false,
    // 平台是否要求禁用动画
    this.disableAnimations = false,
    // 平台是否请求使用粗体来绘制文本。
    this.boldText = false,
  });
  ...
}
```

**MediaQuery.of(context) 的使用**

MediaQuery.of(context) 获得的 MediaQueryData 数据里，我们一般关心的是 

- Size
- devicePixelRatio

*Size*

Size 是整个屏幕的宽高数据，不管你在哪里调用 MediaQuery.of(context) ，获得的都是整个屏幕的 Size。

*devicePixelRatio*

```
devicePixelRatio 表示的是 每个逻辑像素代表的设备像素数。
设备像素数 = 逻辑像素数 * devicePixelRatio
```

**获取 子Widget 的宽高**

```
要想获取 子Widget 的宽高，就不能使用 MediaQuery.of(context) 
这个时候要用到 GlobalKey。
为 子Widget 设置 GlobalKey，然后通过 GlobalKey 获取 子Widget 的宽高。
```

```d
void main() => runApp(MediaQueryFeatureWidget());

GlobalKey _globalKey = GlobalKey();

class MediaQueryFeatureWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.blue,
      ),
      home: Scaffold(
          appBar: AppBar(title: Text("Flutter 功能类Widget -- MediaQuery")),
          body: BodyWidget()),
    );
  }
}

class BodyWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: <Widget>[
        Text(
          "Hello Flutter",
          key: _globalKey,
        ),
        RaisedButton(
          child: Text('getSize'),
          onPressed: () {
            //获取屏幕的宽高
            print("Screen width:" +
                MediaQuery.of(context).size.width.toString() +
                " Screen height:" +
                MediaQuery.of(context).size.height.toString());
            //获取子Widget 的宽高
            print("Ttext width:" +
                _globalKey.currentContext.size.width.toString() +
                " Screen height:" +
                _globalKey.currentContext.size.height.toString());
          },
        )
      ],
    );
  }
}
```

### Navigator

Navigator 是用来管理页面显示的 Widget，这些页面以堆栈的数据结构存储，当有新页面显示时，就会把上一个页面压入栈，所以栈底是最初的页面，栈顶是当前的页面。

**Navigator 在 MaterialApp 中的位置**

Navigator 同 Theme 一样，只看代码，你可能没有看到 Navigator 的使用，但实际上在使用 MaterialApp 时，会自动将 Navigator Widget 加上去：

![image](https://user-gold-cdn.xitu.io/2019/4/23/16a4ad20fc2c8e45?imageslim)

Navigator 会嵌套 MaterialApp。

**Routes（路由）**

移动应用将全屏幕显示的叫做 “screens” 或者 “pages”，在 Flutter 里叫做Routes（路由），Navigator 就是用来管理 Routes 显示的 Widget。

#### Navigator 的说明

Navigator提供了操作 Routes 的两种方法： 
Navigator.push 和 Navigator.pop。

**在 Flutter 中获取 Navigator 的实例**

在 Flutter 中除了 MaterialApp 提供的 Navigator，也可以实现自定义的 Navigator,但是大部分情况下还是使用的 MaterialApp 提供的 Navigator。

首先 根Widget 必须是 MaterialApp，然后在 MaterialApp 的 子Widget 里运行：

```d
// 返回的类型是 NavigatorState
// 是 Navigator Widget 的 State 类
Navigator.of(context);
```

**NavigatorState 说明**

以下是 NavigatorState 类的图：

![image](https://user-gold-cdn.xitu.io/2019/4/24/16a4af6aca3d4aa4?imageslim)

以下是 Navigator 类的图：

![image](https://user-gold-cdn.xitu.io/2019/4/24/16a4af6fa3e9a512?imageslim)

可以看到 Navigator 类里的方法和 NavigatorState 类的方法都一样，所以 也可以直接使用 Navigator ，而不是 Navigator.of(context)。

### InheritedWidget

```
InheritedWidget 可以高效的将数据在 Widget树 中向下传递，
通常用来共享数据，Flutter中非常重要的一个功能 Widget。

前面介绍的 Theme 和 MediaQuery 之所以可以在 子Widget 内访问到数据，
就是使用到了 InheritedWidget。
```
```
大家应该都有这样的经历，从 A 页面跳转到 B 页面，
但 B 页面需要用到 A 页面的一些数据，这时候该怎么做呢？

通常做法是，A 页面打开 B 页面的时候，把数据从 A 页面传递给 B 页面，
但这样做，会使 A 页面的数据和 B 页面的数据产生割裂，
如果又要求 B 页面产生的结果又返回给 A 页面呢？
这使得我们要用很大的精力去处理数据传输的问题。

InheritedWidget 的目的就是处理数据问题，
可以将共享的数据在 InheritedWidget 里实现，
这样 A页面和 B 页面都可以直接访问数据并修改，
数据一旦修改，就会触发依赖这个数据的 UI 的刷新。
InheritedWidget 在 Flutter 开发中有很大的作用。
```
