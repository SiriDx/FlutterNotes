## 实现底部 Navigation Bar

### 使用 Scaffold 的 bottomNavigationBar 属性

```d
return Scaffold(
    ...
    bottomNavigationBar: BottomNavigationBar(
        ...
    ),
    ...
)
```

### 实现 BottomNavigationBar 

**构造函数**

```d
class BottomNavigationBar extends StatefulWidget {
  BottomNavigationBar({
    Key key,
    /*
    List<BottomNavigationBarItem>	底部导航栏的选项，
    是一个List，每个选项的类型是 BottomNavigationBarItem
    */
    @required this.items,
    
    /*
    ValueChanged<int>	底部导航栏选项的点击事件，返回的值是选项的 index
    */
    this.onTap,
    
    // 当前选中的选项的 index
    this.currentIndex = 0,
    
    /*
   	定义底部导航栏的选项如何显示，有两个值：
   	BottomNavigationBarType.fix：当选项少于4个时，默认为这个类型，
   	如果 fixedColor 不为空，则选项选中时的颜色就是 fixedColor，
   	否则是 ThemeData.primaryColor，
   	导航栏背景颜色是ThemeData.canvasColor
   	
   	BottomNavigationBarType.shifting：当选项不小于4个时，默认为这个类型，
   	所有的选项都会被渲染为白色，但是导航栏的背景色是当前选中的选项的背景色
    */
    BottomNavigationBarType type,
    
    /*
    当 BottomNavigationBarType 的 type 是 fix 时，
    选中的选项的颜色就是 fixdColor
    */
    this.fixedColor,
    /// BottomNavigationBarItem 的 icon 的大小
    this.iconSize = 24.0,
  }) 
  ...
}
```

**BottomNavigationBarItem**

构造函数

```d
class BottomNavigationBarItem {
  const BottomNavigationBarItem({
  
  	// 选项的 Icon
    @required this.icon,
    
    // Widget	选项的 标题
    this.title,
    
    // 当选项选中时显示 activeIcon，
    // 但如果 activeIcon 没有设置的话，选中的时候会显示 icon
    Widget activeIcon,
    
    // 当 BottomNavigationBarType 的 type 是 shifting 时才有用，
    // 选中时会成为导航栏的背景色
    this.backgroundColor,
  })
  ...
}
```

为 BottomNavigationBar 添加 BottomNavigationBarItem 就可以写成：

```d
@override
Widget build(BuildContext context) {
    return Scaffold(
        body: Center(
        ),
        bottomNavigationBar: BottomNavigationBar(
            items: [
                BottomNavigationBarItem(icon: Icon(Icons.school), title: Text('热映')),
                BottomNavigationBarItem(icon: Icon(Icons.panorama_fish_eye), title: Text('找片')),
                BottomNavigationBarItem(icon: Icon(Icons.people), title: Text('我的'))
            ],//BottomNavigationBarItem 的 icon 用的是 Icon Widget实现的，这里是随便找的图标，先实现功能，后续在改成和 豆瓣电影 的一样
            currentIndex: 0,//默认选中的 index
            fixedColor: Colors.black, //选中时颜色变为黑色
            type: BottomNavigationBarType.fixed,//类型为 fixed
            onTap: _onItemTapped,
        ),
    );
}
    
void _onItemTapped(int index) {
}
```

## Flutter 异步编程

```d
1. 什么是异步编程？

异步是相对于同步来说的，
同步代码必须得等代码运行完后才能执行下一步的代码，
而异步代码不必等代码运行完，就可以直接运行下一步的，就叫异步代码。

2. Flutter 是运行在 '单线程' 上的

这里的 '单线程' 是带有单引号的，
并不是说 Flutter 只有一个线程，后面也会讲 Flutter 的线程模型，
而是指 Flutter 的 Dart 代码是运行在单线程上的。

3. 单线程？异步？

你可能会有疑问，Flutter 是运行在 '单线程' 上的，竟然还能异步？
当然能异步，JS也是单线程，也可以异步啊。
对于为何 Flutter 能够实现异步的原因，后面的章节会有讲到。

4. Dart 代码运行在 Isolate 上

Dart 代码运行的环境叫 Isolate，
Dart 代码默认跑在 root Isolate 上。
当 Dart 代码正在运行时，同一个 Isolate 中的其他代码无法同时运行。
Flutter 可以拥有多个 Isolates，但 Isolates 之间不能共享内存。

5. 异步操作的结果：Future 对象

当写异步函数的时候，可以像普通函数一样返回结果，
但是异步函数的返回的结果要用 Future 来包装。

Flutter 里的异步编程需要用到 Future 对象，
代表的是异步操作的结果。

Future 的使用方法是：
在异步编程里，要用 Future 来包装返回的结果，如：
	- Future 表示返回结果是类型T的异步操作。
	- Future 表示没有返回结果的异步操作。
除了返回期望的结果，也可能会抛异常出来。

当调用一个返回类型是 Future 对象的函数时，会发生如下两步：

- 函数会被加入到一个待执行的队列里，同时立即返回一个 uncompleted Future object（未完成的Future对象）
- 当队列里的函数执行完后，才会返回带有值的 complete Future object

6. Future API

Future API 是用来写异步代码的。
请和上面的 Future 对象区别开，Future 对象是一个对象，
Future API 指的是 then 等操作符。
Future 对象可以使用 Future API。

//函数返回一个Future<String>对象
Future<String> gatherNewsReports(){
    ...
}

//Future<String>对象 使用 Future API的then操作符
gatherNewsReports().then(...);

7. async 和 await

async 和 await 是 Dart 语言用来支持异步编程的关键字，
是 Dart 1.9 之后才加入的。
这两个关键字使得 Dart 可以用同步代码的方式来写异步代码，
而且不需要使用 Future API，极大的降低了异步代码的复杂度，而且方便阅读。
给函数加上 async关键字，使函数变为异步函数，
await 关键字只能在异步函数里用。

有了 async 和 await 之后，基本上就没有必要使用 Future API 了。

8. 如果想暂停代码运行直到 complete Future object 返回

有两种方法：

- 在 async 函数里使用 await 关键字,例如：

void gather() async{
    var content = await gatherNewsReports();
}

Future<String> gatherNewsReports(){
    ...
}

- 对 Future 对象使用then方法，例如：

void gather() {
    gatherNewsReports().then(...);
}

Future<String> gatherNewsReports(){
    ...
}

9. 在异步函数里使用 try catch 捕获异常

异步函数里有可能会抛出异常，所以使用 try catch 来捕获异常，代码如下：

void gather() async{
    try{
        var content = await gatherNewsReports();
    } catch(e){
        
    }
}
    
Future<String> gatherNewsReports(){
    ...
}

10. 为了让 Flutter 代码可以并行运行，可以创建自定义的 Isolate
```

### async 和 await 的使用

**async 函数（异步函数）**

异步函数是函数标有 async 修饰符的函数。async 只能用来修饰函数。

```d
foo() async => 42;

foo() => new Future(() => 42);
```

```d
当调用异步函数时，异步函数里的代码都是同步执行的，直到遇到 await ，
await 会立即返回一个 uncompleted Future 对象，
但函数的主体也会暂停执行，直到 uncompleted Future 执行完成，
返回一个带结果的 complete Future 对象，函数的主体才会接着运行。

如上面的代码，调用 foo() 后会返回一个 Future，
这个 Future 的结果的值是 42。
```
**await 表达式**

await 表达式允许你用写同步代码的方式来写异步代码。

await 会阻塞当前的操作，直到结果返回才会执行下一步。而且请注意，await 只能在 async 里面使用。

为什么要用 await 阻塞当前操作，这里有一个例子：假设要从服务器获取一个信息，并打印出来：

- 因为从服务器获取一个信息是一个耗时的操作，所以这个操作必须是异步的，所以使用 async 写成 异步函数
- 要把获取的信息打印出来，肯定要等到数据返回，所以这里需要 await 阻塞当前操作，直到数据回来

```d
  void getFromServer() async{
    var content = await fetch();
    print(content);
  }

  String fetch(){
      return 'infomation';
  }
```

如果 await 后面的表达式返回值不是 Future 对象，那么会自动被 Future 对象封装。

在代码运行到 await 之后，fetch() 被调用，产生 Future 对象，同时代码运行会暂停，等这个 Future 对象运行完成，当 Future 对象运行完成后，会返回结果，然后接着运行下一句 print(content)。

await 的作用就是等 Future 运行完成。

同时，要注意 await 的用法

```d
await 只能在 async 函数里使用，如果用在普通函数里，会报语法错误。
```

## Flutter 路由：Route

在 Flutter 中，全屏的页面是 Route，Navigator 通过堆管理Route对象，从而实现页面跳转。

在 Flutter 中跳转页面有两种方式，一种是 Simple Route(简单路由)，一种是 Named Route(命名路由)。

### 简单路由

```d
/*
从 FirstPage 跳转到 SecondPage, 使用 Navigator.push()，
push() 方法将新的 Route 添加到由 Navigator 管理的 Route 对象的堆顶。

那么新的 Route 对象从哪里来呢？
可以直接使用 MaterialPageRoute，或者你也可以创建自定义的 Route。

建议使用 MaterialPageRoute，因为 MaterialPageRoute 已经封装好了，
使用方便，而且自带页面切换动画，

并且适配了 Android和iOS，
如果是 Android 的话，页面进入动画是向上滑动并淡出，退出是相反的，
如果是 iOS 的话，页面进入动画是从右侧滑入，退出是相反的。
*/
Navigator.push(context, 
MaterialPageRoute(builder: (context) => SecondPage()));


/*
使用 Navigator.pop() 关闭当前页面，返回上一个页面。
pop() 方法将当前的 Route 对象从 Navigator 管理 Route 对象的堆中移除。
*/
Navigator.pop(context);
```

**MaterialPageRoute**

```d
MaterialPageRoute四个参数:

// WidgetBuilder	 创建Route里要显示的页面
builder

// RouteSettings Route的一些配置参数 
settings

/*
bool
是否保留之前的Route，
如果是true，前面的Route会保留在内存里，
如果是false，前面的Route在不需要的时候就会被回收掉（不是立即回收）
默认是true
*/
maintainState

// 用来标识新的页面是不是dialog，默认是false
fullscreenDialog
```

```d
class FirstPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Scaffold(
      appBar: AppBar(
        title: Text("Route -- FirstPage"),
      ),
      body: RaisedButton(
        child: Text("JUMP SecondRoute"),
        onPressed: () {
          Navigator.push(
              context, MaterialPageRoute(builder: (context) => SecondPage(),maintainState: false));
        },
      ),
    );
  }
}

class SecondPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Scaffold(
      appBar: AppBar(
        title: Text("Route -- SecondPage"),
      ),
      body: RaisedButton(
        child: Text("Go back!"),
        onPressed: () {
          Navigator.pop(context);
        },
      ),
    );
  }
}
```

### 命名路由

如果 App 里有很多地方都要跳转到相同的页面，那么以前面简单路由的方式跳转的话，就得重复写很多代码，这种情况下，为 Route 命名，使用命令路由就会很方便。

#### 定义路由表

路由表就是将所有Route集合起来，需要使用MaterialAPP的 routes 属性和 initialRoute 属性。

```d
    return MaterialApp(
        title: "Flutter Demo",
        theme: ThemeData(
          primaryColor: Colors.blue,
        ),
        
        // Flutter APP的初始路由 String
        initialRoute: '/First',
        
        // 路由表 Map<String, WidgetBuilder>
        routes: {
          '/First': (context) => FirstPage(),
          "/Second": (context) => SecondPage()
        },
        home: FirstPage());
  }
```

> 注意：命名的路由不能使用 '/'，因为 '/' 相当于是根节点，不要用根节点去命名路由。

```d
Navigator.pushNamed(context, '/Second');
Navigator.pop(context);
```

### Route -- 页面间的参数传递

假设要传递如下的参数：

```d
class PassArgumnets{
  String content;
  
  PassArgumnets(this.content);
}
```

**跳转页面时传递参数**

从一个页面跳转到新的页面，有两种方法传递参数：

- 通过 Navigator.push() 或者 Navigator.pushNamed() 方法传递参数

```d
1. Navigator.push()
/*
MaterialPageRoute 的属性里有 settings，
使用 RouteSettings 里的 arguments 来传递参数，

arguments 是一个 Object 类型，
可以传递 简单类型，如：String、List 等，

也可以自定义一个类，用来传递复杂类型，
例如下面的例子，自定义了 PassArgumnets 类型用来传递参数：
*/

Navigator.push(
   context,
   MaterialPageRoute(
    builder: (context) => SecondPage(),
    settings: RouteSettings(
        arguments:
            PassArgumnets('Data from FirstPage Navigator.push()'))),
);

2. Navigator.pushNamed()

/*
Navigator.pushNamed() 有一个可选参数 arguments，
arguments 是一个 Object 类型，
可以传递 简单类型，如：String、List 等，
也可以自定义一个类，用来传递复杂类型，
例如下面的例子，自定义了 PassArgumnets 类型用来传递参数：
*/
Navigator.pushNamed(context, '/Second',arguments: PassArgumnets('Data from FirstPage Navigator.pushNamed()'));
```

在新页面接受参数

```d
// 接受参数使用 ModalRoute.of 方法：

final PassArgumnets passArgumnets = ModalRoute.of(context).settings.arguments;
```

- 通过 Widget 的构造函数传递参数

```d
class SecondPage extends StatelessWidget {
  PassArgumnets passArgumnets;

  SecondPage(this.passArgumnets);
  
  ....
 }

Navigator.push(
    context,
    MaterialPageRoute(
        builder: (context) => SecondPage(PassArgumnets('Data from FirstPage Navigator.push()'))),
```

**页面关闭时返回数据给上一个页面**

页面关闭时返回数据给上一个页面,需要用到 Navigator.pop()，因为 pop() 方法里有一个 result 的可选参数，只要给这个 result 赋值，就会把数据返回给上一个页面

```d
// 在SecondPage里面返回数据
Navigator.pop(context,PassArgumnets('Return Data from SecondPage'));

// 在上一个页面接受数据
// 接受的数据就是 Navigator.pushNamed() 返回的结果，为了不阻塞 UI，这里用了 async。
_jumpToSecondPage(BuildContext context) async {
    var passArgumnets = await Navigator.pushNamed(context, '/Second',
        arguments: PassArgumnets('Data from FirstPage Navigator.pushNamed()'));
    print(passArgumnets.content);
}
```

## 声明式UI 的编程思维

### 从 命令式UI 到 声明式UI

假设要实现一个界面。

在 命令式UI 里，你需要手动创建所有 UI 的实例，如果界面有变化的话，就需要调用 UI实例的方法，例如 get、set 等方法。换句话说，当 UI 从一个状态转变到另一个状态，都需要对 UI 的实例操作一番，估计大家对这个过程也痛苦不已，尤其是 UI 有频繁、大规模的变动时。

当 UI 状态切换时，命令式UI 就需要写代码来实现，为了减轻开发者在不同 UI 状态之间切换写代码的负担，Flutter 采用了 声明式UI：开发者只需描述当前的 UI 状态，Framework 会自动将 UI 从上一个状态切换到下一个状态，而不需要额外的编码。

而且在 命令式UI 里，你想都不敢想的一些方法，在 声明式UI 里完全没问题，例如，Flutter 更改 UI ，不是通过 set 方法，改变 Widget 的值，而是重新 build 一个全新的 Widget，而且可以做到每帧都创建新的 Widget 也没问题，因为创建新的 Widget 在 Flutter 里的消耗足够小；而每帧都创建新的 UI ，这个在 命令式UI 里，是没法想象的。

### 声明式的 Flutter

Flutter 是 声明式的，就意味着 Flutter 的 UI 反应的是当前的状态，就像下面的函数：

![image](https://user-gold-cdn.xitu.io/2019/4/22/16a431f82601474f?imageslim)

这个函数的输入是 状态（state），函数 f 会重建 Widget，然后输出 UI，状态指的就是 APP 生命周期内变化的数据，而Flutter 的 UI 就是状态的可视化展示。

例如，假设设置菜单中有一个开关，这个开关的功能是将 Flutter APP 的主题色由红色变为蓝色，用户打开这个开关，因为值变了，所以当前 Flutter APP 的状态也发生了变化，会触发 UI 从头到尾的重建，在重建的时候，会读取当前状态的值，例如，当前的主题色是蓝色，从而让 UI 的显示发生变化。

在前面实现城市选择页面时，就是更改了变量 curCity 的值，来实现 UI 内容的刷新：

```d
setState(() {
  curCity =  selectCity;
});
```

声明式UI 在更改 UI 时，不需要对 UI 进行操作，使用的还是原来 UI 的代码，只需要更改值，使状态发生变化，就能触发 UI 刷新。

声明式UI 有很多好处：

- 不管 UI 有多少种状态，写 UI 的代码只有一份。
- 你想要 UI 显示成什么样子，只要描述当前的状态就行
- 相对 UI 进行更改，也只需要描述更改后的 UI 状态就行，不需要像命令式一样进行很多操作，因为 Framework 会自动处理


当然除了好处之外，也是会有坏处的，主要坏处就是：

当 UI 的整个结构发生变化时，就不能复用原来的 UI 代码，需要重新实现。请看下面的详细对比说明。

#### 命令式UI 和 声明式UI 实现 UI 更改的对比

声明式UI 的编程风格不像 命令式UI 的编程风格那么直观，现在通过如下的一个例子来说明。

假设要实现一个如下的功能：

![image](https://user-gold-cdn.xitu.io/2019/3/3/16941be1f260bffb?imageslim)

先实现左边的页面，然后将 ViewB b 里的子 View 替换为 ViewC c3，且将 ViewB b 的背景由黄色变为红色。

**使用命令式实现**

如果用命令式的代码来实现，那么代码就会是这样子的（）：

首先实现左边的页面：

```d
// 实现左边的页面
ViewA a = new ViewA(...)
ViewB b = new ViewB(...)
ViewC c1 = new ViewC(...)
ViewC c2 = New ViewC(...)
a.add(b)
b.add(c1)
b.add(c2)
```

对左边的页面进行修改，实现右边的页面：

```d
//实现右边的页面
b.setColor(red)
b.clearChildren()
ViewC c3 = new ViewC(...)
b.add(c3)
```

**使用声明式实现**

如果是用 Flutter 实现，那么 Flutter 声明式的代码就是这样的：

首先实现左边的页面：

```d
Widget build(BuildContext context) {
    return ViewA{
        child: ViewB{
            color: yellow,
            children: [
                ViewC(...),
                ViewC(...)
            ]
        }
    }
}
```

那这种用声明式写的 UI 怎么更改呢？

前面讲过 UI 如果会变化的话，就要用 StatefulWidget，setState() 会触发 StatefulWidget 重新构建 Widget。所以要改成右边的页面，Flutter 不是更改原来页面的 Widget，而是重新创建新的 Widget 实例：

```d
boolean isChanged = false;

void change(){
    setState((){
      isChanged = true;  
    })
}

Widget build(BuildContext context) {
    if(!isChanged){
        return ViewA{
            child: ViewB{
                color: red,
                children: [
                    ViewC(...),
                ]
            }
        }
    }else{
        return ViewA{
            child: ViewB{
                color: red,
                children: [
                    ViewC(...),
                    ViewC(...)
                ]
            }
        }        
    }
}
```

通过变量 isChanged 的值来返回不同的 Widget，从而达到将左边的页面改成右边页面的目的。

当 UI 结构发生变化时，就需要重新写一份。
