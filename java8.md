# Java 8

[TOC]

#### 1. 概念

  *  流处理

      

  *  函数

      

  *  并行

      

#### 2. Lambda



 * 方法引用 `::`

    `Integer::compare`

 * Lambda操作符` -`

    1. 形参列表 - Lambda体 (重写的抽象方法的方法体)
    
        Lambda体只有一条语句时 `return`和`{}`都可以省略
      
    2. 总结
    
        `->` 左边    参数类型如果可以类型推断那就可以省略
       
        `->` 右边    Lambda体只有一条语句时 `return`和`{}`都可以省略
       
        依赖函数式接口

 * 函数式接口  

    只有一个方法
   
    `java.util.function` 包下是`java8`的函数式接口
   
    `@FunctionalInterface` 校验用, 可不写
   
    内置核心函数式接口: 
   
    |        函数式接口        | 参数类型 | 返回类型 |                             描述                             |
    | :----------------------: | :------: | :------: | :----------------------------------------------------------: |
    |  Consumer<T消费型接口   |    T     |   void   |      对类型为T的对象进行操作,  方法 `void accept(T t)`       |
    |  Supplier<T 供给型接口  |    无    |    T     |               返回类型为T的对象, 方法`T get()`               |
    | Function<T, R函数型接口 |    T     |    R     |  对类型为T的对象应用操作,并返回R型结果, 方法 `R apply(T t)`  |
    | Predicate<T 断定型接口  |    T     | boolean  | 确定T对象是否满足约束,返回boolean值, 方法 `boolean test(T t)` |
    
     

####  3. Stream API

`java.util.stream`

对集合, 数组进行操作

 1. Stream自己不会存储元素
 2. Stream不会改变源对象,只会返回一个持有结果的新Stream
 3. Stream操作是延迟的,这意味着他们会等到需要结果的时候才执行



筛选与切片

|  method  |   param   | return |         description         |
| :------: | :-------: | :----: | :-------------------------: |
|  filter  | Predicate |        |            过滤             |
|  limit   |    int    |        |            限制             |
|   skip   |    int    |        |            跳过             |
| distinct |           |        | 通过hash(),equals()筛选去重 |

映射

| method  |  param   | return |                       description                       |
| :-----: | :------: | :----: | :-----------------------------------------------------: |
|   map   | Function |        |                Function作用在每个元素上                 |
| flatMap | Function |        | 把一个流中的每个值都换成另一个流,再把所有流合并为一个流 |
|         |          |        |                                                         |

排序

| Method |   param    | return | description |
| :----: | :--------: | :----: | :---------: |
| sorted |            |        |  自然排序   |
| sorted | comparator |        |  定制排序   |

终止操作

 1. 匹配和查找

     | method    | param | return | description |
     | --------- | ----- | ------ | ----------- |
     | anyMatch  |       |        |             |
     | allMatch  |       |        |             |
     | noneMatch |       |        |             |
     | findFrist |       |        |             |
     | findAny   |       |        |             |
    
     

 2. 归约

     | method | param             | return | description                     |
     | ------ | ----------------- | ------ | ------------------------------- |
     | reduce | T, BinaryOperator |        | 将流中的元素结合起来,得到一个值 |
     | reduce | BinaryOperator    |        |                                 |

 3. 收集

     | method  |   param    | return | description |
     | :-----: | :--------: | :----: | :---------: |
     | collect | Collection |        |             |
    
     **Collectors类**
     
     | method         | param | return               | description                 |
     | -------------- | ----- | -------------------- | --------------------------- |
     | toList         |       | `List<T`            |                             |
     | toSet          |       | `Set<T`             |                             |
     | toCollection   |       | `Collection<T`      |                             |
     | counting       |       | Long                 | 统计 |
     | summingInt     |       | Integer              | 整型求和                    |
     | averagingInt   |       | Double               | 平均值                      |
     | summarizingInt |       | IntSummaryStatistics | 收集流中Integer属性的统计值 |
     | joining | | String | 对每个元素调用toString() |
     | maxBy | | Optional<T> | 流里的最大值 |
     | minBy | | Optional<T> | 流里的最小值 |
     | reducing | | 归约操作产生的类型 | 使用BinaryOperator归约为单个值 |
     | collectionAndThen | | 转换函数返回类型 | 包裹另外一个收集器,对其结果进行转换 |
     | groupingBy | | Map<K, List<T>> |  |
     | partitioningBy | | Map<Boolean, List<T>> |  |
     |  | |  |  |
     |  | |  |  |
     |  | |  |  |

##### 创建流

1. 由值创建流

   Stream.of(1,2,3,4,5)

2. 由数组创建流

   ```java
   int[] numbers = {2,3,4,5,6};
   int sum = Arrays.stream(numbers).sum();
   ```

3. 由文件生成流

   Files.lines() 返回一个文件流

   BufferedReader.lines() 返回文件流

   ```java
   Stream<String> stream = Files.lines(Paths.get("result.txt"), StandardCharsets.UTF_8);
   ```

4. 由函数生成流

   ```java
       public static<T> Stream<T> iterate(final T seed, final UnaryOperator<T> f) ;
       public static<T> Stream<T> iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> next) ;
       public static<T> Stream<T> generate(Supplier<? extends T> s) ;
   ```


##### 并行

1. 将顺序流转化为并行流

   ```java
   list.stream().limit(100).parallel().map(String::length).reduce(0, Long::sum);
   ```

   parallelStream()或parallel()

2. 将并行流转化为顺序流

   ```java
   list.stream().limit(100).parallel().map(String::length).sequential().limit(10).collect(toList());
   ```

   sequential()

##### 分支-合并算法

继承RecursiveTask, 实现compute() 方法

使用ForkJoinPool.invoke(RecursiveTask)执行

```java
public class ForkJoinSumCalculator extends RecursiveTask<Long> {
    private final long[] numbers;
    private final int start;
    private final int end;

    // 分解的最小数组数量
    private static final long THRESHOLD = 1_000;

    public ForkJoinSumCalculator(long[] numbers){
        this(numbers, 0, numbers.length);
    }

    private ForkJoinSumCalculator(long[] numbers, int start, int end){
        this.numbers = numbers;
        this.start = start;
        this.end = end;
    }

    @Override
    protected Long compute() {
        int length = end - start;

        // 不再分解
        if (length <= THRESHOLD){
            return computeSequentially();
        }
        ForkJoinSumCalculator leftTask = new ForkJoinSumCalculator(numbers, start, start + length / 2);

        // 异步执行
        leftTask.fork();
        ForkJoinSumCalculator rightTask = new ForkJoinSumCalculator(numbers, start + length / 2, end);

        // 同步执行第二个子任务, 递归调用
        Long rightResult = rightTask.compute();

        // 等待 leftTask执行完成
        Long leftResult = leftTask.join();

        return leftResult + rightResult;
    }

    private long computeSequentially(){
        long sum = 0;
        for (int i = start; i < end; i++) {
            sum += numbers[i];
        }
        return sum;
    }

    public static void main(String[] args) {
        long[] longs = LongStream.range(0, 100L * 1000L * 1000L).toArray();
        ForkJoinSumCalculator forkJoinSumCalculator = new ForkJoinSumCalculator(longs);
        Long aLong = new ForkJoinPool().invoke(forkJoinSumCalculator);
        System.out.println(aLong);
    }
}
```



1. 对一个任务调用join方法会阻塞调用方, 直到该任务做出结果. 要在两个子任务的计算都开始后再调用它.
2. 不应该在RecursiveTask内部调用ForkJoinPool的invoke方法, 应该始终直接调用computer或者fork方法.



##### 继承与多接口

如果一个类使用的相同的函数签名从多个地方(比如另外一个类或者接口)继承了方法, 通过三条规则进行判断

1. 类中的方法优先级最高. 类中或父类中的方法优先级高于任何声明为默认方法的优先级

2. 如果无法通过1进行判断,那么子接口的优先级高于任何声明为默认方法的优先级

3. 最好还是无法判断,那么必须通过显式的覆盖和调用期望方法, **显式的选择使用哪一个默认方法的实现**

   ```java
   public interface A {
       default void hello(){
           System.out.println("hello from A");
       }
   }
   
   public interface B {
       default void hello(){
           System.out.println("hello from A");
       }
   }
   
   public class C implements B, A {
   	void hello(){
           B.super.hello();   // 显式的调用B的hello方法
       }
   }
   ```

   

#### 4. `Optional<T`类

|   method    |        param         |  return  |       description        |
| :---------: | :------------------: | :------: | :----------------------: |
|     of      |          T           | Optional |   创建一个Optional实例   |
|    empty    |                      | Optional |   创建一个空的Optional   |
| ofNullable  |          T           | Optional |       t可以为null        |
|  isPresent  |                      | boolean  |       是否包含对象       |
|  ifPresent  | Consumer<? super T  |   void   | 如果有值，就执行Consumer |
|     get     |                      |    T     |                          |
|   ofElse    |       T other        |    T     |   如果为空则返回other    |
|  orElseGet  | Supplier<? extend T |    T     |                          |
| orElseThrow | Supplier<? extend X |    T     |         返回异常         |

 



