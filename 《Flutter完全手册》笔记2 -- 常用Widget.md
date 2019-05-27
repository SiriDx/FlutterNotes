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
build 函数里传来的 context 必须是 Scaffold 的 context

两个方法：
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
  // String	应用的名字
  String applicationName,
  // 	String	应用的版本
  String applicationVersion,
  // Widget	应用的 Icon
  Widget applicationIcon,
  // 	String	应用的法律信息
  String applicationLegalese,
  // List< Widget>	添加在后面的 Widget
  List<Widget> children,
}) {
    ...
}
```

**showDialog()**

showDialog() 的方法定义及参数说明

```d
Future<T> showDialog<T>({
  @required BuildContext context,
  // bool	点击背景是否可以关闭 dialog
  // 默认为true，点击背景可以关闭 dialog
  bool barrierDismissible = true,
  @Deprecated(
    'Instead of using the "child" argument, return the child from a closure '
    'provided to the "builder" argument. This will ensure that the BuildContext '
    'is appropriate for widgets built in the dialog.'
  ) Widget child,
  
  // WidgetBuilder	创建要显示的 Widget
  WidgetBuilder builder,
}) {
    ...
}
```

showDialog() 的使用方法为

```d
Builder(
    builder: (context) {
        return RaisedButton(
            onPressed: () {
                showDialog(context: context,builder: (context) => xxxDialog(...));
            },
            child: Text('showDialog'),
        );
    },
)
```

xxxDialog 就是 Flutter 里的对话框 Widget，在 Flutter 中，对话框 Widget 有3个：

**SimpleDialog**

SimpleDialog 是有一个标题和多个选项的简单对话框。

SimpleDialog 的构造函数及参数说明

```d
class SimpleDialog extends StatelessWidget {
  const SimpleDialog({
    Key key,
    // Widget	对话框的标题
通常是 Text
    this.title,
    // EdgeInsetsGeometry	标题的边距
    this.titlePadding = const EdgeInsets.fromLTRB(24.0, 24.0, 24.0, 0.0),
    // List< Widget>	对话框的按钮，显示在对话框标题的下面
通常是一组 SimpleDialogOption
    this.children,
    // EdgeInsetsGeometry	内容的边距
    this.contentPadding = const EdgeInsets.fromLTRB(0.0, 12.0, 0.0, 16.0),
    // Color	对话框的背景
    this.backgroundColor,
    // double	Button 相对于其父级放置的z坐标，
    // 这可以控制 Button 下的阴影大小, 该值必须>=0
    this.elevation,
    this.semanticLabel,
    // ShapeBorder	Widget 的形状
    this.shape,
  }) : assert(titlePadding != null),
       assert(contentPadding != null),
       super(key: key);
    ...   
}
```

SimpleDialogOption 的构造函数及参数说明

```d
// SimpleDialogOption 是 SimapleDialog 的选项按钮。
class SimpleDialogOption extends StatelessWidget {
  const SimpleDialogOption({
    Key key,
    // VoidCallback	点击事件，当手指松开时才触发
    this.onPressed,
    // Widget	显示的内容 一般是 Text
    this.child,
  }) : super(key: key);
  ...
}
```

使用方法:

```d
SimpleDialog(
    title: Text('SimpleDialog Demo'),
    children: <Widget>[
      SimpleDialogOption(
        child: Text('OK'),
            onPressed: () {
              Navigator.of(context).pop();
            },
       ),
      SimpleDialogOption(
        child: Text('CANCEL'),
            onPressed: () {
              Navigator.of(context).pop();
            },
          )
    ],
  )

// SimpleDialog 的关闭
// 使用Navigator.of(context).pop() 来关闭 SimpleDialog。

```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a00099b746c798?imageslim)

**AlertDialog**

AlertDialog 是警报对话框，具有标题和选型，可以让用户选择。

AlertDialog 的构造函数及参数说明

```d
class AlertDialog extends StatelessWidget {
  const AlertDialog({
    Key key,
    // Widget	对话框的标题
通常是 Text
    this.title,
    this.titlePadding,
    this.titleTextStyle,
    // Widget	对话框的内容
    this.content,
    this.contentPadding = const EdgeInsets.fromLTRB(24.0, 20.0, 24.0, 24.0),
    this.contentTextStyle,
    // List< Widget>	对话框的选型按钮
	// 通常是一组 FlatButton
    this.actions,
    this.backgroundColor,
    this.elevation,
    this.semanticLabel,
    this.shape,
  }) : assert(contentPadding != null),
       super(key: key);
    ...
}
```

使用方法:

```d
// AlertDialog 有一个 title 参数是标题，content 参数是内容，actions 是按钮数组。
AlertDialog(
    title: Text('AlertDialog'),
    content: SingleChildScrollView(
      child: ListBody(
        children: <Widget>[
          Text('This is an alert dialog'),
          Text('add two options.'),
        ],
      ),
    ),
    actions: <Widget>[
      FlatButton(
        child: Text('Ok'),
        onPressed: () {
          Navigator.of(context).pop();
        },
      ),
      FlatButton(
        child: Text('Cancel'),
        onPressed: () {
          Navigator.of(context).pop();
        },
      )
    ],
  )
  
// 使用Navigator.of(context).pop() 来关闭 AlertDialog。
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a0009e3230660c?imageslim)

**CupertinoAlertDialog**

CupertinoAlertDialog 是 iOS 风格的 AlertDialog。

CupertinoAlertDialog 的构造函数及参数说明

```d
class CupertinoAlertDialog extends StatelessWidget {
  const CupertinoAlertDialog({
    Key key,
    this.title,
    this.content,
    // List< Widget>	对话框的选型按钮
	// 通常是一组 CupertinoDialogAction
    this.actions = const <Widget>[],
    // ScrollController	可用于控制对话框中内容的滚动控制器
    this.scrollController,
    // ScrollController	可用于控制对话框中 actions 的滚动控制器
    this.actionScrollController,
  }) : assert(actions != null),
       super(key: key);
    ...
}
```

使用方法:

```d
CupertinoAlertDialog(                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               
    title: Text('CupertinoAlertDialog'),
    content: Text('This is a CupertinoAlertDialog'),
    actions: <Widget>[
      CupertinoDialogAction(
        child: Text('Ok'),
        onPressed: () {
          Navigator.of(context).pop();
        },
      ),
      CupertinoDialogAction(
        child: Text('Cancel'),
        onPressed: () {
          Navigator.of(context).pop();
        },
      )
    ],
  )
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000a34c7ed268?imageslim)

### BottomSheet

BottomSheet 的构造函数及参数说明

```d
class BottomSheet extends StatefulWidget {
  const BottomSheet({
    Key key,
    // AnimationController	控制 BottomSheet 的动画
    this.animationController,
    // bool	BottomSheet 是否可以拖动
	// 默认为true，可以向上拖、向下拖或者向下滑动关闭 BottomSheet
    this.enableDrag = true,
    // double	BottomSheet 相对于其父级放置的z坐标
    // 这可以控制 BottomSheet 的阴影大小
	// 默认值为0.0，该值必须>=0
    this.elevation = 0.0,
    // VoidCallback	当 BottomSheet 关闭的时候调用
	// 这个事件可能会多次调用
    @required this.onClosing,
    // WidgetBuilder	BottomSheet 要显示的内容
    @required this.builder
  }) 
  ...
}
```

BottomSheet 是从底部弹出来的对话框, 总共分为两种

**PersistentBottomSheet：持续的底部对话框**

持续的 BottomSheet，当 BottomSheet 弹出时，会一直覆盖在 APP 的界面上，而且背景是透明的，所以你还可以和界面有其他交互，且点击背景不能让 PersistentBottomSheet 消失。除非点击返回，PersistentBottomSheet 才会消失。

```d
PersistentBottomSheet 的创建和显示的方法：

- 可以用 Scaffold 的 showBottomSheet() 方法
- 也可以设置 Scaffold 的 bottomSheet 参数。

PersistentBottomSheet 的关闭

PersistentBottomSheet 出现时, TitleBar 的右上角会多一个返回的按钮，可以关闭 PersistentBottomSheet，因为 PersistentBottomSheet 无法通过点击背景关闭。

或者在 BottomSheet 里使用 Navigator.of(context).pop(); 来关闭 PersistentBottomSheet。
```

showBottomSheet() 方法的定义及参数说明

```d
PersistentBottomSheetController<T> showBottomSheet<T>({
  @required BuildContext context,
  @required WidgetBuilder builder,
}) {
  assert(context != null);
  assert(builder != null);
  return Scaffold.of(context).showBottomSheet<T>(builder);
}
```

showBottomSheet() 的使用

```d
class ShowPersistentBottomSheetWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Test',
      home: new Scaffold(
          appBar:
              new AppBar(title: new Text('Flutter UI Widget -- BottomSheet')),
          body: Builder(
            builder: (context) {
              return RaisedButton(
                onPressed: () {
                  showBottomSheet(
                      context: context,
                      builder: (context) => BottomSheet(
                          onClosing: () {},
                          builder: (context) => Container(
                                height: 200.0,
                                color: Colors.blue,
                                child: Center(
                                  child: RaisedButton(
                                    onPressed: () {
                                      Navigator.of(context).pop();
                                    },
                                    child: Text('dismissBottomSheet'),
                                  ),
                                ),
                              )));
                },
                child: Text('showBottomSheet'),
              );
            },
          )),
    );
  }
}
```

Scaffold 的 bottomSheet 参数

```
就是直接给 Scaffold 的 bottomSheet 参数赋 BottomSheet 的实例，
这样 Scaffold 一创建的时候就会显示 BottomSheet。
```

```d
class PersistentBottomSheetWidgetWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return new MaterialApp(
      title: 'Test',
      home: new Scaffold(
        appBar: new AppBar(title: new Text('Flutter UI Widget -- BottomSheet')),
        body: Builder(
          builder: (context) {
            return RaisedButton(
              onPressed: () {},
              child: Text('showBottomSheet'),
            );
          },
        ),
        bottomSheet: BottomSheet(
            onClosing: () {},
            builder: (context) => Container(
                  height: 200.0,
                  color: Colors.blue,
                  child: Center(
                    child: RaisedButton(
                      onPressed: () {
                        Navigator.of(context).pop();
                      },
                      child: Text('dismissBottomSheet'),
                    ),
                  ),
                )),
      ),
    );
  }
}
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000c0850831a0?imageslim)

**ModalBottomSheet：有半透明背景的对话框**

ModalBottomSheet 类似一个 Dialog，有一个半透明的背景层，点击背景，ModalBottomSheet 会消失。

使用方法:

```d
只能使用 showModalBottomSheet() 方法。
```

showModalBottomSheet() 方法的定义及参数说明

```d
Future<T> showModalBottomSheet<T>({
  @required BuildContext context,
  @required WidgetBuilder builder,
}) {
  assert(context != null);
  assert(builder != null);
  assert(debugCheckHasMaterialLocalizations(context));
  return Navigator.push(context, _ModalBottomSheetRoute<T>(
    builder: builder,
    theme: Theme.of(context, shadowThemeOnly: true),
    barrierLabel: MaterialLocalizations.of(context).modalBarrierDismissLabel,
  ));
}
```

showModalBottomSheet() 的使用

```d
可以看到 showModalBottomSheet() 和 showBottomSheet() 的参数一样，
所以使用方法也一样, 直接把 Demo 里的方法名替换为 showModalBottomSheet()。

可以看到 ModalBottomSheet 弹出来时，背景是半透明的，点击半透明的背景也能关闭 ModalBottomSheet。
```

![image](https://user-gold-cdn.xitu.io/2019/4/9/16a000c84b8fe2bc?imageslim)


### 菜单按钮

菜单按钮的 Widget 是：PopupMenuButton

PopupMenuButton 是一个按钮，当你点击按钮的时候，就会弹出类似 Android 选项菜单的框。

**PopupMenuButton 的构造函数及参数说明**

```d
class PopupMenuButton<T> extends StatefulWidget {
  const PopupMenuButton({
    Key key,
    // PopupMenuItemBuilder	要显示的菜单
    @required this.itemBuilder,
    // T	初始化的菜单项
	// 如果有，在菜单打开时会突出显示。
    this.initialValue,
    // PopupMenuItemSelected	当用户从此按钮创建的弹出菜单中选择一个值时调用。
    this.onSelected,
    // PopupMenuItemSelected	当用户在不选择项目的情况下关闭弹出菜单时调用。
    this.onCanceled,
    // 	String	当长按时显示的文本，用于盲人辅助模式下
    this.tooltip,
    this.elevation = 8.0,
    this.padding = const EdgeInsets.all(8.0),
    // 	Widget	这个按钮里显示的内容
    this.child,
    // 	Icon	这个按钮里显示的 Icon Widget，前面在 图片框和Icon 一节讲过如何使用 Icon Widget
    this.icon,
    // Offset	menu 相对按钮的偏移量
    this.offset = Offset.zero,
  }) : assert(itemBuilder != null),
       assert(offset != null),
       assert(!(child != null && icon != null)), // fails if passed both parameters
       super(key: key);
    ...   
}
```

![image](https://user-gold-cdn.xitu.io/2019/3/17/169874d66f279152?imageslim)

### 手势识别 Widget

手势事件的分类

```d
第一层是原始的 pointer events(指针事件)

它描述了屏幕上指针（例如，触摸，鼠标和触控笔）的位置和移动。
例如：PointerDownEvent、PointerUpEvent 、PointerMoveEvent 、PointerCancelEvent 等。

为了监听 pointer events，请使用 Listener Widget，
但一般建议使用 GestureDetector Widget，就是下面要介绍的。

第二层是可以对原始的事件识别成不同的手势

第二层是对第一层原始 pointer event 事件进行识别后的手势，
例如：单击、双击、长按、拖动等。

为了识别不同的手势，请使用 GestureDetector Widget。
```

**手势识别的 Widget —— GestureDetector**

GestureDetector 的构造函数及参数说明

```d
class GestureDetector extends StatelessWidget {
  GestureDetector({
    Key key,
    // Widget	要检测手势事件的 Widget
    this.child,
    // 手指触摸屏幕时产生 onTapDown 事件
    this.onTapDown,
    // 手指离开屏幕时产生 onTapUp 事件，之后便会触发 onTap 事件
    this.onTapUp,
    // 点击事件
    this.onTap,
    // 当触发 onTapDown 之后，取消点击，则会触发 onTapCancel
    // 后面的 onTapDown 和 onTapUp 都不会在触发
    this.onTapCancel,
    // 双击事件
    this.onDoubleTap,
    // 长按屏幕时触发，当监听了 onLongPress 事件时，则不能监听 
    // onLongPressDragStart、onLongPressDragUpdate、onLongPressDragUp
    this.onLongPress,
    // 长按屏幕，手指离开屏幕时触发，当监听了 onLongPressUp 事件时，则不能监听 
    // onLongPressDragStart、onLongPressDragUpdate、onLongPressDragUp
    this.onLongPressUp,
    // 长按屏幕，并准备开始拖动时触发，当监听了 onLongPressDragStart 事件时，
    // 则不能监听 onLongPress、onLongPressUp
    this.onLongPressDragStart,
    // 长按屏幕后并拖动时触发，当监听了 onLongPressDragUpdate 事件时，
    // 则不能监听 onLongPress、onLongPressUp
    this.onLongPressDragUpdate,
    // 长按屏幕拖动，手指离开屏幕时触发，当监听了 onLongPressDragUp 事件时，
    // 则不能监听 onLongPress、onLongPressUp
    this.onLongPressDragUp,
    // 手指接触屏幕，并且可能会开始垂直移动时触发
    this.onVerticalDragDown,
    // 手指接触屏幕，并且已经开始垂直移动时触发
    this.onVerticalDragStart,
    // 手指接触屏幕，并且垂直移动时触发
    this.onVerticalDragUpdate,
    // 手指接触屏幕垂直移动，然后手指离开屏幕时触发
    this.onVerticalDragEnd,
    // 当 onVerticalDragDown 没有完成时就会触发 onVerticalDragCancel
    this.onVerticalDragCancel,
    // 手指接触屏幕，并且可能会开始水平移动时触发
    this.onHorizontalDragDown,
    // 手指接触屏幕，并且已经开始水平移动时触发
    this.onHorizontalDragStart,
    // 手指接触屏幕，并且水平移动时触发
    this.onHorizontalDragUpdate,
    // 手指接触屏幕水平移动，然后手指离开屏幕时触发
    this.onHorizontalDragEnd,
    // 当 onHorizontalDragDown 没有完成时就会触发 onHorizontalDragCancel
    this.onHorizontalDragCancel,
    // 手指与屏幕接触，并且当有足够的压力时才会触发
	// 注意，这个事件仅在具有压力检测屏幕的设备上触发。
    this.onForcePressStart,
    //手指与屏幕接触，并且当有压力达到最大时触发
	// 注意，这个事件仅在具有压力检测屏幕的设备上触发。
    this.onForcePressPeak,
    // 手指与屏幕接触，有足够的压力并在屏幕上移动时触发
	// 注意，这个事件仅在具有压力检测屏幕的设备上触发。
    this.onForcePressUpdate,
    // 手指与屏幕分开时触发
	// 注意，这个事件仅在具有压力检测屏幕的设备上触发。
    this.onForcePressEnd,
   // 	手指与屏幕接触，并且可能开始移动时触发
    this.onPanDown,
    // 手指与屏幕接触，并且开始移动时触发
    this.onPanStart,
    // 手指在屏幕上移动时触发
    this.onPanUpdate,
    // 手指离开屏幕时触发
    this.onPanEnd,
    // 当 onPanDown 没有完成时触发 onPanCancel
    this.onPanCancel,
    // 手指与屏幕接触，并且即将有缩放操作时触发，初始值为 1.0
    this.onScaleStart,
    // 手指与屏幕接触，并且有缩放操作时触发
    this.onScaleUpdate,
    // onScaleStart 之后，手指离开屏幕时触发
    this.onScaleEnd,
    // HitTestBehavior 在命中测试期间，此手势检测器应如何表现。
    this.behavior,
    // 是否从语义树中排除这些手势。因为 Widget
    this.excludeFromSemantics = false,
    // DragStartBehavior	确定处理拖动开始行为的方式
    this.dragStartBehavior = DragStartBehavior.down,
  })
  ...
}
```

使用方式

```d
GestureDetector(
          child: Text('手势识别'),
          onTap: (){
            print('点击');
          },
          onDoubleTap: (){
            print('双击');
          },
          onLongPress:  (){
            print('长按');
          },
          onHorizontalDragStart: (DragStartDetails details){
            print('水平滑动');
          },)
```
