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

- Dart支持顶级函数（例如main函数）, 以及将函数绑定到类或对象中（静态方法和实例方法）。函数内部也可以创建函数（嵌套函数或本地函数）。
