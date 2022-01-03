>Cloneable 接口的目的是作为 [[mixin 接口]]，用于让类来宣称它们允许克隆。

## Cloneable 接口的作用
Java规定如果一个类想要使用 `clone` 方法则必须要实现 `Cloneable` 接口，这是一个约定，如果不实现该类就对 `clone` 方法进行调用，运行时会抛出 `CloneNotSupportedException` 异常。

## 覆盖clone方法后的特点
`x.clone() != x`、`x.clone().getClass() == x.getClass()` 以及 `x.clone().equals(x)` 的值都将为 true，但都不是绝对的。

**译注：以上情况的 equals 方法应覆盖默认实现，改为比较对象中的字段才能得到 true。默认实现是比较两个引用类型的内存地址，结果必然为 false**

- `super.clone` 只是浅拷贝，重写时需要注意
- 如果想进行深拷贝，需要注意引用变量的 `clone`

## TIPS
- 不可变类永远不要提供 `clone` 方法
	- because it would merely encourage wasteful copying.