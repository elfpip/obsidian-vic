
## 第十条：复写equals时请遵守通用约定
- 什么类需要覆盖 `equals` 方法？
	- 当一个类有一个逻辑相等的概念（值类 - String、Integer...），而这个概念不同于仅判断对象的同一性（相同对象的引用），并且超类还没有覆盖`equals`方法时。
- `equals` & `==`的区别？
	- `==`比较两个对象的对象引用是否相同，未重写的`equals`和`==`保持相同的含义。而重写后的`equlas`比较的是值类的逻辑相等。
- 什么值类不需要覆盖`equals`方法？
	- 可以确保每个值最多只存在一个对象的类，枚举类就属于这一类
- 覆盖`equals`需要遵守的约定
	- 反身性：对于任何非空的参考值性x，`x.equals(x)`必须返回true
	- 对称性：对于任何非空的参考值x和y，如果`x.equals(y)` 返回true，则 `y.equals(x)` 一定返回true
	- 传递性：对于任何非空的引用值性x,y,z;如果 `x.equals(y)` 返回true，`y.equals(z)`也返回true，则 `x.equals(z)` 一定也返回true
	- 一致性：对于任何非空引用值x,y; 在不改变x,y值的情况下 ，`x.equals(y)` 的多次调用必须一致的返回相同的结果
	- 非无效性：所有对象都不能等于 `null`

***不满足这些约定的例子***
1. 不满足对称性
```java
public final class CaseInsensitieString {

	private final String str;

	public CaseInsensitieString(String _str) {
		this.str = _str;
	}

	@Override
	public boolean equals(Object source) {

		if(source instanceof CaseInsensitieString) {
			return this.str.equalsIgnoreCase(((CaseInsensitieString) source).str);
		}

		if(source instanceof String) {
			return this.str.equalsIgnoreCase((String) source);
		}

		return false;
	}
} 
```
```java
@Test
public void caseInsensitiStringEqualsTest() {
	CaseInsensitieString c1 = new CaseInsensitieString("vic");
	String c2 = new String("vic");
	// 违反对称性
	c1.equals(c2); // true
	c2.equals(c1); // false
} 

```
2. 不满足传递性
```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Point))
            return false;
        Point p = (Point)o;
        return p.x == x && p.y == y;
    }
    ... // Remainder omitted
}

public class ColorPoint extends Point {
    private final Color color;

    public ColorPoint(int x, int y, Color color) {
        super(x, y);
        this.color = color;
    }
    ... // Remainder omitted
	@Override
    public boolean equals(Object o) {
		if (!(o instanceof Point)) return false;
		if (!(o instanceof ColorPoint)) return o.equals(this);
        return super.equals(o) && ((ColorPoint) o).color.equals(this.color);
    }
}
```
```java
@Test
public void colorPointTest() {
	ColorPoint c1 = new ColorPoint(1, 2, Color.RED);
	Point c2 = new Point(1, 2);
	ColorPoint c3 = new ColorPoint(1, 2, Color.BLUE);
	// 违反传递性
	c1.equals(c2); // true
	c2.equals(c3); // true
	c1.equals(c3); // false
}
```

***构件高质量equals放的方秘诀***
1. 使用 `==` 运算符检查参数是否对该对象的引用。
2. 使用 `instanceof` 运算符检查参数是否具有正确的类型。
3. 将参数转化成正确的类型
4. 对于类中的每个 ***重要*** 字段，检查参数的字符是否与该对象的相应字段匹配。
> 对于类型不是 float 或 double 的基本类型字段，使用 == 运算符进行比较；对于对象引用字段，递归调用 equals 方法；对于 float 字段，使用 `static Float.compare(float,float)` 方法；对于 double 字段，使用 `Double.compare(double, double)`。float 和 double 字段的特殊处理是由于 `Float.NaN`、-0.0f 和类似的双重值的存在而必须的；请参阅 JLS 15.21.1 或 `Float.equals` 文档。虽然你可以将 float 和 double 字段与静态方法 Float.equals 和 Double.equals 进行比较，这将需要在每个比较上进行自动装箱，这将有较差的性能。对于数组字段，将这些指导原则应用于每个元素。如果数组字段中的每个元素都很重要，那么使用 `Arrays.equals` 方法之一。
5. 写完 equals 方法后，问自己三个问题：它具备对称性吗？具备传递性吗？具备一致性吗？
> 编写测试用例进行测试。

***TIPS***
1. 当你覆盖 `equals` 方法时，也需要覆盖 `hashCode`
> ***hashCode 的作用***
> -   如果两个对象的`hashCode` 值相等，那这两个对象不一定相等（哈希碰撞）。
> -   如果两个对象的`hashCode` 值相等并且`equals()`方法返回 `true`，我们才认为这两个对象相等。
> -   如果两个对象的`hashCode` 值不相等，我们就可以直接认为这两个对象不相等。
> ***如果不覆盖hashCode会造成什么影响呢？***
> - 如果不覆盖hashCode那么在equals相等的两个对象，可能hashCode会不同，导致在一些工具类在判断时出错，比如 `HashMap\HashSet` , `HashSet` 会导致插入两个相同的对象。
2. 不要自己重载 `equals` 方法，这会浪费很多的时间