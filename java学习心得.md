# dubbo学习及问题

java.lang.NoClassDefFoundError: org/apache/curator/framework/CuratorFrameworkFactory 

解决方法：

添加以下依赖

```xml
<dependency>

  <groupId>org.apache.curator</groupId>

  <artifactId>curator-framework</artifactId>

  <version>5.1.0</version>

</dependency>
```

**dubbo版本升级**：注意需要同时升级**dubbo version，dubbo-denpendencies-zookeeper-version，dubbo-spring-boot-starter**,版本要一致， 网上说zookeeper本地版本和服务端要一致 问题：Invalid name="com.alibaba.dubbo.config.ProtocolConfig#0" https://github.com/apache/dubbo/issues/4657 

在application.propertites中加入属性dubbo.protocol.id=dubbo

## Spring启动

Spring启动--->读取XML文件--->根据命名空间获取具体的namespacehandler,该处理器在dubbo包的META-INF/spring.handlers文件中定义-->对应的handler会解析对应元素节点，通过BeanDefinitionParser

## Dubbo启动

Dubbo启动-->实例化一个ExtensionLoader<Container>类型的容器加载器,加载Container接口的所有可用扩展点-->根据 main方法参数>配置文件>SPI注解默认值的优先级顺序获取到所需的扩展点并启动

 

由于初始任务主要在Spring中进行，因此关注SpringContainer,将dubbo:service转换成实际的ServiceBean,经过Spring的一系列创建过程，调用export方法来暴露服务，然后调doExportUrls方法做服务暴露。

 

loadRegistries(true)

加载注册中心并统一构建成List<URL>集合

 

```java
for (ProtocolConfig protocolConfig : protocols) {

​      doExportUrlsFor1Protocol(protocolConfig, registryURLs);

​    }
```

因为dubbo支持多协议配置，遍历所有协议分别根据不同的协议把服务export到不同的注册中心上去

# JDK8中Stream的使用

和以前的Iterator或者For-Each不同，Stream提供内部迭代

## 生成流

在 Java 8 中, 集合接口有两个方法来生成流：

- **stream()** − 为集合创建串行流。
- **parallelStream()** − 为集合创建并行流。

## Stream中方法

| Stream中方法 | 文字介绍                 | 实例                                                         |
| ------------ | ------------------------ | ------------------------------------------------------------ |
| `forEach()`  | 迭代流中数据             | `Random random = new Random(); <br />random.ints().limit(10).forEach(System.out::println);` |
| `map()`      | 映射每个元素到对应结果   | `List<Integer> numbers = Arrays.asList(3, 2, 2, 3, 7, 3, 5); // 获取对应的平方数 <br /><br />List<Integer> squaresList = numbers.stream().map( i -> i*i).distinct().collect(Collectors.toList());` |
| `filter()`   | 通过设置的条件过滤出元素 | `List<String>strings = Arrays.asList("abc", "", "bc", "efg", "abcd","", "jkl"); // 获取空字符串的数量 long count = strings.stream().filter(string -> string.isEmpty()).count();` |
| `limit()`    | 获取指定数量的流         | `Random random = new Random(); random.ints().limit(10).forEach(System.out::println);` |
| `sorted()`   | 对流进行排序             | `Random random = new Random(); random.ints().limit(10).sorted().forEach(System.out::println);``//倒序可以写调整排序方式sorted((x,y)->y-x)` |

# JSONField

注解在字段和方法上，实例序列化时将名字替换为注解中的name，反序列化时从JSON中name字段取值注入

# Comparator.comparingInt(xxx)

xxx表示用于比较的参数，通过该参数进行排序，默认升序，后面加个.reversed()就降序

```java
import java.util.Arrays;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;
// 重写比较器
class Persons implements  Comparable<Persons> {
    String name;
    int age;
    public Persons(String name, int age) {
        this.name = name;
        this.age = age;
    }
	
	// 定义此方法接受单个参数keyExtractor，该参数是用于提取整数排序键的函数。
    public int getAge() {
        return this.age;
    }
    // 按姓氏ASCII降序，反过来升序
    @Override
    public int compareTo(Persons o) {
        return this.name.compareTo(o.name);
    }
}
public class ComparatorTest {
    public static void main(String[] args) {
        // create some Persons objects
        Persons p1 = new Persons("B", 10);
        Persons p2 = new Persons("D", 20);
        Persons p3 = new Persons("A", 18);
        // before sort
        List<Persons> list = Arrays.asList(p2, p1, p3);
        System.out.println("Before Sort:");
        list.forEach(Persons -> System.out.println("Persons name"+Persons.name));
        // 自定义比较器
        Comparator<Persons> byAge = Comparator.comparingInt(Persons::getAge);
        Collections.sort(list, byAge);
        System.out.println("After Sort:");
        list.forEach(Persons -> System.out.println("Persons age"+Persons.age));
    }
}
结果：

Before Name Sort:
Persons nameD
Persons nameB
Persons nameA
…
After Age Sort:
Persons nameB
Persons nameA
Persons nameD
```

# lombok框架

@Data

整合包，只要加了 @Data 这个注解，等于同时加了以下注解

@Getter/@Setter

@ToString

@EqualsAndHashCode

@RequiredArgsConstructor 只有final修饰的字段写在构造函数

@Value

- @Getter (注意没有setter)
- @ToString
- @EqualsAndHashCode
- @RequiredArgsConstructor

@Builder

自动生成流式set值写法

![img](https://img2020.cnblogs.com/i-beta/1003856/202003/1003856-20200309123907759-554181302.png)

@Slf4j

自动生成该类的 log 静态常量，要打日志就可以直接打，不用再手动 new log 静态常量了



# BeanUtils.copyProperties（）

该方法是浅拷贝。
浅拷贝： 只是调用子对象的set方法，并没有将所有属性拷贝。(也就是说，**引用的一个内存地址**)
深拷贝： 将子对象的属性也拷贝过去。

| –    | spring的BeanUtils            | commons的BeanUtils |
| ---- | ---------------------------- | ------------------ |
| 方法 | copyProperty和copyProperties | copyProperties     |
| 参数 | src ，dest                   | dest，src          |

# [Collections.singletonList方法](https://www.cnblogs.com/crazy-lc/p/13724101.html)

　　这个方法主要用于只有一个元素的优化，**减少内存分配，无需分配额外的内存**，可以从SingletonList内部类看得出来,由于只有一个element,因此可以做到内存分配最小化，相比之下ArrayList的DEFAULT_CAPACITY=10个。

```java
/**
     * Returns an immutable list containing only the specified object.
     * The returned list is serializable.
     *
     * @param  <T> the class of the objects in the list
     * @param o the sole object to be stored in the returned list.
     * @return an immutable list containing only the specified object.
     * @since 1.3
     */
    public static <T> List<T> singletonList(T o) {
        return new SingletonList<>(o);
    }
```

# Future多线程

定义在`java.util.concurrent`包中，主线程只要简单的将异步任务封装在 Future 里，然后开始等待 Future 的完成，在这段等待的时间内，可以处理一些其它逻辑，一旦 Future 执行完毕，就可以从中获取执行的结果并进一步处理。

```JAVA
public class SquareCalculator {    

    private ExecutorService executor 
      = Executors.newSingleThreadExecutor();

    public Future<Integer> calculate(Integer input) {        
        return executor.submit(() -> {
            Thread.sleep(1000);
            return input * input;
        });
    }
}
```

Future 实例中获取结果，我们需要用到两个方法：`isDone()` 和 `get()`

1. `Future.isDone()` 方法用于获取我们的执行器是否已完成任务处理。如果任务完成，则返回 `true`，否则返回 `false`。

2. 从计算中返回实际结果的方法是 `Future.get()`。但要注意的是，`Future.get()` 方法是一个阻塞方法。如果任务还没执行完毕，那么会一直阻塞直到直到任务完成，在底层调用`awaitDone（）`方法，而后调用`LockSupport.park（）`方法等待线程执行状态变为complete，再执行LockSupport.unpark（）方法结束阻塞

3. ```java
   boolean canceled = future.cancel(true);//可以取消线程
   ```

# 注解学习

## 1.定义注解

```java
public @interface Report {
    int type() default 0;
    String level() default "info";
    String value() default "";
}
```

注意可以使用**<u>default</u>**设置默认值

## 2.元注解

元注解用于修饰其他注解，通常由java标准库定义

### @Target

定义注解放在源码的哪些位置

- 类或接口：`ElementType.TYPE`；
- 字段：`ElementType.FIELD`；
- 方法：`ElementType.METHOD`；
- 构造方法：`ElementType.CONSTRUCTOR`；
- 方法参数：`ElementType.PARAMETER`。

### @Retention

定义注解的生命周期，默认为CLASS，自定义一般为RUNTIME：

- 仅编译期：`RetentionPolicy.SOURCE`；
- 仅class文件：`RetentionPolicy.CLASS`；
- 运行期：`RetentionPolicy.RUNTIME`。

### @Repeatable

定义注解是否重复

### @Inherited

定义子类是否可继承父类定义的Annotation，加上该注解就表示继承，只针对class的继承，接口的继承不行

## 3.各种注解

### @Autowired

把bean按类型注入

### @Resource

把bean按name注入 可以定义name属性