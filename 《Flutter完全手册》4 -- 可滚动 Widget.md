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

**构造函数及参数说明**
**使用方法**

### GridView

**构造函数及参数说明**
**使用方法**

### PageView

**构造函数及参数说明**
**使用方法**
