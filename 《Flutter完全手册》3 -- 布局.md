## Flutter布局

### BoxConstraints

**什么是 BoxConstraints（盒约束）？**

```
BoxConstraints 翻译过来是 盒约束，用于指定 Widget 大小的约束。
Flutter 的 Widget 都是通过 BoxConstraints 来约束大小的。
```

**BoxConstraints 如何来约束Widget的大小？**

![image](https://user-gold-cdn.xitu.io/2019/3/3/169442e4e9aeaab7?imageslim)

```d
以左上角为原点，在 X轴 和 Y轴 上设置最小值和最大值

就对应了 BoxConstraints 的四个属性：
minWidth, maxWidth, minHeight, maxHeight

这四个属性可以确定如下的关系：

0.0 <= minWidth <= Widget宽度的实际大小 <= maxWidth <= double.infinity
0.0 <= minHeight <= Widget高度的实际大小 <= maxHeight <= double.infinity

这样就会形成一个矩形的范围：
不小于黄色的矩形，不大于绿色的矩形，这样就确定了一个 Widget 的大小范围，
```

**Flutter 如何确定 Widget 的大小？**

前面讲的 BoxConstraints，只能确定 Widget 大小的一个范围，并不能知道 Widget 的确切大小，这样是无法绘制的，那么 Flutter 是如何确定 Widget 大小的呢？

![image](https://user-gold-cdn.xitu.io/2019/3/3/1694414ab4da9231?imageslim)

```d
这是一个Widget树的结构，为了确定 Widget 的大小：

父Widget 会将自己的 BoxConstraints 传递给自己的 子Widget，
直到传到树的叶节点（没有子节点了），
因此树的叶节点会拥有所有父节点的 BoxConstraints，
这些 BoxConstraints 会相互影响。

叶节点的Widget 会根据这些 BoxConstraints 
和自己的 BoxConstraints 计算出自己的大小，
并将自己的大小返回给父 Widget。

父Widget 得到 子Widget 的大小后，
根据 子Widget 的大小、前面 父Widget 们传递的 BoxConstraints 
和自己的 BoxConstraints，同样可以计算出自己的大小，
这样依次往上传递至 根Widget。
```
> 所以 Widget 的大小是受其 父Widget 的约束和自己 子Widget 的大小共同影响的

#### BoxConstraints 的种类

- Tightly Constraints（严格约束）
- Loose Constraints（松散约束）
- Bounded Constraints（有界约束）
- Unbounded Constraints（无界约束）
- Infinite Constraints（无限约束）

**Tightly Constraints（严格约束）**

当某一轴上的最小值和最大值相同时，那么这个轴上的值就确定了，就是 Tightly Constraints（严格约束）。

```
当 minWidth = maxWidth 时，Widget 的宽就确定了
当 minHeiget = maxHeight 时，Widget 的高度就确定了
当 minWidth = maxWidth 且 minHeiget = maxHeight，Widget 的宽高就都确定了。
```

使用方法:

```d
body: Container(
    // 添加 Tightly Constraints
    constraints: BoxConstraints.tight(Size(100, 100)), 
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

![image](https://user-gold-cdn.xitu.io/2019/3/4/16949568016d2c49?imageslim)

> 当 Widget 是 Tightly Constraints 时，它的宽或高是固定的。

**Loose Constraints（松散约束）**

当某一轴上的最小值为 0 时，就是 Loose Constraints（松散约束）。

- 最大值是确定的值

Container 的 X 轴最小值是 0，假设最大值是 100

```d
body: Container(
        constraints: BoxConstraints.loose(Size(100, 100)), //添加 Loose Constraints
        color: Colors.red,
        child: Text(
          "HelloWorld",
          style: TextStyle(background: paint),
        ))));
```

![image](https://user-gold-cdn.xitu.io/2019/3/4/16949685b5121654?imageslim)

给 Container 设定了最大值是确定值的松散约束，但是 Container 没有 子Widget

```d
body: Container(
        constraints: BoxConstraints.loose(Size(100, 100)), //添加 Loose Constraints
        color: Colors.red,)));
```

![image](https://user-gold-cdn.xitu.io/2019/3/5/1694975e056b24e0?imageslim)

- 最大值是是 Infinite(无限值)

Container 的 X 轴最小值是 0，假设最大值是 Infinite(无限值)

```d
body: Container(
    constraints: BoxConstraints.tightFor(), //添加 Loose Constraints
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

![image](https://user-gold-cdn.xitu.io/2019/3/4/16949685b5121654?imageslim)

如果 Container 没有 子Widget 呢？

```d
body: Container(
    constraints: BoxConstraints.tightFor(), //添加 Loose Constraints
    color: Colors.red,)));
```

Container 铺满了全屏

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000e9b260a699?imageslim)

> 当 Widget 是 Loose Constraints 时，无论最大值是确定值还是无限制，

> - 如果有 子Widget，那么 Widget 的大小就是 子Widget 的大小，相当于 wrap_content

> - 如果没有 子Widget，那么 Widget 就是最大值，如果这个最大值是 Infinite, 就相当于 match_parent


**Bounded Constraints（有界约束）**

当某一轴上的最大值是确定的值时，就是 Bounded Constraints（有界约束）。

新建一个 Bounded Constraints,maxWidth和maxHeight必须为固定值

```d
//添加 Bounded Constraints
constraints: BoxConstraints(minWidth,maxWidth,minHeight,maxHeight), 
```

- 如果 minWidth 大于子 Widget 的宽

```d
body: Container(
    constraints: BoxConstraints(minWidth: 100,maxWidth: 300,minHeight: 0,maxHeight: 300), //添加 Bounded Constraints
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

Container 的宽度取的是最小值 100，而不是最大值 300

![image](https://user-gold-cdn.xitu.io/2019/3/5/169499ac775e9600?imageslim)

- 如果 Container 没有 子Widget 呢？

```d
body: Container(
    constraints: BoxConstraints(minWidth: 100,maxWidth: 300,minHeight: 0,maxHeight: 300), //添加 Bounded Constraints
    color: Colors.red,)));
```

这时候 Container 显示的是最大值。

![image](https://user-gold-cdn.xitu.io/2019/3/5/169499dad2331e94?imageslim)

> 当 Widget 是 Bounded Constraints 时

> - 如果有 子Widget

> 	子Widget 的大小小于有界约束的最小值，则显示的是有界约束的最小值，而当 子Widget 的大小大于有界约束的最小值，小于有界约束的最大值，则显示的是 子Widget 的大小，否则显示的是有界约束的最大值；

> - 如果没有 子Widget 就显示有界约束的最大值

**Unbounded Constraints（无界约束）**

当某一轴上的最大值是 Infinite(无限值) 时，就是 Unbounded Constraints（无界约束）。

新建一个 Unbounded Constraints,maxWidth 和 maxHeight 必须为 double.infinity

```d
constraints: BoxConstraints(minWidth,maxWidth,minHeight,maxHeight), //添加 Bounded Constraints
```

- 当 子Widget 的宽小于 minWidth

```d
body: Container(
    constraints: BoxConstraints(minWidth: 100,maxWidth: double.infinity,minHeight: 100,maxHeight: double.infinity), //添加 Unbounded Constraints
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

![image](https://user-gold-cdn.xitu.io/2019/3/4/16949568016d2c49?imageslim)

- 当 子Widget 的宽大于 minWidth

```d
body: Container(
    constraints: BoxConstraints(minWidth: 10,maxWidth: double.infinity,minHeight: 100,maxHeight: double.infinity), //添加 Unbounded Constraints
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

![image](https://user-gold-cdn.xitu.io/2019/3/5/16949b19eb4ac152?imageslim)

- 如果 Container 没有 子Widget 呢？

```d
body: Container(
    constraints: BoxConstraints(minWidth: 10,maxWidth: double.infinity,minHeight: 100,maxHeight: double.infinity), //添加 Unbounded Constraints
    color: Colors.red,))));
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000e9b260a699?imageslim)

> 其实 Unbounded Constraints 很像 Loose Constraints。

> - 当 Unbounded Constraints  有 子Widget 时，若 子Widget 的大小小于 	Unbounded Constraints 的最小值时，则 Widget 显示 Unbounded Constraints 的最小值，若 子Widget 的大小大于 Unbounded Constraints 的最小值时，则显示 子Widge t的大小；

> - 若没有 子Widget，就相当于 match_parent

**Infinite Constraints（无限约束）**

当某一轴上的最大值和最小值都是 Infinite(无限值) 时，就是 Infinite Constraints（无限约束）。

```d
body: Container(
    constraints: BoxConstraints.expand(), //添加 Infinite Constraints
    color: Colors.red,
    child: Text(
      "HelloWorld",
      style: TextStyle(background: paint),
    ))));
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a0010b333c574b?imageslim)

### 布局 Widget

**布局分类**

布局Widget 按照可以包含的 子Widget 的数量分为：

- 单子Widget布局
- 多子Widget布局

```d
// 单子Widget布局：就是布局 Widget 只能含有一个 子Widget
Center(
     child: Text(content),
)

// 多子Widget布局：就是布局 Widget 可以含有 多个子Widget
Column(
    children: <Widget>[
        Text(content),
        Text(content),
        ...
    ]
)
```

布局 Widget 还可以按照为子元素排布的方式分为：

- 弹性布局Widget
- 线性布局Widget
- 流式布局Widget
- 层叠布局Widget

#### 弹性布局 Flex

```d
Flex 有主轴和交叉轴，
Flex 里的 子Widget 默认沿主轴排列，
并且 Flex 和 Expanded 配合使用可以实现 
子widge 按照一定比例来分配父容器空间。
所以 Flex 叫做弹性布局。
```

![image](https://user-gold-cdn.xitu.io/2019/3/31/169d172a70fb12c0?imageslim)

Flex 有一个必填参数：direction，用于确定主轴的方向，然后就可以在 children 里写 子Widget。

```d
Flex(
    direction: Axis.horizontal,
    children: <Widget>[
      ...
    ],
)
```

**Flex 的构造函数及参数说明**

```d
class Flex extends MultiChildRenderObjectWidget {
  Flex({
    Key key,
    // Axis	主轴的方向
    @required this.direction,
    // MainAxisAlignment	表示 子Widget 在主轴的对齐方式
    this.mainAxisAlignment = MainAxisAlignment.start,
    // MainAxisSize	表示主轴应该占用多大的空间
    this.mainAxisSize = MainAxisSize.max,
    // CrossAxisAlignment	表示 子Widget 在交叉轴的对齐方式
    this.crossAxisAlignment = CrossAxisAlignment.center,
    // TextDirection	表示 子Widget 在主轴方向上的布局顺序
    this.textDirection,
    // VerticalDirection	表示 子Widget 在交叉轴方向上的布局顺序
    this.verticalDirection = VerticalDirection.down,
    // TextBaseline	排列 子Widget 时使用哪个基线
    this.textBaseline,
    // List< Widget>	Flex布局 里排列的内容
    List<Widget> children = const <Widget>[],
  }) : assert(direction != null),
       assert(mainAxisAlignment != null),
       assert(mainAxisSize != null),
       assert(crossAxisAlignment != null),
       assert(verticalDirection != null),
       assert(crossAxisAlignment != CrossAxisAlignment.baseline || textBaseline != null),
       super(key: key, children: children);
    ...
}
```

*direction : 主轴的方向*

```d
direction 的类型是 Axis：
Axis.horizontal	主轴方向为水平方向，
那么 子Widget 就会沿水平方向排列，交叉轴就是垂直方向。

Axis.vertical	主轴方向为垂直方向，
那么 子Widget 就会沿垂直方向排列，交叉轴就是水平方向。
```

![image](https://user-gold-cdn.xitu.io/2019/3/30/169cde68c2cc5843?imageslim)

*mainAxisAlignment ：子Widget 在主轴的对齐方式*

```d
MainAxisAlignment.start	 沿着主轴的起点对齐
textDirection 必须有值，以确定是从左边开始的还是从右边开始的
MainAxisAlignment.end	沿着主轴的终点对齐
textDirection 必须有值，以确定是在左边结束的还是在右边结束的
MainAxisAlignment.center	在主轴上居中对齐
MainAxisAlignment.spaceBetween	在主轴上，两端对齐，项目之间的间隔都相等。
MainAxisAlignment.spaceAround	在主轴上，将多余的控件均匀分布给 子Widget 之间，而且第一个 子Widget 和 最后一个子Widget 距边框的距离是 两个 子Widget 距离的一半
MainAxisAlignment.spaceEvenly	在主轴上，将多余的控件均匀分布给 子Widget 之间，而且第一个 子Widget 和 最后一个子Widget 距边框的距离和 子Widget 之间的距离一样
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a001d0d922566e?imageslim)

*mainAxisSize : 表示主轴应该占用多大的空间*

```d
MainAxisSize.min	
主轴的大小是能显示完 子Widget 的最小大小，
主轴的大小就是 子Widget 的大小

MainAxisSize.max	
主轴能显示的最大的大小，根据约束来判断
```
紫色代表主轴占用的空间：
![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00183506751fd?imageslim)

*crossAxisAlignment ：表示 子Widget 在交叉轴的对齐方式*

```d
CrossAxisAlignment.start	沿着交叉轴的起点对齐
verticalDirection 必须有值，以确定是从左边开始的还是从右边开始的

CrossAxisAlignment.end	沿着主轴的终点对齐
verticalDirection 必须有值，以确定是在左边结束的还是在右边结束的

CrossAxisAlignment.center	在交叉轴上居中对齐
CrossAxisAlignment.stretch	要求 子Widget 在交叉轴上填满
CrossAxisAlignment.baseline	要求 子Widget 的基线在交叉轴上对齐
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a001e173775b64?imageslim)

*textDirection ：表示 子Widget 在主轴方向上的布局顺序*

```
TextDirection.rtl	表示从右到左
TextDirection.ltr	表示从左到右
```
![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00192951bfaeb?imageslim)

*verticalDirection ：表示 子Widget 在交叉轴方向上的布局顺序*

```
VerticalDirection.up	表示从下到上
VerticalDirection.down	表示从上到下
```
![image](https://user-gold-cdn.xitu.io/2019/4/9/16a001a2fed97946?imageslim)


#### Flexible 与 Expanded

如果当 Flex 里的内容过长，超过主轴的大小

```d
Flex(
    direction: Axis.horizontal,
    mainAxisAlignment: MainAxisAlignment.start,
    children: <Widget>[
      Text('Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!')
    ],
)
```

Flex 的主轴是水平方向，而Text 里的内容太多，超过了屏幕的宽度，就会抛出 layout 错误：

```
A RenderFlex overflowed by 267 pixels on the right.
```

![image](https://user-gold-cdn.xitu.io/2019/5/14/16ab3d210f9fd6a6?imageslim)

为了避免 子Widget 在 Row、Column、Flex 中超界，就可以使用 Flexible 与 Expanded。Flexible 与 Expanded 可以让 Row、Column、Flex 的 子Widget 具有弹性能力。

用 Flexible 或 Expended 来改写：

```d
Flexible(
    child: Text(
        'Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!'),
  )
  
Expended(
    child: Text(
        'Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!Hello Flutter!'),
  )
```
![image](https://user-gold-cdn.xitu.io/2019/5/14/16ab4a507477269b?imageslim)

用 Flexible 与 Expanded 来包 子Widget，当 子Widget 要超过主轴的大小时，会自动换行，但是 Flexible 与 Expanded 也有不同的地方，Expanded 是 Flexible 的子类。

**Flexible和Expanded的构造函数**

```d
class Flexible extends ParentDataWidget<Flex> {
  const Flexible({
    Key key,
    // int	此 Widget 的弹性因子
    this.flex = 1,
    // FlexFit	如何分配 弹性Widget 在可用空间里的大小
    this.fit = FlexFit.loose,
    // Widget	要显示的 Widget
    @required Widget child,
  }) : super(key: key, child: child);
  ...
}
```

```d
class Expanded extends Flexible {
  /// Creates a widget that expands a child of a [Row], [Column], or [Flex]
  /// expand to fill the available space in the main axis.
  const Expanded({
    Key key,
    int flex = 1,
    @required Widget child,
  }) : super(key: key, flex: flex, fit: FlexFit.tight, child: child);
}
```

```
Flexible 和 Expanded 的 fit 参数不同
- Flexible 的 fit 是 FlexFit.loose，
- Expanded 的 fit 参数是 FlexFit.tight。

所以，当还有剩余空间时，
- Expanded 会占满剩余的所有空间，
- Flexible 只会占用自身大小的空间。
```

这里举一个例子，当 Text 的内容不够长时：

```d
 Flexible(
    child: Container(
      color: Colors.yellow,
      child: Text('使用 Flexible 来包裹 子Widget'),
    ),
  ),
  
  Expanded(
    child: Container(
      color: Colors.yellow,
      child: Text('使用 Expanded 来包裹 子Widget'),
    ),
  ),
```

Flexible 会占用自身大小，而 Expanded 会占满全屏幕。

![image](https://user-gold-cdn.xitu.io/2019/5/14/16ab4b04fb7adf76?imageslim)

> 总结: 
> Flexible 与 Expanded 可以让 Row、Column、Flex 的 子Widget 具有弹性能力，
> 当 子Widget 要超过主轴的大小时，会自动换行，
> 当还有剩余空间时，Expanded 会占满剩余的所有空间，
> 而 Flexible 只会占用自身大小的空间。

**Fexible 和 Expanded 的 flex 弹性系数**

Fexible 和 Expanded 还有一个很重要的参数：flex

```d
flex 为弹性系数，其布局过程如下：

如果 flex 为0或null，则 child 是没有弹性的，称为 非弹性子Widget，
非弹性子Widget 的大小就是其本身的大小，不会被扩展去占用多余的空间。

如果 flex 大于0，child 是有弹性的，称为 弹性子Widget，
首先会计算出第一步所有 flex为0或null 的 子Widget 的大小，
然后会会按照 弹性子Widget的flex 占 所有弹性子Widget的flex 总和 的比例
来分割主轴的空闲空间
```

- Flexible 的 flex 的使用

```d
Flex(
    direction: Axis.horizontal,
    mainAxisAlignment: MainAxisAlignment.start,
    children: <Widget>[
      Flexible(
        flex: 1,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.yellow,
        ),
      ),
      Flexible(
        flex: 2,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.green,
        ),
      ),
      Flexible(
        flex: 1,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.blue,
        ),
      ),
    ],
  ),
```

使用 Flexible 包裹三个宽高都为 30 的色块，并设置 flex 为 1、2、1，效果如下：

因为 子Widget 的宽度是固定的，所以 Flexible 只会占用本身的大小。

![image](https://user-gold-cdn.xitu.io/2019/5/14/16ab4c0388e777c8?imageslim)

- Expanded 的 flex 使用

```d
Flex(
    direction: Axis.horizontal,
    mainAxisAlignment: MainAxisAlignment.start,
    children: <Widget>[
      Expanded(
        flex: 1,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.yellow,
        ),
      ),
      Expanded(
        flex: 2,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.green,
        ),
      ),
      Expanded(
        flex: 1,
        child: Container(
          height: 30.0,
          width: 30.0,
          color: Colors.blue,
        ),
      ),
    ],
  ),
```

使用 Expanded 包裹三个宽高都为 30 的色块，并设置 flex 为 1、2、1，

虽然三个色块的宽度是固定的，但是 Expanded 还是按照比例瓜分了剩余的全部空间。

![image](https://user-gold-cdn.xitu.io/2019/5/14/16ab4c396f5d1292?imageslim)


### 线性布局 Row 和 Column

线性布局 可以将 子Widget 在同一个方向(水平和垂直)上排列.

- Row：水平方向的线性布局
- Column：垂直方向的线性布局

Row 和 Column 都继承自弹性布局 Flex，其实就是确定了主轴方向的 Flex，其余的用法和 Flex 一致。

#### Row

Row 的构造函数及参数说明

```d
class Row extends Flex {
  Row({
    Key key,
    // MainAxisAlignment	表示 子Widget 在主轴的对齐方式
    MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
    // MainAxisSize	表示主轴应该占用多大的空间
    MainAxisSize mainAxisSize = MainAxisSize.max,
    // CrossAxisAlignment	表示 子Widget 在交叉轴的对齐方式
    CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
    // TextDirection	表示 子Widget 在主轴方向上的布局顺序
    TextDirection textDirection,
    // VerticalDirection	表示 子Widget 在交叉轴方向上的布局顺序
    VerticalDirection verticalDirection = VerticalDirection.down,
    // TextBaseline	排列 子Widget 时使用哪个基线
    TextBaseline textBaseline,
    // List< Widget>	Flex布局 里排列的内容
    List<Widget> children = const <Widget>[],
  }) : super(
    children: children,
    key: key,
    direction: Axis.horizontal,
    mainAxisAlignment: mainAxisAlignment,
    mainAxisSize: mainAxisSize,
    crossAxisAlignment: crossAxisAlignment,
    textDirection: textDirection,
    verticalDirection: verticalDirection,
    textBaseline: textBaseline,
  );
}
```

使用方法: 

```d
Row(
  children: <Widget>[
    Text("Hello Flutter"),
    Image.asset(
      "images/flutter.png",
      width: 200,
    )
  ],
)
```

#### Column

Column 的构造函数及参数说明

```d
class Column extends Flex {
  Column({
    Key key,
    MainAxisAlignment mainAxisAlignment = MainAxisAlignment.start,
    MainAxisSize mainAxisSize = MainAxisSize.max,
    CrossAxisAlignment crossAxisAlignment = CrossAxisAlignment.center,
    TextDirection textDirection,
    VerticalDirection verticalDirection = VerticalDirection.down,
    TextBaseline textBaseline,
    List<Widget> children = const <Widget>[],
  }) : super(
    children: children,
    key: key,
    direction: Axis.vertical,
    mainAxisAlignment: mainAxisAlignment,
    mainAxisSize: mainAxisSize,
    crossAxisAlignment: crossAxisAlignment,
    textDirection: textDirection,
    verticalDirection: verticalDirection,
    textBaseline: textBaseline,
  );
}
```

使用方法

```d
Column(
  children: <Widget>[
    Text("Hello Flutter"),
    Image.asset(
      "images/flutter.png",
      width: 200,
    )
  ],
)
```

**特殊情况**

```
如果 Row 里面嵌套 Row，或者 Column 里面再嵌套 Column，
那么只有最外面的 Row 或 Column 会占用尽可能大的空间，
里面 Row 或 Column 所占用的空间为实际大小。
```

### 流式布局 Wrap

页面元素的宽度可以按照屏幕分辨率进行适配调整，但整体布局不变。

为什么需要流式布局？

前面讲到的，在 Flex、Row、Column 中，当 子Widget 的大小超过 主轴的大小后，就会报 layout 的 overflowed 错误 ，会在界面上看到黄黑色的条。

```d
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
        title: 'Test',
        home: new Scaffold(
            appBar: new AppBar(title: new Text('Flutter 布局Widget -- 流式布局')),
            body: Wrap(
              children: <Widget>[Text('Hello Flutter ' * 100)],
            )));
  }
}
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00277ff1f9ad0?imageslim)

#### Wrap

Wrap 会把超出屏幕显示范围的 Widget 自动换行，所以称为流式布局。

**Wrap 的构造函数及参数说明**

```d
class Wrap extends MultiChildRenderObjectWidget {
  Wrap({
    Key key,
    // Axis	主轴的方向 默认是 Axis.horizontal
    this.direction = Axis.horizontal,
    // WrapAlignment	子Widget 在主轴上的对齐方式
    // 默认值为WrapAlignment.start
	// WrapAlignment 的值和 MainAxisAlignment 的一样
    this.alignment = WrapAlignment.start,
    this.spacing = 0.0,
    // Wrap 会自动换成或换列，runAlignment 就是每行或每列的对齐方式，
    // 如果主轴为水平方向，就是每行，如果主轴为竖直方向，就是每列，
    // 默认值为WrapAlignment.start
	// WrapAlignment 的值和 MainAxisAlignment 的一样
    this.runAlignment = WrapAlignment.start,
    // 	每行或每列之间的间距 默认是0.0
    this.runSpacing = 0.0,
    // WrapCrossAlignment	子Widget 在交叉轴上的对齐方式，
    // WrapCrossAlignment.start
	// WrapCrossAlignment 的值和 MainAxisAlignment 的一样
    this.crossAxisAlignment = WrapCrossAlignment.start,
    // 表示 子Widget 在主轴方向上的布局顺序
    this.textDirection,
    // 	表示 子Widget 在交叉轴方向上的布局顺序
    this.verticalDirection = VerticalDirection.down,
    // List< Widget>	Wrap布局 里排列的内容
    List<Widget> children = const <Widget>[],
  }) : super(key: key, children: children);
  ...
}
```

使用方式:

```d
// Wrap 需要设置主轴方向，默认的主轴方向为水平方向，
// 给其 children 参数添加 子Widget 即可
class WrapWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
        title: 'Test',
        home: new Scaffold(
            appBar: new AppBar(title: new Text('Flutter 布局Widget -- 流式布局')),
            body: Wrap(
              direction: Axis.horizontal,
              spacing: 8.0, // 主轴 方向间距
              runSpacing: 12.0, // 交叉轴 方向间距
              alignment: WrapAlignment.center,
              runAlignment: WrapAlignment.start,
              children: <Widget>[
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('A')),
                  label: new Text('AAAAAAAA'),
                ),
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('M')),
                  label: new Text('BBBBBB'),
                ),
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('H')),
                  label: new Text('CCCCCCCCC'),
                ),
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('J')),
                  label: new Text('DDDDDDDD'),
                ),
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('J')),
                  label: new Text('EEEEEEEE'),
                ),
                new Chip(
                  avatar: new CircleAvatar(
                      backgroundColor: Colors.blue, child: Text('J')),
                  label: new Text('FFFFFFFFFFFFFFFF'),
                ),
              ],
            )));
  }
}
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a002845670f9aa?imageslim)

### 层叠布局 Stack

Flutter 的层叠布局是 Stack，层叠布局允许 子Widget 堆叠（按照代码中声明的顺序）,同时 子Widget 可以根据到父容器四个边的位置来确定本身的位置。

**Stack 的构造函数及参数说明**

```d
class Stack extends MultiChildRenderObjectWidget {
  Stack({
    Key key,
    /*
    AlignmentDirectional	
    决定如何对齐 non-positioned子Widget 和 部分positioned子Widget，
    默认值为 AlignmentDirectional.topStart
	部分positioned子Widget，在某一个轴上没有定义的，这个轴就使用 alignment 的值，
	比如 left、right 为横轴，left 和 right 都没有定义，
	就是横轴没有定义，只要这两个一个有值，这个轴就算有值；
	top、bottom 为纵轴，同理。
    */
    this.alignment = AlignmentDirectional.topStart,
    
    // 用于确定 alignment 的对齐方向
    this.textDirection,
    
    // StackFit	此参数用于决定 non-positioned子Widget 如何去适应Stack的大小
    this.fit = StackFit.loose,
    
    // Overflow	决定如何显示超出 Stack显示空间的 子widget
    this.overflow = Overflow.clip,
    
    // List< Widget>	Stack布局 里排列的内容
    List<Widget> children = const <Widget>[],
  }) : super(key: key, children: children);
  ...
}
```

*alignment：对齐方式*

```
alignment 的类型是 AlignmentDirectional：
注意这里 start 和 end 指的是 textDirection 给定的方向

AlignmentDirectional.topStart	上边 start 对齐
AlignmentDirectional.topCenter	上边 居中 对齐
AlignmentDirectional.topEnd	上边 end 对齐
AlignmentDirectional.centerStart	中间 start 对齐
AlignmentDirectional.center	中间 对齐
AlignmentDirectional.centerEnd	中间 end 对齐
AlignmentDirectional.bottomStart	下边 start 对齐
AlignmentDirectional.bottomCenter	下边 居中 对齐
AlignmentDirectional.bottomEnd	下边 end 对齐
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00297b8b0b17f?imageslim)

*fit：non-positioned子Widget 如何去适应Stack的大小*

```
fit 的类型是 StackFit：

StackFit.loose	使用 子Widget 自身的大小
StackFit.expand	子Widget 扩伸到Stack的大小
StackFit.passthrough	Stack的父Widget 的约束无修改的传递给 Stack的子Widget
```

*overflow：如何显示超出 Stack显示空间的 子widget*

```
overflow 的类型为 Overflow：

Overflow.visible	超出部分仍能看见
Overflow.clip	超出部分会被剪裁
```

**使用方法**

```d
Stack(
  children: <Widget>[
    Image.asset(
      "images/flutter.png",
       width: 200,
       fit: BoxFit.cover,
    ),
    Text('Hello Flutter',style: TextStyle(fontSize: 50.0),),
  ],
)
```

![image](https://user-gold-cdn.xitu.io/2019/5/15/16ab7df85a034d9c?imageslim)

#### Stack的子Widget

为了确定 子Widget 到父容器四个角的位置,Stack 将 子Widget 分为两类：

```
positioned 子Widget
是指被 Positioned 嵌套起来的 Widget，
Positioned 可以控制 子Widget 到父容器四个边的距离

non-positioned 子Widget
就是不用 Positioned 嵌套起来的 Widget,
non-positioned子Widget 使用 Stack 设置的 alignment 来确定自己在父容器里的位置
```

**Positioned**

Positioned 的构造函数及参数说明

```d
class Positioned extends ParentDataWidget<Stack> {
  const Positioned({
    Key key,
    // 离 Stack 左边的距离
    this.left,
    this.top,
    this.right,
    this.bottom,
    // 指定 Widget 的宽度
    this.width,
    this.height,
    // 	Stack布局 里排列的内容
    @required Widget child,
  }) : assert(left == null || right == null || width == null),
       assert(top == null || bottom == null || height == null),
       super(key: key, child: child);
    ...
}
```

```
注意，此处的 width、height 是用于配合 left、top 、right、 bottom 来定位 Widget 的。
举个例子，在水平方向上，你只能指定 left、right、width 三个属性中的两个，
如指定 left 和 width 后，right 会自动算出 (left+width)，
如果同时指定三个属性则会报错，垂直方向同理。
```

使用方法

```d
class StackWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(title: Text("Flutter布局Widget -- 层叠布局")),
        body: Stack(
          fit: StackFit.expand,
          children: <Widget>[
            Positioned(
              left: 50,
              top: 100,
              child: Image.asset(
                "images/flutter.png",
                width: 200,
                fit: BoxFit.cover,
              ),
            ),
            Text('Hello Flutter'),
          ],
        ),
      ),
    );
  }
}
```

![image](https://user-gold-cdn.xitu.io/2019/4/17/16a2a1ab07cb564b?imageslim)
