## Dart语法

#### 简单的Dart程序

```d
// 定义个方法。
printNumber(num aNumber

) {
	//$variableName (or ${expression})
	//字符串插值：在字符串中引用变量或者表达式。
	print('The number is $aNumber.');
}

// 这是程序执行的入口。
main() {
	var number = 42; // 定义并初始化一个变量。
	printNumber(number); // 调用一个方法。
}
```

#### 重要概念

- 能够使用var引用的东西都是对象， 每个对象都是一个类的实例。在 Dart中 numbers、functions和 null 都是对象。所有的对象都继承于 Object 类。

- 尽管dart是强类型语言, 不一定需要指定类型, 因为dart会进行类型推导. 在上面代码, number推导为int类型. 如果要明确说明不需要任何类型，可以使用特殊类型dynamic标识。

- Dart支持泛型，如 List<int>（int类型的对象列表）或 List<dynamic> (任何对象类型的列表)。

- Dart支持顶级函数（例如main函数）, 也支持将函数绑定到类(静态方法)或对象(实例方法)中。函数内部也可以创建函数（嵌套函数或本地函数）。

- Dart支持顶级变量，以及绑定到类(静态变量)或对象(实例变量)的变量, 实例变量有时候也成为字段或属性

- Dart没有关键字public、protected和private, 如果一个标识符以`_`开头, 它在当前库是私有的

- 标识符可以以字母或下划线`_`开头，后跟这些字符加数字的任意组合

- Dart可以使用`表达式`和`语句`, 例如, 条件表达式 `condition ? expr1 : expr2`, 得到的结果值为expr1 或 expr2, 而if-else语句没有返回值. 一个语句中通常包含一个或多个表达式, 而一个表达式中不能直接包含语句.

- Dart工具会报告两种问题: `warnings` 和 `errors`. 警告只是表明您的代码可能无法正常工作，但它们不会阻止您的程序执行。 错误可以是编译时或运行时。 编译时错误会阻止代码执行; 运行时错误导致代码执行时引发异常。


#### 关键字

...

### 变量

创建一个变量, 并初始化

```d
// name 推导出类型为String
var name = 'Bob';

// 如果一个对象不局限于单一类型, 可以指定为 Object 或 dynamic类型
dynamic name = 'Bob';
Object name = 'Bob';

// 指定变量的类型
String name = 'Bob';
```

#### 默认值

未初始化的变量默认值为`null`. number类型的对象初始值也是null.

```d
int lineCount;
// 调用assert在生产环境中会被忽略. 开发环境下assert(条件)中的条件如果不为true, 会抛出异常.
assert(lineCount == null);
``` 

#### Final 和 const

如果不想改变一个变量的值, 可以使用final或者const. final变量只能设置一次; const变量是一个编译时常量. final顶级变量或者类在它第一次使用的时候进行初始化.
> 实例变量可以使用final, 不能用const. final实例变量必须在构造之前进行初始化 -- 变量声明, 构造函数的参数, 或者在构造体的初始化列表中

```d
final name = 'Bob'; // Without a type annotation
final String nickname = 'Bobby';

name = 'Alice'; // 报错: final变量只能被设置一次
```

如果一个const变量在类中, 标记为`static const`. 定义时必须赋值.

```d
const bar = 1000000; // Unit of pressure (dynes/cm2)
const double atm = 1.01325 * bar; // Standard atmosphere
```

### 内置类型

Dart支持以下类型:

```
numbers
strings
booleans
lists (also known as arrays)
sets
maps
runes (for expressing Unicode characters in a string)
symbols
```

可以通过字面直接初始化一个对象. 如,  'this is a string' 是一个字符串, `true`是一个布尔值
因为Dart中每个变量所指的对象(类的实例), 可以通过构造方法来初始化, 因此, 一些内置类型提供他们自己的构造方法. 如, 可以通过Map()创建一个map对象.

#### Numbers

- Dart包含int和double两种类型, int 和 double都是num的子类. 
- num类中包含基本的运算, 如 +, -, /, *
- 也可以在num中找到abs(), ceil(), floor()等方法.

```d
// int
var x = 1;
var hex = 0xDEADBEEF;

// double
var y = 1.1;
var exponents = 1.42e5;

// Dart 2.1, 必要的时候, 赋值为int时, 会自动转成double(2.1之前报错)
double z = 1; // Equivalent to double z = 1.0.
```

string 转 number

```d
// String -> int
var one = int.parse('1');

// String -> double
var onePointOne = double.parse('1.1');

// int -> String
String oneAsString = 1.toString();

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
```

数字字面量为编译时常量。 很多算术表达式 只要其操作数是常量，则表达式结果 也是编译时常量。

```d
const msPerSecond = 1000;
const secondsUntilRetry = 5;
const msUntilRetry = secondsUntilRetry * msPerSecond;
```

#### Strings

Dart 字符串是 UTF-16 编码的字符序列。 可以使用单引号或者双引号来创建字符串：

```d
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
```

可以在字符串中使用表达式，用法是这样的： ${expression}。如果表达式是一个标识符，可以省略 {}。 如果表达式的结果为一个对象，则 Dart 会调用对象的 toString() 函数来获取一个字符串。

```d
var s = 'siri';

var result1 = 'my name is $s, nice to meet you.'
        
var result2 = 'my name is ${s.toUpperCase()}, nice to meet you.'
```

可以把多个字符串放一起, 或使用`+`号来拼接字符串
```d
var s1 = 'String '
    'concatenation'
    " works even over line breaks.";

var s2 = 'The + operator ' + 'works, as well.';
```

使用三个单引号或者双引号也可以 创建多行字符串对象：

```d
var s1 = '''
You can create
multi-line strings like this one.
''';

var s2 = """This is also a
multi-line string.""";
```

#### Booleans

Dart通过bool类来表示布尔值, true和false两个对象, 都是编译时常量. Dart的类型安全意味着不能使用 if（非布尔值）或 assert（非布尔值）等代码, 如, OC中if (1) 在dart中会报错.

#### Lists

Dart中的数组为`List`对象

```d
// dart会自动推导list类型为List<int>, 因此无法添加非int类型对象到该数组中
// 第一个元素: list[0]
// 最后一个元素: list[list.length - 1]
var list = [1, 2, 3];

// 在值前面使用const, 来创建一个编译时常量数组
var constantList = const [1, 2, 3];
```

#### Sets

Dart通过`Set`对象, 来创建一个无序集合, 且集每个元素都是唯一的.

```d
// 会自动推导halogens类型为Set<String>, 如果加入一个错误的类型到集合中运行时会报错
var halogens = {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

创建一个空set对象

```d
var names = <String>{};
Set<String> names = {};

// 这种方式创建的对象是map不是set
var names = {};
```
其他用法

```d
// 添加元素
var elements = <String>{};
elements.add('fluorine');
elements.addAll(halogens);

//.length获取set的元素个数
assert(elements.length == 5);

// 创建编译时常量的set
final constantSet =
    const {'fluorine', 'chlorine', 'bromine', 'iodine', 'astatine'};
```

#### Maps

map是一个键值对集合的对象. key和value可以是任何对象类型, key是唯一, value可以重复. 

- 定义的时候直接赋值来创建map

```d
// 会自动推导gifts类型为Map<String, String>, 如果加入一个错误的类型到集合中运行时会报错
var gifts = {
  // Key:    Value
  'first': 'partridge',
  'second': 'turtledoves',
  'fifth': 'golden rings'
};
```

- 使用构造方法创建map

```d
var gifts = Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';
```

- 其他

```
// 添加新的键值对
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';

// 根据key或者值
assert(gifts['first'] == 'partridge');
assert(gifts['fifth'] == null);

// .length获取键值对个数
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);

// 构建编译时常量的map对象
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};
```

#### Runes(符文)

...emoji

#### Symbols

...

### Functions

Dart是面向对象的编程语言, 每个函数都是一个对象, 类型为`Function`.

```d
bool isNoble(int atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}

// 省略返回值类型和参数类型(不推荐)
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}

// 当函数中只有一行表达式,可以使用简写
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

#### 可选参数

- 指定参数名, 格式: {param1, param2, …}

```d
void enableFlags({bool bold, bool hidden}) {...}
enableFlags(bold: true, hidden: false);
```

- 位置参数

```d
String say(String from, String msg, [String device]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  return result;
}

assert(say('Bob', 'Howdy') == 'Bob says Howdy');


```

- 默认值, 默认值必须是编译时常量, 如果没有设置默认值, 则默认为`null`

```d
// 命名参数
void enableFlags({bool bold = false, bool hidden = false}) {...}
enableFlags(bold: true);

// 位置参数
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');
    
```

```d
// 给lists和maps设置一个默认值 
void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list:  $list');
  print('gifts: $gifts');
}
```

#### main()函数

每个app有一个 top-level main()函数，该函数是app的入口。 main() 函数返回值为 void, 并且有个可选的 List<String> 参数

```d
void main(List<String> arguments) {
  print(arguments);
  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

```d
//函数作为另一个函数的参数
void printElement(int element) {
  print(element);
}

var list = [1, 2, 3];
list.forEach(printElement);

// 函数赋值给一个变量
var loudify = (msg) => '!!! ${msg.toUpperCase()} !!!';
assert(loudify('hello') == '!!! HELLO !!!');
```

#### 匿名函数

匿名函数anonymous(也叫lambda或closure)

```
语法:
([[Type] param1[, …]]) { 
  代码块; 
}; 
```

```d
// 函数定义
void showDesc(var name, Function log) {
  log(name);
}

// 函数调用，匿名函数作为参数
showDesc("Bruce", (name) {
    print("name = $name");
  });

// 输出结果
name = Bruce
```

#### 作用域

Dart变量的作用域在写代码的时候就确定过了。 基本上大括号里面定义的变量就只能在大括号里面访问

#### 闭包
闭包是一个函数对象, 不管该对象在何处被调用， 该对象都可以访问其作用域内 的变量。
方法可以封闭定义到其作用域内的变量。 

下面的示例中，makeAdder() 捕获到了变量 addBy。 不管你在那里执行 makeAdder() 所返回的函数，都可以使用 addBy 参数。

```d
/// 函数作为返回值
Function makeAdder(num addBy) {
  return (num i) => addBy + i;
}

void main() {
  // Create a function that adds 2.
  var add2 = makeAdder(2);

  // Create a function that adds 4.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

#### 函数比较

比较`顶级函数`、`静态函数`和`实例函数` 是否相等：

```d
void foo() {} // A top-level function

class A {
  static void bar() {} // A static method
  void baz() {} // An instance method
}

void main() {
  var x;

  // Comparing top-level functions.
  x = foo;
  assert(foo == x);

  // Comparing static methods.
  x = A.bar;
  assert(A.bar == x);

  // Comparing instance methods.
  var v = A(); // Instance #1 of A
  var w = A(); // Instance #2 of A
  var y = w;
  x = w.baz;

  // These closures refer to the same instance (#2),
  // so they're equal.
  assert(y.baz == x);

  // These closures refer to different instances,
  // so they're unequal.
  assert(v.baz != w.baz);
}
```

#### 返回值

所有的函数都返回一个值。如果没有指定返回值，则默认return null; 隐式添加到函数体后。

```d
foo() {}

assert(foo() == null);
```

### 操作符

#### 算术运算符 Arithmetic operators

```
+, -, *, 负数, %

/: 除, 返回一个double值
~/: 除, 返回一个int值
```

```d
assert(2 + 3 == 5);
assert(2 - 3 == -1);
assert(2 * 3 == 6);
assert(5 / 2 == 2.5); // Result is a double
assert(5 ~/ 2 == 2); // Result is an int
assert(5 % 2 == 1); // Remainder

assert('5/2 = ${5 ~/ 2} r ${5 % 2}' == '5/2 = 2 r 1');
```

++var, var++, --var, var--

```d
var a, b;

a = 0;
b = ++a; // Increment a before b gets its value.
assert(a == b); // 1 == 1

a = 0;
b = a++; // Increment a AFTER b gets its value.
assert(a != b); // 1 != 0

a = 0;
b = --a; // Decrement a before b gets its value.
assert(a == b); // -1 == -1

a = 0;
b = a--; // Decrement a AFTER b gets its value.
assert(a != b); // -1 != 0
```

#### 关系运算符 Equality and relational operators

==, !=, >, <, >=, <=

```d
使用=号来判断来个对象是否是同一个东西
(如果需要知道两个对象是否确切的同一个对象, 使用 identical())
```

#### 类型检测 Type test operators

- as, 作为什么类型
-  is, 是什么类型
-  is!, 不是什么类型

```
if (emp is Person) {
  // Type check
  emp.firstName = 'Bob';
}

// 简写(如果emp不是Person类型, 可能跑出异常)
(emp as Person).firstName = 'Bob';

```

#### 赋值运算符 Assignment operators

使用`=`号和`??=`进行赋值

`??=`, 如一个变量值为null时进行赋值, 如果不为null, 则该变量值保持不变

```
// 给a赋值
a = value;

// 如果b为null时, 给b赋值; 否则, b保持之前的值.
b ??= value;
```

组合赋值运算符(+=, *=, /= ...)

```d
var a = 2; // Assign using =
a *= 3; // Assign and multiply: a = a * 3
assert(a == 6);
```

#### 逻辑运算符 Logical operators

非`!`, 或`||`, 与`&&`

```d
if (!done && (col == 0 || col == 3)) {
  // ...Do something...
}
```

#### 位运算 Bitwise and shift operators

...

#### 条件表达式 Conditional expressions

两种条件表达式

- `condition ? expr1 : expr2`
- `expr1 ?? expr2`, 如expr1不为null, 返回expr1, 如果为null返回expr2

```d
var visibility = isPublic ? 'public' : 'private';
String playerName(String name) => name ?? 'Guest';
```

其他方式实现以上方案, 相对没那么简洁

```d
// Slightly longer version uses ?: operator.
String playerName(String name) => name != null ? name : 'Guest';

// Very long version uses if-else statement.
String playerName(String name) {
  if (name != null) {
    return name;
  } else {
    return 'Guest';
  }
}
```

#### 层叠符号 Cascade notation (..)

使用`..`来对同一个对象进行一系列操作(函数调用, 访问字段). 省去多余变量的创建, 使代码更加流程 

```d
querySelector('#confirm') // Get an object.
  ..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```
上面的代码和下面的代码功能一样：

```d
var button = querySelector('#button');
button.text = 'Confirm';
button.classes.add('important');
button.onClick.listen((e) => window.alert('Confirmed!'));
```

cascade嵌套

```d
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

注意: 

```d
var sb = StringBuffer();

// 报错, sb.write()返回void, void不能够使用cascade
sb.write('foo')
  ..write('bar');// Error: method 'write' isn't defined for 'void'.
```

> 严格来说, 两个点表示的层叠符号, 不属于操作符, 只是dart的语法

#### 其他操作符 Other operators

- `()` 函数调用
- `[]` 访问列表
- `.` 成员访问
- `?.` 带条件的成员访问

### 流程控制语句

```
Dart通过以下方式进行对应的流程控制

if and else
for loops
while and do-while loops
break and continue
switch and case
assert
```

#### If and else

```d
if (isRaining()) {
  you.bringRainCoat();
} else if (isSnowing()) {
  you.wearJacket();
} else {
  car.putTopDown();
}
```

#### For loops

标准for循环遍历

```d
var message = StringBuffer('Dart is fun');
for (var i = 0; i < 5; i++) {
  message.write('!');
}
```

forEach()遍历

```d
candidates.forEach((candidate) => candidate.interview());
``` 

for-in

```d
var collection = [0, 1, 2];
for (var x in collection) {
  print(x); // 0 1 2
}
```

#### While and do-while

```d
while (!isDone()) {
  doSomething();
}

do {
  printLine();
} while (!atEndOfPage());
```

#### Break and continue

使用`break`跳出循环

```d
while (true) {
  if (shutDownRequested()) break;
  processIncomingRequests();
}
```

使用`continue `进入下一次循环

```d
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}
```

其他方式

```d
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```

#### Switch and case

使用`==`对integer, 字符串, 编译时常量进行对比. 比较的对象必须是同一个类的对象(不能是子类)

```d
var command = 'OPEN';
switch (command) {
  case 'CLOSED':
    executeClosed();
    break;
  case 'PENDING':
    executePending();
    break;
  case 'APPROVED':
    executeApproved();
    break;
  case 'DENIED':
    executeDenied();
    break;
  case 'OPEN':
    executeOpen();
    break;
  default:
    executeUnknown();
}
```

非空的case中, 不能省略break

空case语句中可以省略break, 达到fall-through效果

```d
var command = 'CLOSED';
switch (command) {
  case 'CLOSED': // Empty case falls through.
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

continue

```d
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
    executeClosed();
    continue nowClosed;
  // Continues executing at the nowClosed label.

  nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
}
```

#### 断言 Assert

注意: Assert只有在开发代码中才会生效, 在生产代码中没有效果

```d
// Make sure the variable has a non-null value.
assert(text != null);

// Make sure the value is less than 100.
assert(number < 100);

// Make sure this is an https URL.
assert(urlString.startsWith('https'));
```

给assert第二个参数传入字符串, 用来携带信息

```d
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

### 异常

代码中可以出现异常和捕获异常。异常表示一些 未知的错误情况。如果异常没有捕获， 则异常会抛出，导致 抛出异常的代码终止执行。

Dart没有检查异常, 方法中没有声明了他们可能会抛出的异常，页不要求捕获每一个异常。

异常包含 `Exception` 和 `rror` 类型， 以及一些子类型, 还可以定义自己的异常类型. Dart程序可以抛出任何非空对象作为异常(不仅仅是Exception和Error对象)

#### Throw

抛出异常

```d
throw FormatException('Expected at least 1 section');
```
也可以跑出任意对象作为异常

```d
throw 'Out of llamas!';
```
简写

```d
void distanceTo(Point other) => throw UnimplementedError();
```

#### Catch

捕获并处理异常

```d
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  buyMoreLlamas();
}
```

捕获多个异常

```d
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // 未指定异常类型, 处理所有异常
  print('Something really unknown: $e');
}
```

可以使用`on`或者`catch`, 或者同时使用两者来处理异常. 

需要指定异常类型的时候使用`on`

需要获取异常对象的时候使用`catch `

可以通过传入的参数指定捕获1或2个对象, 第一个参数:异常对象, 第二个参数:堆栈信息

```d
try {
  // ···
} on Exception catch (e) {
  print('Exception details:\n $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
}
```

处理部分异常, 使用rethrow重新跑出异常

```d
void misbehave() {
  try {
    dynamic foo = true;
    print(foo++); // Runtime error
  } catch (e) {
    print('misbehave() partially handled ${e.runtimeType}.');
    rethrow; // Allow callers to see the exception.
  }
}

void main() {
  try {
    misbehave();
  } catch (e) {
    print('main() finished handling ${e.runtimeType}.');
  }
}
```

#### Finally

无论是否出现异常, 为了保证某些代码最终都会执行, 使用finally

如果异常未被捕获, 在finally中代码执行完毕之后, 异常会被抛出

```d
try {
  breedMoreLlamas();
} finally {
  // Always clean up, even if an exception is thrown.
  cleanLlamaStalls();
}
```

捕获并处理对应异常代码执行完后, 才会执行finally中的代码

```d
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}
```

### 类

- 面向对象, 基于 mixin 的继承机制
- 每个对象都是一个类的实例, 所有的类都继承于 Object
- 基于Mixin的继承意味着, 虽然每个类（Object除外）都只有一个父类，但是一个类的结构可以在多个层级的类中进行复用

#### Using class members

对象由方法和实例变量组成

使用`.`来指向实例对象中的变量或方法

```d
var p = Point(2, 2);

// Set the value of the instance variable y.
p.y = 3;

// Get the value of y.
assert(p.y == 3);

// Invoke distanceTo() on p.
num distance = p.distanceTo(Point(4, 4));
```

使用`?.`来避免左边元素为null时抛出异常

```d
// If p is non-null, set its y value to 4.
p?.y = 4;
```

#### 构造器 Using constructors

使用构造器来创建对象

```d
var p1 = Point(2, 2);
var p2 = Point.fromJson({'x': 1, 'y': 2});
```

```d
// Dart 2中, new可写可不写
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```

一些类提供常量构造器

```d
var p = const ImmutablePoint(2, 2);
```

构建两个相同的编译时常量, 得到的是同一个实例对象

```d
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // They are the same instance!
```

可以省略构造方法或者值前面的const

```d
// 太多const关键字
const pointAndLine = const {
  'point': const [const ImmutablePoint(0, 0)],
  'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
};
```

```d
// 只使用一个const
const pointAndLine = {
  'point': [ImmutablePoint(0, 0)],
  'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
};
```

以上是类的使用, 余下的部分展示类的实现

#### 获取对象类型 Getting an object’s type

使用 Object 的 runtimeType 属性来判断实例 的类型，该属性 返回一个 Type 对象

```d
print('The type of a is ${a.runtimeType}');
```

#### 实例变量 Instance variables

```d
class Point {
  num x; //声明实例变量x, 默认值为null.
  num y; //声明实例变量y, 默认值为 null.
  num z = 0; //声明实例变量z, 默认值为 0.
}
```

未初始化的实例变量默认值为null

所哟偶实例变量都有一个隐式的getter方法. 非final实例变量有一个隐式的setter方法

```d
class Point {
  num x;
  num y;
}

void main() {
  var point = Point();
  point.x = 4; // Use the setter method for x.
  assert(point.x == 4); // Use the getter method for x.
  assert(point.y == null); // Values default to null.
}
```

如果实例变量在声明的时候直接初始化, 该值在实例创建前就设置好了(构造函数和初始列表执行之前)

#### 构造函数 Constructors

创建一个与类名一样的函数来声明构造函数

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

this指向当前对象. 有名字重复冲突的时候才需要使用this, 否则this可以省略

可以使用语法糖将构造函数的参数值赋值给实例变量

```d
class Point {
  num x, y;

  // Syntactic sugar for setting x and y
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

#### 默认构造函数

如果你没有定义构造函数，则会有个默认构造函数。 默认构造函数没有参数，并且会调用父类的没有参数的构造函数。

#### 构造函数不能继承

子类不会继承父类的构造函数。子类如果没有定义构造函数，则只有一个默认构造函数(没有名字没有参数）。

#### 命名的构造函数 Named constructors

给构造函数取名

```d
class Point {
  num x, y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```

注意：构造函数不能继承，所以父类的命名构造函数也不会被继承。如果你希望子类也有父一样的命名构造函数， 必须在子类中自己实现该构造函数。

#### 调用父类的构造函数(非默认) Invoking a non-default superclass constructor

默认，子类的构造函数会自动调用父类的无名无参数的默认构造函数, 在子类构造函数体开始执行的位置会调用父类的构造函数. 如果用到initializer list（初始化参数列表），则初始化参数列表在父类构造函数执行之前执行。 下面是构造函数执行顺序：

- 初始化列表 initializer list 
- 父类的无参数构造函数 superclass’s no-arg constructor
- 主类的无参数构造函数 main class’s no-arg constructor

如果超类没有无名无参数构造函数， 则需手动的调用父类的其他构造函数。 在构造函数参数后使用冒号 `:` 指定父类构造函数。

```d
class Person {
  String firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // Person does not have a default constructor;
  // you must call super.fromJson(data).
  Employee.fromJson(Map data) : super.fromJson(data) {
    print('in Employee');
  }
}

main() {
  var emp = new Employee.fromJson({});

  // Prints:
  // in Person
  // in Employee
  if (emp is Person) {
    // Type check
    emp.firstName = 'Bob';
  }
  (emp as Person).firstName = 'Bob';
}
```

父类的构造函数的参数在子类构造函数调用前就已经赋值, 因此参数可以是一个表达式, 如函数调用

```d
class Employee extends Person {
  Employee() : super.fromJson(getDefaultData());
  // ···
}
```

#### 初始化列表 Initializer list

除了初始化父类的构造函数, 你也可以在构造函数运行前初始化实例变量, 通过`:`分号隔开

```d
// 初始化列表在构造函数运行前就设置好所有变量的值
// 初始化中不能使用this
Point.fromJson(Map<String, num> json)
    : x = json['x'],
      y = json['y'] {
  print('In Point.fromJson(): ($x, $y)');
}
```

开发环境中, 可以在初始化列表中使用assert来检验传入值的有效性

```d
Point.withAssert(this.x, this.y) : assert(x >= 0) {
  print('In Point.withAssert(): ($x, $y)');
}
```

使用初始化列表配置final字段

```d
import 'dart:math';
​
class Point {
  final num x;
  final num y;
  final num distanceFromOrigin;
​
  Point(x, y)
      : x = x,
        y = y,
        distanceFromOrigin = sqrt(x * x + y * y);
}
​
main() {
  var p = new Point(2, 3);
  print(p.distanceFromOrigin);
}
```

#### 重定向构造函数 Redirecting constructors

有时候, 构造函数的唯一目的是重定向到当前类的其他构造函数. 重定向构造函数没有函数体. 在`:`冒号后面调用其他构造函数

```d
class Point {
  num x, y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(num x) : this(x, 0);
}
```

#### 常量构造函数 Constant constructors

如果希望一个类创建的对象不会改变, 可以定义一个const构造函数, 并将所有实例变量都设置为final

```d
class ImmutablePoint {
  static final ImmutablePoint origin =
      const ImmutablePoint(0, 0);

  final num x, y;

  const ImmutablePoint(this.x, this.y);
}
```

#### 工厂构造函数 Factory constructors

使用`factory `关键字实现构造函数, 创建的对象不一定是当前类. 有可能返回缓存中的对象, 或者子类的对象

以下例子返回一个缓存中的对象

```d
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache =
      <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = Logger._internal(name);
      _cache[name] = logger;
      return logger;
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

> 工厂构造函数无法访问this

工厂构造函数的调用和其他构造函数一样

```d
var logger = Logger('UI');
logger.log('Button clicked');
```

#### Methods
#### Implicit interfaces
#### Extending a class
#### Enumerated types
#### Adding features to a class: mixins
#### Class variables and methods

### 泛型

#### Why use generics?
#### Using collection literals
#### Using parameterized types with constructors
#### Generic collections and the types they contain
#### Restricting the parameterized type
#### Using generic methods

### 库和可见性

#### Using libraries
#### Implementing libraries

### 异步

#### Handling Futures
#### Declaring async functions
#### Handling Streams

### Generators

### 可调用的类

### Callable classes

### Isolates

### Typedefs

### Metadata

### Comments

### Summary
