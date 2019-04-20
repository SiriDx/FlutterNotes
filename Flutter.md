## Flutter

官方文档: [Documentation](https://docs.flutter.io/index.html)

### 网络请求
### JSON解析
### UI和布局

#### 常用Widget

**文本 Text**

构造

```d
Text(String data, {Key key, TextStyle style, StrutStyle strutStyle, TextAlign textAlign, TextDirection textDirection, Locale locale, bool softWrap, TextOverflow overflow, double textScaleFactor, int maxLines, String semanticsLabel}) → Text
```

属性

```
textAlign: 文本的对齐方式
maxLines: 最多显示的行数
overflow: 文本内容超出显示范围处理方式(截断, 省略号, 渐变)
style: 字体大小, 颜色, 装饰(下划线...)
```

示例

```d
  Text(
    'Hello World',
    textAlign: TextAlign.center,
    maxLines: 1,
    overflow: TextOverflow.clip,
    style: TextStyle(
      fontSize: 25.0,
      color: Color.fromARGB(255, 255, 150, 150),
      decoration: TextDecoration.underline,
      decorationStyle: TextDecorationStyle.solid
    ),
  );
```

**容器 Container**


```d
Container({Key key, AlignmentGeometry alignment, EdgeInsetsGeometry padding, Color color, Decoration decoration, Decoration foregroundDecoration, double width, double height, BoxConstraints constraints, EdgeInsetsGeometry margin, Matrix4 transform, Widget child })
```

属性

![image](https://flutter.dev/assets/ui/layout/margin-padding-border-9616dd0d7af45b95e6fcface25cd933b6b4a0fda51c1ab1bb9287bc8ed92c356.png)

```
alignment: 容器内子部件的对齐方式
padding: 内边距
margin: 外边距
decoration: 背景图, 背景色, 边框...
width:宽
height:高

```

示例

```d
  Container(
    child: Text('Container'),
    alignment: Alignment.topLeft,
    padding: const EdgeInsets.fromLTRB(10.0, 12.0, 9.0, 0),
    margin: EdgeInsets.all(10.0),
    decoration: BoxDecoration(
      gradient: LinearGradient(colors: [Colors.lightBlue, Colors.green, Colors.purple]),
      border: Border.all(width: 2.0, color: Colors.red)
      )
  );
```

**图片 Image**

构造

```
图片的几种构造方式

Image.network: 加载网络资源图片
Image.asset: 根据name参数, 加载assets bundle中的图片, 相对路径
Image.file: 加载本地图片，绝对路径，与包无关
Image.memory: 加载Uint8List资源图片
```

[Image.network](https://docs.flutter.io/flutter/widgets/Image/Image.network.html)
[Image.asset](https://docs.flutter.io/flutter/widgets/Image/Image.asset.html)
[Image.file](https://docs.flutter.io/flutter/widgets/Image/Image.file.html)
[Image.memory](https://docs.flutter.io/flutter/widgets/Image/Image.memory.html)

属性

```
fit: BoxFit 图片的绘制方式
	- BoxFit.contain 图片完整显示在盒子中
	- BoxFit.cover 图片铺满目前盒子
	- BoxFit.fill  
	- BoxFit.fitWidt
	- BoxFit.fitHeight
	- BoxFit.scaleDown

repeat: ImageRepeat 如何绘制盒子中未被图片覆盖的区域
	- Image.noRepeat 不重复
	- ImageRepeat.repeat 横向和纵向都进行重复，直到铺满整个画布
	- ImageRepeat.repeatX 横向重复，纵向不重复
	- ImageRepeat.repeat 纵向重复，横向不重复
```

[BoxFit](https://docs.flutter.io/flutter/painting/BoxFit-class.html)
[ImageRepeat](https://docs.flutter.io/flutter/painting/ImageRepeat-class.html)


示例

```d
  Image.network(
    'https://www.baidu.com/img/bd_logo1.png', 
    scale: 1.0,
    fit: BoxFit.cover,
    repeat: ImageRepeat.repeat,
  );
```

**列表 ListView**


构造

```d
ListView({
	Key key,
	Axis scrollDirection: Axis.vertical,
	bool reverse: false,
	ScrollController controller,
	bool primary,
	ScrollPhysics physics,
	bool shrinkWrap: false,
	EdgeInsetsGeometry padding,
	double itemExtent,
	bool addAutomaticKeepAlives: true,
	bool addRepaintBoundaries: true,
	bool addSemanticIndexes: true,
	double cacheExtent,
	List<Widget> children: const [],
	int semanticChildCount,
	DragStartBehavior dragStartBehavior: DragStartBehavior.down
})

// 动态列表
ListView.builder({
	Key key,
	@required IndexedWidgetBuilder itemBuilder,
	int itemCount,
	...
})
```

属性

```
children: widget数组(ListTile, Image...等)
scrollDirection: 滚动方向(Axis.horizontal)
itemCount: 列表元素个数
itemBuilder: IndexedWidgetBuilder, 传入context和index, 返回一个widget, 作为列表的元素
```

示例

```d
  ListView(
    children: <Widget>[
      ListTile(
        leading: Icon(Icons.access_time),
        title: Text('time'),
      ),
      ListTile(
        leading: Icon(Icons.account_balance),
        title: Text('balance'),
      ),
      scrollDirection: Axis.horizontal
    ],
  );
  
// ListView.builder()
  var items = List<String>.generate(1000, (i)=> "Item $i");
  
  ListView.builder(
    itemCount: items.length,
    itemBuilder: (context, index) {
      return ListTile(title: Text('${items[index]}'));
    },
  );
  
```


### 状态管理
### 第三方库
