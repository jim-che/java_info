## hashcode与equals

这是一个很基础的问题，但是需要答道点子上，答案来源[Java Guide](https://javaguide.cn/java/basis/java基础知识总结/#为什么要有-hashcode)

### hashcode有什么用

`hashcode()` 的作用是获取哈希码（`int`），也称为散列码。这个哈希码的作用是确定该对象在哈希表中的索引位置。

`hashcode()` 定义在 JDK 的 `Object` 类中，这就意味着 Java 中任何类都包含有 `hashCode()` 函数。另外需要注意的是：`Object` 的 `hashCode()`  方法是本地方法，也就是用 C/C++ 实现的，该方法通常用来将对象的内存地址转换为整数之后返回。

```jaav
public native int hashCode();
```

散列表存储的是键值对(key-value)，它的特点是：**能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）**

### 为什么要有hashcode

当你把对象加入 `HashSet` 时，`HashSet` 会先计算对象的 `hashCode` 值来判断对象加入的位置，同时也会与其他已经加入的对象的 `hashCode` 值作比较，如果没有相符的 `hashCode`，`HashSet` 会假设对象没有重复出现。但是如果发现有相同 `hashCode` 值的对象，这时会调用 `equals()` 方法来检查 `hashCode` 相等的对象是否真的相同。如果两者相同，`HashSet` 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。。这样我们就大大减少了 `equals` 的次数，相应就大大提高了执行速度。

hashcode 和 equals 都是为了比较两个对象是否相等。

### 为什么 JDK 要同时使用这两个方法呢？

这是因为在一些容器（比如 `HashMap`、`HashSet`）中，有了 `hashcode()` 之后，判断元素是否在对应容器中的效率会更高。

我们在前面也提到了添加元素进`HastSet`的过程，如果 `HashSet` 在对比的时候，同样的 `hashCode` 有多个对象，它会继续使用 `equals()` 来判断是否真的相同。也就是说 `hashCode` 帮助我们大大缩小了查找成本。

### 为什么不只提供hashcode呢？

因为两个对象的 `hashcode` 值相同，也不代表这两个对象相等。

再好的 `hashcode` 算法都会造成哈希冲突，越糟糕的 hash 算法越容易发生碰撞，但这也与数据域的分布特性有关。

总结下来就是：

+ 如果两个对象的 `hashcode` 值相等，那么这两个对象不一定相等（哈希碰撞）。
+ 如果两个对象的 `hashcode` 值相同并且 `equals()` 方法也是 true，那么就可以认为这两个对象是相等的。
+ 如果两个对象的 `hashcode` 值不相等，我们就可以认为这两个对象不相等。

### 为什么要重写 equals() 方法时必须重写 hashcode() 方法？

因为两个相等的对象的 `hashCode` 值必须是相等。也就是说如果 `equals` 方法判断两个对象是相等的，那这两个对象的 `hashCode` 值也要相等。

如果重写 `equals()` 时没有重写 `hashCode()` 方法的话就可能会导致 `equals` 方法判断是相等的两个对象，`hashCode` 值却不相等。