# Java 8

[TOC]

#### 1. 概念

  *  流处理

     > 

  *  函数

     > 

  *  并行

     > 

#### 2. Lambda



> * 方法引用 `::`
>
>   > `Integer::compare`
>
> * Lambda操作符` ->`
>
>   > 1. 形参列表 -> Lambda体 (重写的抽象方法的方法体)
>   >
>   >    > Lambda体只有一条语句时 `return`和`{}`都可以省略
>   >
>   > 2. 总结
>   >
>   >    > `->` 左边    参数类型如果可以类型推断那就可以省略
>   >    >
>   >    > `->` 右边    Lambda体只有一条语句时 `return`和`{}`都可以省略
>   >    >
>   >    > 依赖函数式接口
>
> * 函数式接口  
>
>   > 只有一个方法
>   >
>   > `java.util.function` 包下是`java8`的函数式接口
>   >
>   > `@FunctionalInterface` 校验用, 可不写
>   >
>   > 内置核心函数式接口: 
>   >
>   > > |        函数式接口        | 参数类型 | 返回类型 |                             描述                             |
>   > > | :----------------------: | :------: | :------: | :----------------------------------------------------------: |
>   > > |  Consumer<T>消费型接口   |    T     |   void   |      对类型为T的对象进行操作,  方法 `void accept(T t)`       |
>   > > |  Supplier<T> 供给型接口  |    无    |    T     |               返回类型为T的对象, 方法`T get()`               |
>   > > | Function<T, R>函数型接口 |    T     |    R     |  对类型为T的对象应用操作,并返回R型结果, 方法 `R apply(T t)`  |
>   > > | Predicate<T> 断定型接口  |    T     | boolean  | 确定T对象是否满足约束,返回boolean值, 方法 `boolean test(T t)` |
>   > >
>   > > 

####  3. Stream API

>`java.util.stream`
>
>对集合, 数组进行操作
>
>> 1. Stream自己不会存储元素
>> 2. Stream不会改变源对象,只会返回一个持有结果的新Stream
>> 3. Stream操作是延迟的,这意味着他们会等到需要结果的时候才执行
>
>
>
>筛选与切片
>
>|  method  |   param   | return |         description         |
>| :------: | :-------: | :----: | :-------------------------: |
>|  filter  | Predicate |        |            过滤             |
>|  limit   |    int    |        |            限制             |
>|   skip   |    int    |        |            跳过             |
>| distinct |           |        | 通过hash(),equals()筛选去重 |
>
>映射
>
>| method  |  param   | return |       description        |
>| :-----: | :------: | :----: | :----------------------: |
>|   map   | Function |        | Function作用在每个元素上 |
>| flatMap | Function |        |                          |
>|         |          |        |                          |
>
>排序
>
>| Method |   param    | return | description |
>| :----: | :--------: | :----: | :---------: |
>| sorted |            |        |  自然排序   |
>| sorted | comparator |        |  定制排序   |
>
>终止操作
>
>> 1. 匹配和查找
>>
>>    > 
>>
>> 2. 归约
>>
>>    > | method | param             | return | description                     |
>>    > | ------ | ----------------- | ------ | ------------------------------- |
>>    > | reduce | T, BinaryOperator |        | 将流中的元素结合起来,得到一个值 |
>>    > | reduce | BinaryOperator    |        |                                 |
>>
>> 3. 收集
>>
>>    > | method  |   param    | return | description |
>>    > | :-----: | :--------: | :----: | :---------: |
>>    > | collect | Collection |        |             |
>>    >
>>    > **Collectors类**
>>    >
>>    > | method         | param | return               | description                 |
>>    > | -------------- | ----- | -------------------- | --------------------------- |
>>    > | toList         |       | `List<T>`            |                             |
>>    > | toSet          |       | `Set<T>`             |                             |
>>    > | toCollection   |       | `Collection<T>`      |                             |
>>    > | counting       |       | Long                 |                             |
>>    > | summingInt     |       | Integer              | 整型求和                    |
>>    > | averagingInt   |       | Double               | 平均值                      |
>>    > | summarizingInt |       | IntSummaryStatistics | 收集流中Integer属性的统计值 |

#### 4. `Optional<T>`类

> |   method    |        param         |  return  |       description        |
> | :---------: | :------------------: | :------: | :----------------------: |
> |     of      |          T           | Optional |   创建一个Optional实例   |
> |    empty    |                      | Optional |   创建一个空的Optional   |
> | ofNullable  |          T           | Optional |       t可以为null        |
> |  isPresent  |                      | boolean  |       是否包含对象       |
> |  ifPresent  | Consumer<? super T>  |   void   | 如果有值，就执行Consumer |
> |     get     |                      |    T     |                          |
> |   ofElse    |       T other        |    T     |   如果为空则返回other    |
> |  orElseGet  | Supplier<? extend T> |    T     |                          |
> | orElseThrow | Supplier<? extend X> |    T     |         返回异常         |
>
> 



