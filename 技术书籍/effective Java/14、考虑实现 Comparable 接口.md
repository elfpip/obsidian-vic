## api介绍

-   `Comparable`

该接口只有一个方法 `compareTo()` ，这个方法的作用有点类似于 `Object.equals()` ，除了简单的相等比较之外，他还允许顺序比较，一个类实现 `Comparable` 表明实例具有自然顺序。

该方法在Java中作用于非常多的地方，比如 `Arrays.sort(T t)`

> 如果一个类没有实现 Comparable 接口的话，sort方法执行到这里就会报错，sort底层也是调用对象的compareTo方法

```java

 	private static int countRunAndMakeAscending(Object[] a, int lo, int hi) {
        assert lo < hi;
        int runHi = lo + 1;
        if (runHi == hi)
            return 1;

        // Find end of run, and reverse range if descending
        if (((Comparable) a[runHi++]).compareTo(a[lo]) < 0) { // Descending
            while (runHi < hi && ((Comparable) a[runHi]).compareTo(a[runHi - 1]) < 0)
                runHi++;
            reverseRange(a, lo, runHi);
        } else {                              // Ascending
            while (runHi < hi && ((Comparable) a[runHi]).compareTo(a[runHi - 1]) >= 0)
                runHi++;
        }

        return runHi - lo;
    }

```

> 所有枚举类默认实现了这个接口,并且基于 ordinal 字段进行比较

```java

	// 此枚举常量的序号（其在枚举声明中的位置，初始常量的序号为零）。大多数程序员对这个领域毫无用处。它设计用于复杂的基于枚举的数据结构，如java。util。EnumSet和java。util。枚举图。
	private final int ordinal;

	public final int compareTo(E o) {
        Enum<?> other = (Enum<?>)o;
        Enum<E> self = this;
        if (self.getClass() != other.getClass() && // optimization
            self.getDeclaringClass() != other.getDeclaringClass())
            throw new ClassCastException();
        return self.ordinal - other.ordinal;
    }

```

> Java 库中所有的值类都实现了该接口

```java
public final class Integer extends Number implements Comparable<Integer> {}
public final class String implements java.io.Serializable, Comparable<String>, CharSequence {}

```

实现此接口的对象列表（和数组）可以按集合自动排序。排序（和array.sort）。实现此接口的对象可以用作排序映射中的键或排序集中的元素，而无需指定比较器。

-   `Comparator`

## Comparable 的约定

将一个对象与指定对象进行比较，当该对象小于、等于、大于指定对象时，对应返回一个负整数、零或正整数。如果比较的两个对象类型不同的话，则抛出 `ClassCastException`

```java
String s1 = new String();
String s2 = new String();
Integer i1 = new Integer():

- s1.compareTo(s2) 小于 = -1
- s1.compareTo(s2) 等于 = 0
- s1.compareTo(s2) 大于 = 1
- s1.compareTo(i1) ClassCastException

```

第一个规定指出，如果你颠倒两个对象引用之间的比较的方向，就应当发生这样的情况：

-   如果第一个对象小于第二个对象，那么第二个对象必须大于第一个；
-   如果第一个对象等于第二个对象，那么第二个对象一定等于第一个对象；
-   如果第一个对象大于第二个对象，那么第二个对象一定小于第一个对象。 第二个规定指出，
-   如果一个对象大于第二个，第二个大于第三个，那么第一个对象一定大于第三个对象。 最后一个规定指出，
-   所有 compareTo 结果为相等的对象分别与任何其他对象相比，必须产生相同的结果。

> 最后一个规定说明 compareTo() 需要实现和 equals() 相同的效应 如果违反这条建议，那么它的顺序就会和 equals() 不同。

```java
	BigDecimal b1 = new BigDecimal("1.0");
    BigDecimal b2 = new BigDecimal("1.00");

    System.out.println("compareTo result >>  " + b1.compareTo(b2));
    System.out.println("equals result >>  " + b1.equals(b2));

    // reuslt >> {"compareTo result >>  0", "equals result >>  false"}

```

思考：这会导致什么问题呢？

-   使用 `TreeMap` ，底层通过 `compareTo()` 来判断key是否存在，sout 结果为一个 BigDecimal 对象
-   使用 `HashSet` ，底层通过 `equals()` 来判断key是否存在，sout 结果为两个 BigDecimal 对象

## 如何编写 compareTo 方法

> 在 compareTo 方法中，字段是按顺序而不是等同性比较的，要比较对象的引用字段就要要递归的调用该引用的 compareTo 方法，如果该引用没有实现 compareTo 方法，则需要使用 Comparator 自定义比较器，或者使用现有的比较器。

```java

public class Compare implements Comparable<Compare>{

    private String name;

    private Integer order;

    private Entity entity;

    static class Entity {
        private String name;

        public String getName() {
            return name;
        }
    }

    @Override
    public int compareTo(Compare o) {

        int result = this.name.compareTo(o.name);

        if (result == 0) {
            result = this.order.compareTo(o.order);
        }

        if (result == 0) {
            // 没有比较器使用自定义比较器
            result = Comparator.comparing(Entity::getName, String::compareTo).compare(this.entity, o.entity);
        }

        return result;
    }
}

```

## Comparator 的使用

> 在上述就有一个非常典型的使用例子，Comparator接口大量的使用函数式的写法，在流式处理中大量的使用。 在 Java 8 中，Comparator 接口配备了一组比较器构造方法，可以流畅地构造比较器。然后可以使用这些比较器来实现 Comparator 接口所要求的 compareTo 方法。

```java

Comparator<Compare> comsumerCompartor = Comparator.comparing(Compare::getName)
            .thenComparing(Compare::getOrder)
            .thenComparing(compare -> compare.getEntity().getName());

```
