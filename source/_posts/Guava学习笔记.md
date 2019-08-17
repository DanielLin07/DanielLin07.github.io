---
title: Guava学习笔记
date: 2019-07-02 20:14:44
tags:
  - Java
  - Guava
categories: Java笔记
description: 有趣的Java库「番石榴」的学习记录。
photos:
  - "https://i.loli.net/2019/07/01/5d1a15df42d4730438.jpg"
---

# Guava概述

[Guava](https://github.com/google/guava)是Google开源的一个Java库，Google最早提出它，是对Java Collection进行扩展以提高开发效率。随着时间推移，Guava已经覆盖到了Java开发的方方面面，包括提供用于集合，缓存，I/O，并发等。截止到28.0-jre版本，Guava的基本结构如图所示：

![Guava结构](https://i.loli.net/2019/07/02/5d1b77942cf2d83205.jpg)

本笔记记录了个人的Guava学习重点总结，主要面向的Java版本是JDK1.7与JDK1.8，针对其他版本的特性暂不作记录，更多完整的内容可以参考Guava的[中文指导](https://ifeve.com/google-guava/)、[英文指导](https://github.com/google/guava/wiki)与[Api文档](https://guava.dev/releases/snapshot-jre/api/docs/)。

# 1 基本工具「Basic utilities」

## 1.1 Optional

Guava用`Optional<T>`表示可能为`null`的`T`类型引用。一个`Optional`实例可能包含非`null`的引用（我们称之为引用存在），也可能什么也不包括（称之为引用缺失）。

使用`Optional`除了赋予`null`语义，增加了可读性，最大的优点在于它是一种傻瓜式的防护。`Optional`迫使你积极思考引用缺失的情况，因为你必须显式地从`Optional`获取引用。

### Optional的常用Api

```Java
/**
 * Optional的常用Api
 */
public void baseOptional() {

    // 创建指定引用的Optional实例，若引用为null则快速失败
    Optional<Integer> possible = Optional.of(5);
    // 返回true
    possible.isPresent();
    // 返回5
    possible.get();

    // 抛出 NullPointerException
    // possible = Optional.of(null);

    // 创建引用缺失的Optional实例
    possible = Optional.absent();
    // 抛出 IllegalStateException
    // possible.get();
    // 返回5
    possible.or(5);
    // 返回null
    possible.orNull();

    possible = Optional.fromNullable(5);
    // 返回5
    possible.get();

    // 创建引用缺失的Optional实例
    possible = Optional.fromNullable(null);
    // 抛出 IllegalStateException
    // possible.get();
}
```

在JDK1.8中已经提供了功能相同的`java.util.Optional`类。

## 1.2 Preconditions

前置条件：让方法调用的前置条件判断更简单。

### Preconditions的常用Api

```Java
/**
 * Preconditions的常用Api
 */
public void basePreconditions(Integer param1, int[] param2) {

    /*
     * 检查boolean是否为true，用来检查传递给方法的参数
     * 检查失败时抛出 IllegalArgumentException
     */
    Preconditions.checkArgument(param1 != null);

    /*
     * 检查value是否为null，该方法直接返回value，因此可以内嵌使用checkNotNull
     * 检查失败时抛出 NullPointerException
     */
    Integer index = Preconditions.checkNotNull(param1);

    /*
     * 用来检查对象的某些状态
     * 检查失败时抛出 IllegalStateException
     */
    Preconditions.checkState(param1 == 1);

    /*
     * 检查index作为索引值对某个列表、字符串或数组是否有效。index >= 0 && index < size
     * 检查失败时抛出 IndexOutOfBoundsException
     * 索引值常用来查找列表、字符串或数组中的元素，如List.get(int), String.charAt(int)
     */
    Preconditions.checkElementIndex(index, param2.length);

    /*
     * 检查index作为位置值对某个列表、字符串或数组是否有效。index >= 0 && index <= size
     * 检查失败时抛出 IndexOutOfBoundsException
     * 位置值和位置范围常用来截取列表、字符串或数组，如List.subList(int，int), String.substring(int)
     */
    Preconditions.checkPositionIndex(index, param2.length);

    /*
     * 检查[start, end]表示的位置范围对某个列表、字符串或数组是否有效
     * 检查失败时抛出 IllegalStateException
     */
    Preconditions.checkPositionIndexes(0, 5, param2.length);
}
```

## 1.3 Ordering

Ordering是Guava链式风格比较器Comparator的实现，它可以用来为构建复杂的比较器，以完成集合排序的功能。

从实现上说，Ordering实例就是一个特殊的Comparator实例。Ordering把很多基于Comparator的静态方法包装为自己的实例方法，并且提供了链式调用方法，来定制和增强现有的比较器。

### Ordering的常用Api
```Java
/**
 * Ordering的常用Api
 */
public static void baseOrdering() {

    // 对可排序类型做自然排序，如数字按大小，日期按先后排序
    Ordering<String> naturalOrdering = natural();
    // 按对象的字符串形式做字典排序
    Ordering<Object> usingToStringOrdering = usingToString();
    // 把给定的Comparator转化为排序器
    Ordering<String> fromOrdering = Ordering.from(new Comparator<String>() {
        public int compare(String o1, String o2) {
            return o1.hashCode() - o2.hashCode() ;
        }
    });
    // 直接继承Ordering
    Ordering<String> byLengthOrdering = new Ordering<String>() {
        public int compare(String left, String right) {
            return Ints.compare(left.length(), right.length());
        }
    };

    List<String> withNullList = Lists.newArrayList(list);
    withNullList.add(null);
    System.out.println("withNullList:"+ withNullList);

    List<List> listList = Lists.newArrayList();
    listList.add(Lists.newArrayList("perter", "jerry"));
    listList.add(Lists.newArrayList("perter"));
    listList.add(Lists.newArrayList("perter", "jerry", null));
    System.out.println("listList:"+ withNullList);

    // 对可排序类型做自然排序，如数字按大小，日期按先后排序
    Ordering<String> naturalOrdering = natural();
    // 获取语义相反的排序器
    Ordering<String> reverseOrdering = natural().reverse();
    // 使用当前排序器，但额外把null值排到最前面
    Ordering<String> nullsFirst = natural().nullsFirst();
    // 使用当前排序器，但额外把null值排到最后面
    Ordering<String> nullsLast = natural().nullsLast();
    // 合成另一个比较器，以处理当前排序器中的相等情况
    Ordering<People> secondaryOrdering = new PeopleAgeOrder().compound(new PeopleNameLengthOrder());
    // 返回该类型的可迭代对象Iterable<T>的排序器
    Ordering lexicographicalOrdering = naturalOrdering.lexicographical();
    // 对集合中元素调用Function，再按返回值用当前排序器排序
    Ordering<String> resultOfOrdering = natural().nullsFirst().onResultOf(new Function<String, Integer>() {
        public Integer apply(String input) {
            return input == null ? null : input.length();
        }
    });
}
```

## 1.4 Objects

Objects与JDK1.7中提供的方法类似，不再赘述。

## 1.5 Throwables

Throwables与JDK1.7中提供的多重catch功能类似，不再赘述。

# 2 集合「Collections」

## 2.1 不可变集合

不可变集合因为不可变性，所以可以作为常量，且在被多个线程调用时，不存在竞态条件问题。

### 不可变集合的常用Api

```Java
/**
 * 不可变集合常用Api
 */
public void baseImmutable() {
    Set<String> colors = Sets.newHashSet("red", "orange", "yellow");
    Set<Color> colorSet = Sets.newHashSet(new Color(0, 0, 0));

    // copyOf方法
    ImmutableSet.copyOf(colors);

    // of方法
    ImmutableSet.of(colors);
    ImmutableSet.of("red", "orange", "yellow");
    ImmutableMap.of("a", 1, "b", 2);

    // Builder工具
    ImmutableSet<Color> GOOGLE_COLORS = ImmutableSet.<Color>builder()
            .addAll(colorSet)
            .add(new Color(0, 191, 255))
            .build();

    ImmutableSet<String> colorList = ImmutableSet.of("red", "orange", "yellow");

    // ImmutableXXX.copyOf方法会尝试在安全的时候避免做拷贝
    // 在这段代码中，ImmutableList.copyOf(colorList)会智能地直接返回colorList.asList()
    // 它是一个ImmutableSet的常量时间复杂度的List视图
    ImmutableList.copyOf(colorList);

    // 所有不可变集合都有一个asList()方法提供ImmutableList视图
    ImmutableList immutableList = colorList.asList();
}
```

## 2.2 新集合类型

Guava引入了很多JDK没有但是非常有用的新集合类型。

### 2.2.1 Multiset

Multiset允许元素重复，元素之间没有顺序。
我们经常碰到一类统计需求——统计某个对象（常见的如字符串）在一个集合中的出现次数，使用JDK提供的数据结构，我们会这么做：

```Java
Map<String, Integer> counts = new HashMap<String, Integer>();
for (String word : words) {
    Integer count = counts.get(word);
    if (count == null) {
        counts.put(word, 1);
    } else {
        counts.put(word, count + 1);
    }
}
```

Guava提供了一个新集合类型 Multiset，它可以多次添加相等的元素，使用Guava改进代码如下：

```Java
Multiset<String> multiset = HashMultiset.create();

multiset.add("a");
multiset.add("a");
// 添加"b"元素5次
multiset.add("b", 5);

// 返回[a, b]
multiset.elementSet();
// 返回2
multiset.count("a");
// 返回5
multiset.count("b");
// 返回0
multiset.count("c");
```

### 2.2.2 Multimap

Multimap可以把键映射到任意多个值上。
在JDK提供的集合元素中，我们经常会使用Map<K, List<V>>以及Map<K, Set<V>>用来表达某一个Key对应的元素是一个集合，在Guava中，使用MultiMap可以达到这样的效果：

```Java
Multimap<String, Integer> multiMap = ArrayListMultimap.create();
multiMap.put("a", 1);
multiMap.put("a", 2);
multiMap.put("a", 4);
multiMap.put("b", 3);
multiMap.put("c", 5);

// [a x 3, b, c]
multiMap.keys();
// [a, b, c]
multiMap.keySet();
// [1 ,2, 4]
multiMap.get("a");
// [3]
multiMap.get("b");
// [5]
multiMap.get("c");
// []
multiMap.get("d");

// {a=[1, 2, 4], b=[3], c=[5]}
multiMap.asMap();
```

### 2.2.3 Table

`Table`带有两个支持所有类型的键：”行”和”列”。
我们有时候会用到这样一种数据结构：`Map<R, Map<C, V>>`，在Guava中，可以使用`Table`来实现：

```Java
Table<Integer, Integer, Double> weightedGraph = HashBasedTable.create();
weightedGraph.put(1, 2, 4d);
weightedGraph.put(1, 3, 20d);
weightedGraph.put(2, 3, 5d);
// 返回{1={2=4.0, 3=20.0}, 2={3=5.0}}
System.out.println(weightedGraph);

// 用Map<R, Map<C, V>>表现Table<R, C, V>
Map<Integer, Map<Integer, Double>> map = weightedGraph.rowMap();
// 返回{1={2=4.0, 3=20.0}, 2={3=5.0}}
System.out.println(map);

// 返回”行”的集合Set<R>
Set<Integer> set = weightedGraph.rowKeySet();
// 返回[1, 2]
System.out.println(set);

// 用Map<C, V>返回给定”行”的所有列
// 类似的列访问方法：columnMap()、columnKeySet()、column(c)
// （基于列的访问会比基于的行访问稍微低效点）
Map<Integer, Double> row = weightedGraph.row(2);
// 返回[1, 2]
System.out.println(set);
// 对这个map进行的写操作也将写入Table中
row.put(4, 6d);
row.put(5, 8d);
// 返回{1={2=4.0, 3=20.0}, 2={3=5.0, 4=6.0, 5=8.0}}
System.out.println(weightedGraph);

// 用元素类型为Table.Cell<R, C, V>的Set表现Table<R, C, V>
// Cell类似于Map.Entry，但它是用行和列两个键区分的
Set<Table.Cell<Integer, Integer, Double>> cells = weightedGraph.cellSet();
// 返回[(1,2)=4.0, (1,3)=20.0, (2,3)=5.0, (2,4)=6.0, (2,5)=8.0]
System.out.println(cells);
```

其他更多新集合类型见Guava的中文指导：新集合类型。

## 2.3 集合扩展增强

Guava引入了很多JDK没有但是非常有用的新集合类型。

### 2.3.1 静态创建方法

通过使用Guava提供的静态创建方法，可以简化集合创建代码的编写。
在JDK提供的集合中，我们初始化一个集合，需要这么做：

```Java
List<String> list = new ArrayList<>();
list.add("a");
list.add("b");
list.add("c");
list.add("d");
```

而在Guava中，可以轻松使用一行代码实现：

```Java
List<String> list = Lists.newArrayList("a", "b", "c", "d");
```
同理，创建其他集合，也可以用这种方式实现。

#### 2.3.2 集合工具类

Gauva提供了Lists、Sets、Maps、Multisets等工具类，方便对集合进行操作。
以下是常用Api示例：

```Java
List countUp = Ints.asList(1, 2, 3, 4, 5);
// 反转列表顺序
List countDown = Lists.reverse(countUp);
// {5, 4, 3, 2, 1}
System.out.println(countDown);

Set<String> wordsWithPrimeLength = ImmutableSet.of("one", "two", "three", "six", "seven", "eight");
Set<String> primes = ImmutableSet.of("two", "three", "five", "seven");

// 交集运算
Sets.SetView<String> intersection = Sets.intersection(primes, wordsWithPrimeLength);
// [two, three, seven]
System.out.println(intersection);

// 并集运算
Sets.SetView<String> union = Sets.union(primes, wordsWithPrimeLength);
// [two, three, five, seven, one, six, eight]
System.out.println(union);

// 差集运算
Sets.SetView<String> difference = Sets.difference(wordsWithPrimeLength, primes);
Sets.SetView<String> difference2 = Sets.difference(primes, wordsWithPrimeLength);
// [one, six, eight]
System.out.println(difference);
// [five]
System.out.println(difference2);

// difference用来比较两个Map以获取所有不同点
Map<String, Integer> left = ImmutableMap.of("a", 1, "b", 2, "c", 3);
Map<String, Integer> right = ImmutableMap.of("b", 2, "c", 4, "d", 5);
MapDifference<String, Integer> diff = Maps.difference(left, right);

// 两个Map中都有的映射项，包括匹配的键与值
Map<String, Integer> entriesInCommon = diff.entriesInCommon();
// {b=2}
System.out.println(entriesInCommon);

// 键相同但是值不同值映射项
Map<String, MapDifference.ValueDifference<Integer>> entriesDiffering = diff.entriesDiffering();
// {c=(3, 4)}
System.out.println(entriesDiffering);
```

### 2.3.3 扩展工具类

让实现和扩展集合类变得更容易，比如创建Collection的装饰器，或实现迭代器。这一部分目前用的不是很多，暂时不作记录。

# 3 图表「Graphs」

用的不多，暂不记录。

# 4 缓存「Caches」

Guava提供本地缓存，本地缓存作用就是提高系统的运行速度，是一种空间换时间的取舍。它实质上是一个做key-value查询的字典，但是相对于常用的HashMap它又有以下的不同点：

- 并发性；由于目前的应用大都是多线程的，所以缓存需要支持并发的写入，这点在ConcurrentHashMap上一致的。
- 过期策略；在某些场景中，我们可能会希望缓存的数据有一定“保质期”，过期策略可以固定时间，例如缓存写入10分钟后过期。也可以是相对时间，例如10分钟内未访问则使缓存过期（类似于servlet中的session）。在java中甚至可以使用软引用，弱引用的过期策略。
- 淘汰策略；由于本地缓存是存放在内存中，我们往往需要设置一个容量上限和淘汰策略来防止出现内存溢出的情况。

Guava的Caches使用的是LRU（Least Recently Used）过期策略，即淘汰最后一次使用时间离当前最久的缓存数据，保留最近访问的数据。

## 4.1 缓存的创建

Cache与LoadingCache是Guava提供的两种构建本地缓存的方式。

### 4.1.1 Cache

Cache通过CacheBuilder的build()方法构建，当缓存不存在时，则通过Callable进行加载并返回，该操作是原子操作。

```Java
Cache<String, String> cache = CacheBuilder.newBuilder()
        // 设置缓存的最大容量
        .maximumSize(100)
        // 设置缓存在写入一分钟后失效
        .expireAfterWrite(1, TimeUnit.MINUTES)
        // 设置并发级别为10
        .concurrencyLevel(10)
        // 开启缓存统计
        .recordStats()
        .build();

// 放入缓存
cache.put("key1", "abc");
// 获取缓存
String cacheValue1 = cache.getIfPresent("key1");
// get方法需要抛出异常
String cacheValue2 = cache.get("key2", () -> "def");
// 返回abc
System.out.println(cacheValue1);
// 返回cde
System.out.println(cacheValue2);
```

### 4.1.2 LoadingCache

LoadingCache通过CacheBuilder的build(CacheLoader<? super K1, V1> loader)方法构建：

```Java
LoadingCache<String, String> loadingCache = CacheBuilder.newBuilder()
        // 设置缓存在写入10分钟后，通过CacheLoader的load方法进行刷新
        .refreshAfterWrite(1, TimeUnit.SECONDS)
        // JDK8以后可以使用 Duration
        // .refreshAfterWrite(Duration.ofMinutes(10))
        .build(new CacheLoader<String, String>() {
            @Override
            public String load(String key) throws Exception {
                // 缓存加载逻辑
                return "def";
            }
        });

loadingCache.put("key1", "abc");
// 获取缓存，当缓存不存在时，会通过CacheLoader自动加载，该方法会抛出ExecutionException异常
String loadingCacheValue1 = loadingCache.get("key1");
// 返回abc
System.out.println(loadingCacheValue1);
// 以不安全的方式获取缓存，当缓存不存在时，会通过CacheLoader自动加载，该方法不会抛出异常
String loadingCacheValue2 = loadingCache.getUnchecked("key2");
// 返回def
System.out.println(loadingCacheValue2);
```

## 4.2 缓存的参数设置

### 4.2.1 缓存的并发级别

设置并发级别，使得缓存支持并发的写入和读取。

```Java
CacheBuilder.newBuilder()
        // 设置并发级别为cpu核心数
        .concurrencyLevel(Runtime.getRuntime().availableProcessors())
        .build();
```

### 4.2.2 缓存的初始容量

设置一个合理大小初始容量，减少缓存的扩容次数。

```Java
CacheBuilder.newBuilder()
        // 设置初始容量为100
        .initialCapacity(100)
        .build();
```

### 4.2.3 缓存的回收

制定缓存的回收策略，防止内存溢出。

基于数量回收：

```Java
CacheBuilder.newBuilder()
        // 缓存数量上限为100
        .maximumSize(100)
        .build();
```

基于容量回收：

```Java
CacheBuilder.newBuilder()
        // 设置最大容量为 1M
        .maximumWeight(1024 * 1024)
        // 设置用来计算缓存容量的Weigher
        .weigher((String key, String value) -> key.getBytes().length + value.getBytes().length)
        .build();
```

基于引用回收：

```Java
CacheBuilder.newBuilder()
        // 使用弱引用存储键。当键没有其它（强或软）引用时，该缓存可能会被回收
        .weakKeys()
        // 使用弱引用存储值。当值没有其它（强或软）引用时，该缓存可能会被回收
        .weakValues()
        // 使用软引用存储值。当内存不足并且该值其它强引用引用时，该缓存就会被回收
        .softValues()
        .build();
```

显示回收：

```Java
// 构建一个缓存
Cache<String, String> cache = CacheBuilder.newBuilder().build();
// 回收key为k1的缓存
cache.invalidate("k1");
// 批量回收key为k1、k2的缓存
List<String> needInvalidateKeys = new ArrayList<>();
needInvalidateKeys.add("k1");
needInvalidateKeys.add("k2");
cache.invalidateAll(needInvalidateKeys);
// 回收所有缓存
cache.invalidateAll();
```

### 4.2.4 缓存的过期策略

通过为缓存设置过期策略以为缓存数据提供保质期。

固定时间过期：

```Java
CacheBuilder.newBuilder()
        // 写入10分钟后过期
        .expireAfterWrite(10, TimeUnit.MINUTES)
        .build();

// JDK8后可以使用Duration设置
CacheBuilder.newBuilder()
        .expireAfterWrite(Duration.ofMinutes(10))
        .build();
```

相对时间过期：

```Java
CacheBuilder.newBuilder()
        // 在10分钟内未访问则过期
        .expireAfterAccess(10, TimeUnit.MINUTES)
        .build();

// java8后可以使用Duration设置
CacheBuilder.newBuilder()
        .expireAfterAccess(Duration.ofMinutes(10))
        .build();
```

### 4.2.5 缓存的刷新

Guava目前支持的刷新方式包括定时刷新与显示刷新。

定时刷新：

```Java
// 目前只有CacheLoader支持定时刷新
CacheBuilder.newBuilder()
        // 设置缓存在写入10分钟后，通过CacheLoader的load方法进行刷新
        .refreshAfterWrite(10, TimeUnit.SECONDS)
        // JDK8以后可以使用 Duration
        // .refreshAfterWrite(Duration.ofMinutes(10))
        .build(new CacheLoader<String, String>() {
            @Override
            public String load(String key) throws Exception {
                // 缓存加载逻辑
                return String.valueOf(Math.random());
            }
        });
```

显示刷新：

```Java
// 构建一个缓存
Cache<String, String> refreshCache = CacheBuilder.newBuilder().build();
// 使用put进行覆盖刷新
refreshCache.put("k1", "v1");
// 使用Map的put方法进行覆盖刷新
refreshCache.asMap().put("k1", "v1");
// 使用Map的putAll方法进行批量覆盖刷新
Map<String,String> needRefresh = new HashMap<>();
needRefresh.put("k1", "v1");
refreshCache.asMap().putAll(needRefresh);
// 使用ConcurrentMap的replace方法进行覆盖刷新
refreshCache.asMap().replace("k1", "v1");

// loadingCache在进行刷新时无需显式的传入value，它会自动调用一次实现的load()方法
loadingCache.refresh("k1");
```

# 5 函数式编程「Functional idioms」

替换使用JDK1.8的Lambda表达式，暂不记录。

# 6 并发「Concurrency」

暂空。

# 7 字符串处理「Strings」

## 7.1 Joiner

Joiner用于简化字符串的连接工作。

```Java
Joiner joiner = Joiner.on(",").skipNulls();
// 返回Harry,Ron,Hermione
System.out.println(joiner.join("Harry", null, "Ron", "Hermione"));
// 返回1,5,7
System.out.println(Joiner.on(",").join(Arrays.asList(1, 5, 7)));
```

## 7.2 Splitter

```Java
// 返回Harry,Ron,Hermione
Iterable<String> strSplits = Splitter.on(",")
        // 移除结果字符串的前导空白和尾部空白，指定参数则移除匹配字符
        .trimResults()
        // 从结果中自动忽略空字符串
        .omitEmptyStrings()
        // 限制拆分出的字符串数量
        // .limits(10)
        .split("Harry,Ron,,   Hermione");
```

## 7.3 CharMatcher

CharMatcher是为了匹配与处理这些匹配的字符。

# 8 基本类型「Primitives」

Guava的Primitives提供了许多int、double等基本类型的工具类。

Primitives常用Api:

```Java
// 返回[1, 2, 3, 4]
System.out.println(Ints.asList(1, 2, 3, 4));
// 返回-1，与JDK1.7后也提供了Integer.compare(x, y)方法
System.out.println(Ints.compare(1, 2));
// 返回字符串"1 2 3 4"
System.out.println(Ints.join(" ", 1, 2, 3, 4));
// 返回最大值6
System.out.println(Ints.max(1, 3, 5, 4, 6));
// 尝试对字符串转换成int类型，如果超过了int的取值范围，则返回null
System.out.println(Ints.tryParse("123412312"));
// 尝试将long转换成int类型，如果超过了int的取值范围，则抛出IllegalArgumentException异常
System.out.println(Ints.checkedCast(12314L));
```

# 9 区间「Ranges」

Guava提供的Ranges是一个可以比较类型的区间Api。

构建Ranges:

```Java
// 返回(1, 5)
Range.open(1, 5);
// 返回[1, 5]
Range.closed(1, 5);
// 返回(1, 5]
Range.closedOpen(1, 5);
// 返回(1, +∞)
Range.greaterThan(1);
// 返回[1, +∞)
Range.atLeast(1);
// 返回(-∞, 1)
Range.lessThan(1);
// 返回(-∞, 1]
Range.atMost(1);
// 返回[-∞, +∞)
Range.all();
```

Ranges常用Api:

```Java
// 返回false，查询(4, 5]是否为空
Range.closedOpen(4, 5).isEmpty();
// 返回3，查询区间的下限
Range.closed(3, 10).lowerEndpoint();
// 返回true，(1, 10]包含(3, 7)
Range.closedOpen(1, 10).encloses(Range.closed(3, 7));
// 返回true，判断两个区间是否是连续的（两个区间的并集是连续集合的形式）
Range.closedOpen(0, 5).isConnected(Range.openClosed(5, 10));
// 返回[3, 4]，取两个区间的交级
Range.closed(0, 9).intersection(Range.closed(3, 4));
// 返回[3, 10)，同时包含两个区间的最小区间
Range.closed(3, 5).span(Range.open(5, 10));
```

# 10 I/O

JDK7开始已有提供功能类似的工具类，暂略。

# 11 哈希「Hash」

Guava的Hash是对JDK提供的Hash的增强，通常运用于简单散列表以外的散列运用。

构建HashCode:

```Java
HashFunction hf = Hashing.md5();
HashCode hc = hf.newHasher()
        .putLong(1234)
        .putString("daniel", Charsets.UTF_8)
        .hash();
```

布隆过滤器:

```Java
class Person {
  final int id;
  final String firstName;
  final String lastName;
  final int birthYear;
}

Funnel<Person> personFunnel = new Funnel<Person>() {
  @Override
  public void funnel(Person person, PrimitiveSink into) {
    into
        .putInt(person.id)
        .putString(person.firstName, Charsets.UTF_8)
        .putString(person.lastName, Charsets.UTF_8)
        .putInt(birthYear);
  }
};

// 第二个参数是预计插入数量，第三个参数是允许的误差概率
BloomFilter<Person> friends = BloomFilter.create(personFunnel, 500, 0.01);
for(Person friend : friendsList) {
    friends.put(friend);
}

if (friends.mightContain(dude)) {
    // dude不是朋友还运行到这里的概率为1%
}
```

# 12 事件总线「EventBus」

暂空。

# 13 数学运算「Math」

使用Guava的Math可以进行防溢出检测以及其余复杂运算。

常用Api：

```Java
// 返回log以2为底的10的对数，第二个参数为RoundingMode舍入策略
int logFloor = LongMath.log2(10, FLOOR);
// 返回50，如果溢出则抛出ArithmeticException，快速失败
int mustNotOverflow = IntMath.checkedMultiply(5, 10);
// 8除以3四舍五入，返回3
long quotient = LongMath.divide(8, 3, RoundingMode.HALF_UP);
// 离1023.2最近的整数，向最近的整数舍入，返回1023
BigInteger nearestInteger = DoubleMath.roundToBigInteger(1023.2, RoundingMode.HALF_EVEN);
// 对第一个参数1023进行开根，向正无限大方向舍入，返回1024
BigInteger sideLength = BigIntegerMath.sqrt(nearestInteger, CEILING);
```

具体可见：[Guava-Math中文文档](http://ifeve.com/google-guava-math/)。

# 14 反射「Reflection」

暂空。



