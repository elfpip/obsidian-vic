## 为什么覆盖equals方法的同时需要覆盖hashCode方法？
- 假设我们没有覆盖了equals却没有覆盖hashCode看看会发生什么
```java
public class ElevenExample {

    private Map<PhoneNumber, String> elevenCollection;

    @Before
    public void before() {
        this.elevenCollection = new HashMap<>();
    }

    @Test
    public void noOverrideHashCodeTest() {
        PhoneNumber p1 = new PhoneNumber(139, 20);
        elevenCollection.put(p1, "hello");
    }

    @After
    public void after() {
		// 没有正确的获取到"hello",这是为什么呢？
        String result = this.elevenCollection.get(new PhoneNumber(139, 20));
        System.out.println(result);
    }

    static class PhoneNumber {

        private final Integer phone;

        private final Integer areaCode;

        public PhoneNumber(Integer phone, Integer areaCode) {
            this.phone = phone;
            this.areaCode = areaCode;
        }

        @Override
        public boolean equals(Object obj) {
            if (obj == null) {
                return false;
            }
            if (!(obj instanceof PhoneNumber)) {
                return false;
            }
            PhoneNumber phoneNumber = (PhoneNumber) obj;
            return this.phone.equals(phoneNumber.phone) && this.areaCode.equals(phoneNumber.areaCode);
        }

    }
}
```
***很明显hello没有没正确的检索出来，这是为什么呢？***
```java
	// hashMap 源码
    public V get(Object key) {
        Node<K,V> e;
		// hash() 调用了Object.hashCode() 
        return (e = getNode(hash(key), key)) == null ? null : e.value;
    }

	final Node<K,V> getNode(int hash, Object key) {
        Node<K,V>[] tab; Node<K,V> first, e; int n; K k;
        if ((tab = table) != null && (n = tab.length) > 0 &&
			// 通过&的方式获取key的坐标
            (first = tab[(n - 1) & hash]) != null) {
            if (first.hash == hash && // always check first node
                ((k = first.key) == key || (key != null && key.equals(k))))
                return first;
            if ((e = first.next) != null) {
				// Tree
                if (first instanceof TreeNode)
                    return ((TreeNode<K,V>)first).getTreeNode(hash, key);
				// Link
                do {
                    if (e.hash == hash &&
                        ((k = e.key) == key || (key != null && key.equals(k))))
                        return e;
                } while ((e = e.next) != null);
            }
        }
        return null;
    }

```
> 通过源码可以看出HashMap的get方法是先依赖hashCode定位到Key的具体位置，然后再通过遍历的方式调用equals进行比较。例子中虽然我们重写了equals方法，但是每次创建的对象hashCode的值一般都是不同的，这也就导致get方法执行时错误的定位到了不同的散列桶，这也就导致我们后续的equals大概率找不到我们要的值。就算碰巧分配在同一个散列桶上，get方法也肯定会返回null，因为HashMap有一个优化，在进行equals操作之前会先判断两个对象的hashCode是否相同。

## 如何覆盖hashCode方法？
1. 返回一个固定的hashCode值
```java
// The worst possible legal hashCode implementation - never use!
@Override
public int hashCode() { return 42; }
```
> 这样虽然可以确保每个创建的对象的hashCode都相同，不会产生上诉的问题，但是这样会导致`==`操作的失效，HashMap等依赖hashCode的工具类计算时间的阶层级增长，这真是一个糟糕的设计。

2. 好的散列算法
```java
// 1. 声明一个名为 result 的 int 变量，并将其初始化为对象中第一个重要字段的散列码 c
// 2. 对象中剩余的重要字段 f，执行以下操作：为字段计算一个整数散列码 c：如果字段是基本数据类型，计算 `Type.hashCode(f)`，其中 type 是与 f 类型对应的包装类。
// 如果字段是对象引用，并且该类的 equals 方法通过递归调用 equals 方法来比较字段，则递归调用字段上的 hashCode 方法。如果需要更复杂的比较，则为该字段计算一个「canonical representation」，并在 canonical representation 上调用 hashCode 方法。如果字段的值为空，则使用 0（或其他常数，但 0 是惯用的）。如果字段是一个数组，则将其每个重要元素都视为一个单独的字段。也就是说，通过递归地应用这些规则计算每个重要元素的散列码，并将每个步骤 2.b 的值组合起来。如果数组中没有重要元素，则使用常量，最好不是 0。如果所有元素都很重要，那么使用 `Arrays.hashCode`。
// 3. 返回 result 变量。

// 例如，如果字符串散列算法中省略了乘法，那么所有的字母顺序都有相同的散列码。选择 31 是因为它是奇素数。如果是偶数，乘法运算就会溢出，信息就会丢失，因为乘法运算等同于移位。使用素数的好处不太明显，但它是传统用法。31 有一个很好的特性，可以用移位和减法来代替乘法，从而在某些体系结构上获得更好的性能：`31 * i == (i <<5) – i`。现代虚拟机自动进行这种优化。
@Override
public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}
```
3. 使用Objects的hash方法

```java
// One-line hashCode method - mediocre performance
@Override
public int hashCode() {
    return Objects.hash(lineNum, prefix, areaCode);
}
```

***TIPS***
- 如果一个类是不可变的并且计算hash的成本很高则建议缓存hash值
## 原则
- 相同的对象必须具有相同的散列码