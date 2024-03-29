> 提供一个好的 toString 实现（能）使类更易于使用，使用该类的系统（也）更易于调试。

## 为什么要覆盖toString方法？
**提供一个好的 toString 实现（能）使类更易于使用，使用该类的系统（也）更易于调试**。当对象被传递给 println、printf、字符串连接操作符或断言或由调试器打印时，将自动调用 toString 方法。即使你从来没有调用 toString 对象，其他人也可能（使用）。例如，有对象引用的组件可以在日志错误消息中包含对象的字符串表示。如果你未能覆盖 toString，则该消息可能完全无用。

*如果不覆盖toString方法，那么在打印对象时，将默认表现为如下:*
```java
System.out.println("Failed to connect to " + phoneNumber);

// console => PhoneNumber@163b91

```

## 如何覆盖toString方法？
- **toString 方法应该返回对象中包含的所有重要的信息**，如果对象很大，则应该打印一个重要信息集合的摘要。
- 不论是否规定了 `toString()` 方法的格式，都需要写明注释，方便后人维护。如果以后有人需要修改该方法出现了问题则可以大胆的甩锅了。
- 抽象类应该尽可能的复写 `toString()` 方法，该类的子类可以共享公共的字符串表示形式。
