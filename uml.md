# UML

#### 依赖关系:

依赖是两件事物之间的语义联系，其中一个事物的变化也影响到另一个事物。

![img](https://atts.w3cschool.cn/attachments/image/20170818/1503028031675933.png)

#### 协作:

一种描述一组对象之间连接的结构关系，如聚合关系（描述了整体和部分间的结构关系）；

![img](https://atts.w3cschool.cn/attachments/image/20170818/1503028037625295.png)

#### 泛化:

泛化可以被定义为一个专门的元件连接关系与一个广义的元素，它基本上描述了在对象世界中的继承关系，是一种一般化-特殊化的关系；

![img](https://atts.w3cschool.cn/attachments/image/20170818/1503028042977930.png)

####  实现:

类之间的语义关系，其中的一个类指定了由另一个类保证执行的契约。

![img](https://atts.w3cschool.cn/attachments/image/20170818/1503028048635277.png)

| **元素名称**              | **符号图例**                                                 | **含义**                                                     |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Class                     | ![img](https://images0.cnblogs.com/blog/175043/201402/082323087202856.png) | 包含类的名称、属性和方法定义。可见性修饰符：+ public-  private# protected无修饰符为 internal |
| Interface                 | ![img](https://images0.cnblogs.com/blog/175043/201402/082325012928228.png) | 接口名称通常以字母 "I" 开头。接口定义通常也用于抽象类。      |
| Note                      | ![img](https://images0.cnblogs.com/blog/175043/201402/082325350318390.png) | 任意的描述性文字。                                           |
| Package                   | ![img](https://images0.cnblogs.com/blog/175043/201402/082328381589611.png) | 将类和接口分组。                                             |
| InheritanceGeneralization | ![img](https://images0.cnblogs.com/blog/175043/201402/082337119585644.png) | B 继承自 A。B inherits from A.                               |
| Realization               | ![img](https://images0.cnblogs.com/blog/175043/201402/082337272699031.png) | B 实现了 A。B implements A.                                  |
| Association               | ![img](https://images0.cnblogs.com/blog/175043/201402/082337371241054.png) | A 和 B 相互调用和访问对方的元素。A and B call and access each other’s elements. |
| Association (one way)     | ![img](https://images0.cnblogs.com/blog/175043/201402/082337504891772.png) | A 可以调用和访问 B 的元素，但 B 不可以访问 A 的元素。A can call and access B’s elements, but not vice versa. |
| Aggregation               | ![img](https://images0.cnblogs.com/blog/175043/201402/082338012654178.png) | A 中拥有一个 B，但 B 脱离于 A 仍然可以独立存活。A has a B, and B can outlive A.A "uses" B = Aggregation : B exists independently (conceptually) from A. |
| Composition               | ![img](https://images0.cnblogs.com/blog/175043/201402/082338094143285.png) | A 中拥有一个 B，B 脱离 A 后在系统中没有任何存活的意义。A has a B, and B depends on A.A "owns" B = Composition : B has no meaning or purpose in the system without A. |
| Dependency                | ![img](https://images0.cnblogs.com/blog/175043/201402/082339218484060.png) | B 的变化会影响 A，则 A 依赖于 B。A dependency exists between two elements if changes to the definition of one element (the supplier) may cause changes to the other (the client). |