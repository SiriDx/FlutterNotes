## Dart基础

#### 简单的Dart程序

```d
// 定义个方法。
printNumber(num aNumber) {
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
