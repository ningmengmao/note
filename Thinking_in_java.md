# Thinking in JAVA

1. 对象：**`封装`** 、**`继承`**、**`多态`**

   > `has-a`  拥有、`is-a` 是一个、 `like-a` 像一个
   >
   > `is-like-a` 像是一个: 子类扩展了接口, 但是基类无法访问子类扩展的接口

   > 类与基类具有相同的类型

   > **`多态`**基于**`继承`**, 由**重写**实现

   * 高精度数字 

     > 整数:`BigInteger` 和浮点数: `BigDecimal`

   * 基本类型

     > | 基本类型 |     默认值      |
     > | :------: | :-------------: |
     > |   char   | '\u00000'(null) |
     > | boolean  |      false      |
     > |   byte   |        0        |
     > |  short   |        0        |
     > |   int    |        0        |
     > |   long   |       0L        |
     > |  float   |      0.0f       |
     > |  double  |      0.0d       |

   * 值传递和引用传递

     > 1. 对象传递引用
     >
     > 2. 基本类型传递值

   * 方法退出

     > 对于`void`类型的方法直接使用`return`退出

   * 整数除法直接去掉小数位,不会四舍五入

   * `==`比较的是地址, 使用`equals`比较对象

   * `&&`、 `||` 都是短路的，`&`、`|`是非短路的

   *  float、double转化为int时，对该数字执行截尾（舍去小数）

     > 使用Math.round()进行四舍五入转换为int

   * int可能会溢出（**编译和runtime都不报错**）

   * switch支持String类型，底层还是int，依赖HashCode实现

   * 重载：依赖方法签名区分

     > ```java
     > public double a(double db, int i) {
     >     //do the calculation here
     > }
     > ```
     >
     > 此方法的方法签名为 a(double, int)，不包括返回值

   * 普通方法和构造函数可以同名

   * finalize()方法在对象销毁前调用，但是不知道对象什么时候销毁

   ---

   * 初始化
   
     > ```java
     > int[] arr = {1, 2, 3, 4, 5};
     > String[] strs = {"abc", "def", "ghi"};
     > ```
     >
     > 