## 常用Widget

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

### 文本框

#### Text

```d
class Text extends StatelessWidget {

  const Text(this.data, {
    Key key,
    // TextStyle	文本样式
    this.style,
    // StrutStyle	设置每行的最小行高
    this.strutStyle,
    //	 TextAlign	文本的对齐方式
    this.textAlign,
    // TextDirection	文字方向
    this.textDirection,
    // Locale	用于选择用户语言和格式设置首选项的标识符
    this.locale,
    // bool	是否支持软换行符
	// 如果是 false 的话，这个文本只有一行，水平方向是无限的
    this.softWrap,
   	// TextOverflow	文本的截断方式
    this.overflow,
    // double	代表文本相对于当前字体大小的缩放因子(默认值为1.0)
    this.textScaleFactor,
    // int	显示的最大行数
    this.maxLines,
    // String	给文本加上一个语义标签 没有实际用处
    this.semanticsLabel,
  }) : assert(data != null),
       textSpan = null,
       super(key: key);
    
    ...
}
```

从构造函数里也可以看到只有 data 是必选参数，其余都是可选参数。


**TextStyle**

```
用来定义 Text 显示的样式，如 颜色、大小、背景灯
```

TextStyle 的构造参数

```d
class TextStyle extends Diagnosticable {

  const TextStyle({
  	// 是否继承父 Text 的样式，默认为 true
	// 如果为false，且样式没有设置具体的值，
	// 则采用默认值：白色、字体大小 10px、sans-serif 字体
    this.inherit = true,
    // 文字的颜色
    this.color,
    // 	文字的大小
    this.fontSize,
    // 字体粗细
    this.fontWeight,
    // 是否在字体中倾斜字形
    this.fontStyle,
    // 字母之间的间隔
    this.letterSpacing,
    // 单词之间的间隔
    this.wordSpacing,
    // 用于对齐文本的水平线
    this.textBaseline,
    // 文本的高度
    // 但它并不是一个绝对值，而是一个因子，
    // 具体的行高等于fontSize*height。
    this.height,
    // 用于选择用户语言和格式设置首选项的标识符
    this.locale,
    // 文本的前景色
    this.foreground,
    // 文本的背景色
    this.background,
    // 在文本下方绘制阴影
    this.shadows,
    // 文本的线条
    this.decoration,
    // TextDecoration 线条的颜色
    this.decorationColor,
    // TextDecoration 线条的样式
    this.decorationStyle,
    // 文本样式的描述, 无实际用处
    this.debugLabel,
    // 用于设置使用哪种自定义字体
    String fontFamily,
    // 字体列表，当前面的字体找不到时，会在这个列表里依次查找
    List<String> fontFamilyFallback,
    // 用于设置使用哪种自定义字体
    String package,
  }) : fontFamily = package == null ? fontFamily : 'packages/$package/$fontFamily',
       _fontFamilyFallback = fontFamilyFallback,
       _package = package,
       assert(inherit != null),
       assert(color == null || foreground == null, _kColorForegroundWarning);
       
    ...
}
```

TextDecoration：文本的线条

```
文本的线条，包含四种：

TextDecoration.underline：下划线
TextDecoration.overline：上划线
TextDecoration.lineThrough：中划线
TextDecoration.none：不划线
```

TextDecorationStyle: 文本线条的种类

```
1. TextDecorationStyle.solid：实线
2. TextDecorationStyle.double：两条线
3. TextDecorationStyle.dotted：点虚线
4. TextDecorationStyle.dashed：间隔虚线（比点要长）
5. TextDecorationStyle.wave：波浪线
```

**Text 的命名构造函数：Text.rich()**

Text 除了默认的构造函数外，还有命名构造函数：Text.rich()

```d
/// Creates a text widget with a [TextSpan].
  const Text.rich(this.textSpan, {
    Key key,
    this.style,
    this.strutStyle,
    this.textAlign,
    this.textDirection,
    this.locale,
    this.softWrap,
    this.overflow,
    this.textScaleFactor,
    this.maxLines,
    this.semanticsLabel,
  }) 
```

Text.rich() 需要传入 TextSpan，可以显示多种样式的 text，具体使用可以查看下面介绍的 RichText，使用方法是一样的。

#### RichText

```d
一个富文本 Text，可以显示多种样式的 text。
RichText 要传入 TextSpan 数组，每个 TextSpan 是一个独立的文本，可以定义自己的 Style

RichText(
    text: TextSpan(children: [
      TextSpan(text: "Hello", style: TextStyle(color: Colors.blue)),
      TextSpan(text: "Flutter", style: TextStyle(color: Colors.red))
    ]),
  )
```

**RichText 的必选参数：TextSpan**

```
如果我们需要对一个 Text 内容的不同部分按照不同的样式显示，这时就可以使用 TextSpan，它代表文本的一个片段，然后把不同的 TextSpan 组合起来。
```

**RichText 的构造函数**

```d
class RichText extends LeafRenderObjectWidget {

  const RichText({
    Key key,
    // TextSpan	文字片段
    @required this.text,
    this.textAlign = TextAlign.start,
    this.textDirection,
    this.softWrap = true,
    this.overflow = TextOverflow.clip,
    this.textScaleFactor = 1.0,
    this.maxLines,
    this.locale,
    this.strutStyle,
  }) : assert(text != null),
  ...
}
```

**TextSpan 的构造函数及参数说明**

```d
class TextSpan extends DiagnosticableTree {
  const TextSpan({
  	 // TextStyle	文本样式
    this.style,
    // String	要显示的文字
    this.text,
    // List< TextSpan>	子 TextSpan
    this.children,
    // GestureRecognizer 一个手势识别器，它将接收到达此文本范围的事件。
    this.recognizer,
  });
  ...
}
```

#### 自定义字体

在 Flutter中 使用字体分两步完成：

- 首先在 pubspec.yaml 中声明它们，以确保它们会打包到应用程序中。

```
要将字体打文件打包到应用中，和使用其它资源一样，要先在 pubspec.yaml 中声明它。
然后将字体文件复制到在 pubspec.yaml 中指定的位置。如：

flutter:
  fonts:
    - family: Raleway
      fonts:
        - asset: assets/fonts/Raleway-Regular.ttf
        - asset: assets/fonts/Raleway-Medium.ttf
          weight: 500
        - asset: assets/fonts/Raleway-SemiBold.ttf
          weight: 600
    - family: AbrilFatface
      fonts:
        - asset: assets/fonts/abrilfatface/AbrilFatface-Regular.ttf
```

- 然后通过 TextStyle 属性使用字体。

```
// 声明文本样式
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
);

// 使用文本样式
var buttonText = const Text(
  "Use the font for this text",
  style: textStyle,
);
```

**Package 中的字体**

要使用 Package 中定义的字体，必须提供 package 参数。

```d
// 例如，假设上面的字体声明位于 my_package 包中。
// 然后创建 TextStyle 的过程如下：
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
  package: 'my_package', //指定包名
);

//如果在 package 包内部使用它自己定义的字体，
// 也应该在创建文本样式时指定 package 参数
```

```
一个包也可以只提供字体文件而不需要在 pubspec.yaml 中声明。 
这些文件应该存放在包的 lib/ 文件夹中。
字体文件不会自动绑定到应用程序中，应用程序可以在声明字体时有选择地使用这些字体。
假设一个名为 my_package 的包中有一个字体文件：

lib/fonts/Raleway-Medium.ttf
```

然后，应用程序可以声明一个字体，如下面的示例所示：

```
 flutter:
   fonts:
     - family: Raleway
       fonts:
         - asset: assets/fonts/Raleway-Regular.ttf
         - asset: packages/my_package/fonts/Raleway-Medium.ttf
           weight: 500
```

lib/ 是隐含的，所以它不应该包含在 asset 路径中。

在这种情况下，由于应用程序本地定义了字体，所以在创建 TextStyle 时可以不指定 package 参数：

```
const textStyle = const TextStyle(
  fontFamily: 'Raleway',
);
```

### 图片和Icon

#### Image

**将本地图片资源添加到 Flutter APP**

- 在 Flutter 工程的根目录下创建一个 images 目录（名字随便取），然后将一张 flutter.png 的图片拷贝到该目录

- 打开 pubspec.yml ,在 flutter 中添加图片的配置信息

```d
// 如果只添加一张图片
flutter:
  uses-material-design: true
    
  assets:
    - images/flutter.png
```

```d
// 或者可以把整个目录配置上，就不用一张张图片添加了
flutter:
  uses-material-design: true
    
  assets:
    - images/
```

- 使用的时候，需要传入图片的路径

```d
Image.asset("images/flutter.png")
```

**Image 的构造函数及参数说明**

```d
// Image 总共有 5 个构造函数
// 除了默认的构造函数，还有四种命名构造函数：
class Image extends StatefulWidget {

    const Image({
        @required this.image,
        ...
    })
    
    // 从网络加载显示图片
    Image.network(String src,{
        ...
    })
    
    // 从本地文件加载显示图片
    Image.file(File file, {
        ...
    })
    
    // 从Flutter APP的资源文件里加载显示图片
    Image.asset(String name, {
        ...
    })
    
    // 从内存加载显示图片
    Image.memory(Uint8List bytes, {
        ...
    })

}
```

Image 的五个构造函数的参数都有所不同，这里讲一下共同都有的参数：

```d
scale	double	图形显示的比例

width	double	图片的宽
height	double	图片的高
如果为null的话，则图像将选择最佳大小显示，而且会保留其固有宽高比的大小

color	Color	图片的混合色值
colorBlendMode	BlendMode	图片与颜色的混合模式

fit	BoxFit	用于在图片的显示空间和图片本身大小不同时指定图片的适应模式
alignment	Alignment	图片的对齐方式
repeat	ImageRepeat	当图片本身大小小于显示空间时，指定图片的重复规则
centerSlice	Rect	在这个矩形范围内的图片会被当成.9的图片

matchTextDirection	bool	图片的绘制方向
true:从左往右
false:从右往左

gaplessPlayback	bool	当图像提供者更改时
true：继续显示旧图像
false：简单地显示任何内容

filterQuality	FilterQuality	设置图片的过滤质量
```

**BoxFit**

用于在图片的显示空间和图片本身大小不同时指定图片的适应模式

```d
BoxFit的值	含义
BoxFit.fill	会拉伸填充满显示空间，图片本身长宽比会发生变化，图片会变形。
BoxFit.contain	会按图片的长宽比放大后居中填满显示空间，图片不会变形，超出显示空间部分会被剪裁。
BoxFit.cover	这是图片的默认适应规则，图片会在保证图片本身长宽比不变的情况下缩放以适应当前显示空间，图片不会变形。
BoxFit.fitWidth	图片的宽度会缩放到显示空间的宽度，高度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
BoxFit.fitHeight	图片的高度会缩放到显示空间的高度，宽度会按比例缩放，然后居中显示，图片不会变形，超出显示空间部分会被剪裁。
BoxFit.scaleDown	对齐目标框内的源（默认情况下，居中），并在必要时缩小源以确保源适合框内。这与contains相同，如果这会缩小图像，否则它与none相同。
BoxFit.none	图片没有适应策略，会在显示空间内显示图片，如果图片比显示空间大，则显示空间只会显示图片中间部分
```

**BoxFit**

ImageRepeat,当图片本身大小小于显示空间时，指定图片的重复规则

```d
ImageRepeat的值	含义
ImageRepeat.repeat	在x和y轴上重复图像，直到填充满空间。
ImageRepeat.repeatX	在x轴上重复图像，直到填充满空间。
ImageRepeat.repeatY	在y轴上重复图像，直到填充满空间。
ImageRepeat.noRepeat	不重复
```

**FilterQuality**

设置图片的过滤质量

```
FilterQuality.none	最快的过滤。
FilterQuality.low	比none的过滤质量好，但是比none的时间要长。
FilterQuality.medium	比low的过滤质量好，但是也比low的时间要长
FilterQuality.high	过滤质量最高，但也最慢
```

#### Icon

```d
Flutter 中，可以像 web 开发一样使用 iconfont，iconfont 即“字体图标”，
它是将图标做成字体文件，然后通过指定不同的字符而显示不同的图片。
用于显示 iconfont 的就是 Icon Widget。
```

iconfont 和图片相比有如下优势：

```
1. 体积小：可以减小安装包大小。
2. 矢量的：iconfont 都是矢量图标，放大不会影响其清晰度。
3. 可以应用文本样式：可以像文本一样改变字体图标的颜色、大小对齐等。
4. 可以通过 TextSpan 和文本混用。
```

**Icon 的构造函数及参数说明**

```d
class Icon extends StatelessWidget {

  const Icon(this.icon, {
    Key key,
    // double	icon的大小	可选
    this.size,
    // Color	icon的颜色
    this.color,
    this.semanticLabel,
    this.textDirection,
  }) : super(key: key);
  
  ...
}
```

### 输入框和表单

#### TextField

TextField 是文本输入框

**TextField 的构造函数及参数说明**

```d
class TextField extends StatefulWidget {
 
  const TextField({
    Key key,
    // TextEditingController	控制 TextField 的编辑，如果没有设置，会有默认值
    this.controller,
    // FocusNode	用于控制TextField是否占有当前键盘的输入焦点
	// 它是我们和键盘交互的一个handle
    this.focusNode,
    // InputDecoration	用于控制TextField的外观显示，如提示文本、背景颜色、边框等
    this.decoration = const InputDecoration(),
    // TextInputType 用于设置该输入框默认的键盘输入类型
    TextInputType keyboardType,
    // TextInputAction	键盘动作按钮图标(即回车键位图标)
    this.textInputAction,
    // TextCapitalization	定义文本的大写格式
    this.textCapitalization = TextCapitalization.none,
    // TextStyle	文本样式
    this.style,
    this.textAlign = TextAlign.start,
    this.textDirection,
    // bool	是否自动获取焦点, 默认为false
    this.autofocus = false,
    // bool	是否隐藏正在编辑的文本，如用于输入密码的场景等，
    // 文本内容会用“•”替换 默认为false
    this.obscureText = false,
    this.autocorrect = true,
    // int	显示的最大行数
    this.maxLines = 1,
    // int	输入框中允许的最大字符数
    this.maxLength,
    // bool	是否强制限制最大字符数，默认为true
	// true：强制限制最大字符数
	// false：不限制最大字符数，即使设置了maxLength也不生效
    this.maxLengthEnforced = true,
    // ValueChanged	输入框内容改变时的回调函数；
    // 注：内容改变事件也可以通过controller来监听
    this.onChanged,
    // VoidCallback	输入框输入完成时触发，但是onEditingComplete没有参数，不会返回内容
    this.onEditingComplete,
    // ValueChanged	输入框输入完成时触发，但是onSubmitted有参数，会返回内容
    this.onSubmitted,
    // List< TextInputFormatter>	用于指定输入格式；
    // 当用户输入内容改变时，会根据指定的格式来校验。
    this.inputFormatters,
    // bool	输入框是否禁用
// 如果为false，则输入框会被禁用，禁用状态不接收输入和事件，同时显示禁用态样式（在其decoration中定义）。
    this.enabled,
    // double	自定义输入框光标宽度
    this.cursorWidth = 2.0,
    // Radius	自定义输入框光标圆角
    this.cursorRadius,
    // Color	自定义输入框光标颜色
    this.cursorColor,
    // Brightness	设置键盘的亮度模式
	// 只能在iOS上使用，有两种：Brightness.dart和Brightness.light
    this.keyboardAppearance,
    //	EdgeInsets	文本框滑动时的间距
    this.scrollPadding = const EdgeInsets.all(20.0),
    // DragStartBehavior	设置确定当用户启动拖动时拖动正式开始的时间
    this.dragStartBehavior = DragStartBehavior.down,
    // bool	是否启用交互式选择
	// true：长按将会选中文字，并且弹出 cut/copy/paste 的菜单
    this.enableInteractiveSelection,
    // GestureTapCallback	TextField的点击事件
    this.onTap,
    // InputCounterWidgetBuilder	生成自定义 InputDecorator.counter 小部件的回调
    this.buildCounter,
  }) : assert(textAlign != null),
       assert(autofocus != null),
       assert(obscureText != null),
       assert(autocorrect != null),
       assert(maxLengthEnforced != null),
       assert(scrollPadding != null),
       assert(dragStartBehavior != null),
       assert(maxLines == null || maxLines > 0),
       assert(maxLength == null || maxLength == TextField.noMaxLength || maxLength > 0),
       keyboardType = keyboardType ?? (maxLines == 1 ? TextInputType.text : TextInputType.multiline),
       super(key: key);

}
```

**获取 TextField 的内容**

TextField 获取输入内容有两种方式：

- onChanged
当用户输入，TextField 的内容发生变化，TextField 就会调用它的 onChanged 回调。 因此 onChanged 可以实时查看 TextField 的内容变化。

```d
TextField(
    onChanged: (String data) {
      //实时获取
      print(data);
    },
  )
```

- TextEditingController

TextEditingController 是 TextField 的控制类，可以控制 TextField 的编辑，是 TextField 的 controller 属性，我们可以为 TextField 赋值自己创建的 TextEditingController 对象来控制 TextField。 

```d
class TextFieldWidget extends StatelessWidget {
  final TextEditingController _controller = TextEditingController();

  @override
  Widget build(BuildContext context) {
    return 
        ...
        TextField(
            controller: _controller,
        ),
        ...
    );
  }
}

// 然后使用 _controller.text 来访问 TextField 里的内容
```

**keyboardType**

类型为TextInputType，用于设置该输入框默认的键盘输入类型

```d
TextInputType的值	含义
TextInputType.text	文本输入键盘
TextInputType.multiline	多行文本，需和maxLines配合使用(设为null或大于1)
TextInputType.number	数字；会弹出数字键盘
TextInputType.phone	优化后的电话号码输入键盘；会弹出数字键盘并显示"* #"
TextInputType.datetime	优化后的日期输入键盘；Android上会显示“: -”
TextInputType.emailAddress	优化后的电子邮件地址；会显示“@ .”
TextInputType.url	优化后的url输入键盘； 会显示“/ .”
```

**textInputAction**

类型为TextInputAction，键盘动作按钮图标(即回车键位图标)

**textCapitalization**

类型为TextCapitalization，定义文本的大写格式

```
TextCapitalization.none	全部小写
TextCapitalization.words	每个单词的首字母大写
TextCapitalization.sentences	每个句子的首字母大写
TextCapitalization.characters	每个字每大写
```

#### Form

Form 是将多个表单元素组合起来的一个容器，可以将多个表单元素合并起来一起校验

```d
表单元素的 Widget 是 FormField 及其子类，最常用的是以下两个：

DropdownButtonFormField
TextFormField
```

**Form 的构造函数及参数说明**

```d
class Form extends StatefulWidget {

  const Form({
    Key key,
    // child	Widget	Form 的子 Widget	必选
    @required this.child,
    // bool	是否自动验证，默认为 false
	// true：每次输入有变动都会验证
	// false：只有调用 FormFieldState.validate 才会验证
    this.autovalidate = false,
    // WillPopCallback	决定 Form 所在的路由是否可以直接返回（如点击返回按钮），
    // 这个回调会返回一个 Future 对象，如果 Future 的最终结果是 false，则当前路由不会返回；
    // 如果为 true，则会返回到上一个路由。此属性通常用于拦截返回按钮。
    this.onWillPop,
    // VoidCallback	Form 的任意一个 子FormField 内容发生变化时会触发此回调
    this.onChanged,
  }) : assert(child != null),
       super(key: key);
    ...   
}
```

使用方法

```
1. 创建 Form，并为其添加 GlobalKey。
2. 在 Form 里添加表单元素，并给表单元素添加校验逻辑。
3. 添加一个按钮去提交并验证表单，提交并验证表单需要用到 Form 的 FormState 方法，Form 是 StatefulWidget，FormState 是 Form 的状态。
```

表单元素的验证逻辑是 validator 函数

```d
// validator 的使用方法是，当表单元素的 value 值，当校验不符合时，就返回一个 String ，这个 String 是错误提示，如果校验成功，就什么也不做。
validator: (value) { // 校验
    if (value?.length <= 8) {
      return '用户名必须大于 5 个字符';
    }
  }
```

###  SnackBar 和 Builder 的使用

SnackBar 是 Flutter 的底部消息提示

在使用 SnackBar 的时候，需要用到 Builder ，Builder 也是 Widget，Builder 可以用闭包的方式创建 子Widget，使得 子Widget 可以使用 父Widget 的上下文，这里你可能还不太好理解，下面看具体的案例。

**SnackBar 的构造函数及参数说明**

```d
class SnackBar extends StatelessWidget {
  const SnackBar({
    Key key,
    // Widget	SnackBar 显示的主要内容	必选
    @required this.content,
    // Color	SnackBar 的背景色	可选
    this.backgroundColor,
    // SnackBarAction	SnackBar 的按钮	可选
    this.action,
    // Duration	SnackBar 显示的时间 默认是4.0s
    this.duration = _kSnackBarDisplayDuration,
    // Animation	SnackBar 显示和消失的动画
    this.animation,
  }) : assert(content != null),
       assert(duration != null),
       super(key: key);
    ...   
}
```

两种使用方式

```
使用 Builder Widget
将使用 SnackBar 的 Widget 拆分出来
```

#### 使用 Builder Widget

Builder 的构造函数及参数说明

```d
class Builder extends StatelessWidget {
  const Builder({
    Key key,
    // WidgetBuilder	创建 子Widget	必选
    @required this.builder
  }) : assert(builder != null),
       super(key: key);
    ...
}
```

```d
// WidgetBuilder 是一个函数，定义为：
typedef WidgetBuilder = Widget Function(BuildContext context);

应该说 WidgetBuilder 函数实现了 Builder Widget 的核心功能，
Builder 只是 WidgetBuilder 的封装
在有的地方，其实是直接使用 WidgetBuilder 的
```

Builer 的使用

```d
class SnackBarBuilderWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.blue,
      ),
      home: Scaffold(
        appBar: AppBar(title: Text("Flutter UI Widget -- SnackBar 及 Builder")),
        body: Builder(
          builder: (context) => RaisedButton(
                child: Text('Show SnackBar'),
                onPressed: () {
                  Scaffold.of(context).showSnackBar(SnackBar(
                      content: Text('SnackBar'),
                      duration: Duration(seconds: 5)));
                },
              ),
        ),
      ),
    );
  }
}
```

#### 将使用 SnackBar 的 Widget 拆分出来

将使用 SnackBar 的 Widget 拆分出来后，SnackBar 的 Widget 使用的 context 就是 Scaffold 的 context。

```d
class SnackBarNoBuilerWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter Demo",
      theme: ThemeData(
        primaryColor: Colors.blue,
      ),
      home: Scaffold(
          appBar: AppBar(title: Text("Flutter UI基础Widget -- SnackBar")),
          body: SnackBarWidget()),
    );
  }
}

class SnackBarWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return RaisedButton(
      child: Text('Show SnackBar'),
      onPressed: () {
        Scaffold.of(context).showSnackBar(SnackBar(
            content: Text('SnackBar'), duration: Duration(seconds: 5)));
      },
    );
  }
}
```

**总结**

```
两种方式，使用 Builder Widget，或者将 SnackBar 拆分出来，
都可以实现底部消息提示，但是建议使用 Builder
```

运行效果如下：

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000385b379abf?imageslim)

### 对话框

- showAboutDialog()
- showDialog()

>showAboutDialog() 和 showDialog() 只能在 MaterialApp 的 子Widget 里使用，因为这两个方法需要使用 MaterialApp 的 context，所以解决问题的办法就和前一节讲 SnackBar 和 Builder 的使用 一样，要么使用 Builder，要么将 Widget 单独拆分出来。

#### 使用 Builder 和 showAboutDialog()、showDialog() 来弹对话框

**showAboutDialog()**

```d
Builder(
    builder: (context) => RaisedButton(
        onPressed: () {
            showAboutDialog(
                context: context,
                applicationName: 'lutter UI Widget -- 对话框',
                applicationVersion: '1.0.0');
            },
        child: Text('RaisedButton'))
    )
```

showAboutDialog() 方法的定义及参数说明

```d
void showAboutDialog({
  @required BuildContext context,
  String applicationName,
  String applicationVersion,
  Widget applicationIcon,
  String applicationLegalese,
  List<Widget> children,
}) {
    ...
}
```
