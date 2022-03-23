## 第03课：定义查询方法（Defining Query Methods）

Spring Data JPA 的最大特色，利用方法名定义查询方法，我们本篇内容将围绕这个来展开详细讲解。

### 定义查询方法的配置方法

由于 Spring JPA Repository 的实现原理是采用动态代理的机制，所以我们介绍两种定义查询方法，从方法名称中可以指定特定用于存储的查询和更新，或通过使用 @Query 手动定义的查询，取决于实际对数据的操作，只需要实体 Repository 继承 Spring Data Common 里面的 Repository 接口即可，就像前面我们讲的一样。如果你想有其他更多默认通用方法的实现，可以选择 JpaRepository、PagingAndSortingRepository、CrudRepository 等接口，也可以直接继承我们后面要介绍的 JpaSpecificationExecutor、QueryByExampleExecutor，QuerydslPredicateExecutor 和自定义 Response，都可以达到同样的效果。

如果不想扩展 Spring 数据接口，还可以使用它来注解存储库接口 @RepositoryDefinition，扩展 CrudRepository 公开了一套完整的方法来操纵实体。如果希望对所暴露的方法有选择性，只需将要暴露的方法复制 CrudRepository 到域库中即可，其实也是自定义 Repository 的一种。

```
示例：选择性地暴露CRUD方法
@NoRepositoryBeaninterface
MyBaseRepository<T, ID extends Serializable> extends Repository<T, ID> {
    T findOne(ID id); 
    T save(T entity);
}
interface UserRepository extends MyBaseRepository<User, Long> {
     User findByEmailAddress(EmailAddress emailAddress);
}
```

在此实例中，您为所有域存储库定义了一个公共基础接口，并将其暴露出来，findOne(…) 和 save(…) 这些方法将由 Spring Data 路由到你提供的 MyBaseRepository 的基本 Repository 实现中。在 JPA 的默认情况下，SimpleJpaRepository 作为上面两个接口的实现类，所以 UserRepository 现在将能够保存用户，并通过 ID 查找单个，以及触发查询以 Users 通过其电子邮件地址查找。

**综上所述，得出以下两单：**

- MyRepository Extends Repository 接口就可以实现 Defining Query Methods 的功能。
- 继承其他 Repository 的子接口，或者自定义子接口，可以选择性的暴漏 SimpleJpaRepository 里面已经实现的基础公用方法。

### 方法的查询策略设置

通过下面的命令来配置方法的查询策略：

```
@EnableJpaRepositories(queryLookupStrategy= QueryLookupStrategy.Key.CREATE_IF_NOT_FOUND)
```

其中，QueryLookupStrategy.Key 的值一共就三个：

- Create：直接根据方法名进行创建，规则是根据方法名称的构造进行尝试，一般的方法是从方法名中删除给定的一组已知前缀，并解析该方法的其余部分。如果方法名不符合规则，启动的时候会报异常。
- `USE_DECLARED_QUERY`：声明方式创建，即本书说的注解的方式。启动的时候会尝试找到一个声明的查询，如果没有找到将抛出一个异常，查询可以由某处注释或其他方法声明。
- `CREATE_IF_NOT_FOUND`：这个是默认的，以上两种方式的结合版。先用声明方式进行查找，如果没有找到与方法相匹配的查询，那用 Create 的方法名创建规则创建一个查询。

**除非有特殊需求，一般直接用默认的，不用管。以 Spring Boot 项目为例，更改其配置方法如下：**

```
@EnableJpaRepositories(queryLookupStrategy= QueryLookupStrategy.Key.CREATE_IF_NOT_FOUND)
public class Example1Application {
   public static void main(String[] args) {
      SpringApplication.run(Example1Application.class, args);
   }
}
```

QueryLookupStrategy 是策略的定义接口，JpaQueryLookupStrategy 是具体策略的实现类，类图如下：

![enter image description here](http://images.gitbook.cn/e2afc6e0-2c4b-11e8-a18c-d1f2937a88af)

### 查询方法的创建

内部基础架构中有个根据方法名的查询生成器机制，对于在存储库的实体上构建约束查询很有用，该机制方法的前缀 find…By、read…By、query…By、count…By 和 get…By 从所述方法和开始分析它的其余部分（实体里面的字段）。

感兴趣的读者可以到类 org.springframework.data.repository.query.parser.PartTree 查看相关源码的逻辑和处理方法，关键源码如下：

![enter image description here](http://images.gitbook.cn/a0069fa0-2c4e-11e8-a18c-d1f2937a88af)

![enter image description here](http://images.gitbook.cn/2d2a8540-2c4f-11e8-a18c-d1f2937a88af)

引入子句可以包含其他表达式，例如在 Distinct 要创建的查询上设置不同的标志，然而，第一个 By 作为分隔符来指示实际标准的开始，在一个非常基本的水平，可以定义实体性条件，并与它们串联 And 和 Or。

> 一句话概况，带查询功能的方法名有查询策略（关键字）+ 查询字段 + 一些限制性条件组成。

**看例子如下，可以直接 Controller 里面进行调用看看效果：**

```
interface PersonRepository extends Repository<User, Long> {
   // and的查询关系
   List<User> findByEmailAddressAndLastname(EmailAddress emailAddress, String lastname);
   // 包含distinct去重，or的sql语法
   List<User> findDistinctPeopleByLastnameOrFirstname(String lastname, String firstname);
   List<User> findPeopleDistinctByLastnameOrFirstname(String lastname, String firstname);
   // 根据lastname字段查询忽略大小写
   List<User> findByLastnameIgnoreCase(String lastname);
   // 根据lastname和firstname查询equal并且忽略大小写
   List<User> findByLastnameAndFirstnameAllIgnoreCase(String lastname, String firstname); 
  // 对查询结果根据lastname排序
   List<User> findByLastnameOrderByFirstnameAsc(String lastname);
   List<User> findByLastnameOrderByFirstnameDesc(String lastname);
}
```

解析方法的实际结果取决于创建查询的持久性存储。但是，有一些一般的事情要注意：

- 表达式通常是可以连接运算符的属性遍历，可以使用组合属性表达式 And 和 or，还可以得到这样的运算关键字为支撑 Between、LessThan、GreaterThan、Like 为属性表达式，受支持的操作员可能因数据存储而异，需要注意一下。
- 该方法解析器支持设置一个 IgnoreCase 标志个别特性（例如，findByLastnameIgnoreCase(…)）或对于支持忽略大小写（通常是一个类型的所有属性 String 情况下，如 findByLastnameAndFirstnameAllIgnoreCase(…)），是否支持忽略案例可能会因存储而异，需要了解特定于场景的查询方法。
- 还可以通过 OrderBy 在引用属性和提供排序方向（Asc 或 Desc）的查询方法中附加一个子句来应用静态排序，要创建支持动态排序的查询方法，来影响查询结果。

### 3.4 关键字列表

|      关键字       |                           案例                            |                  JPQL 表达                   |
| :---------------: | :-------------------------------------------------------: | :------------------------------------------: |
|        And        |                findByLastnameAndFirstname                 | … where x.lastname = ?1 and x.firstname = ?2 |
|        Or         |                 findByLastnameOrFirstname                 | … where x.lastname = ?1 or x.firstname = ?2  |
|     Is,Equals     | findByFirstname、findByFirstnameIs、findByFirstnameEquals |           … where x.firstname = ?1           |
|      Between      |                  findByStartDateBetween                   |    … where x.startDate between ?1 and ?2     |
|     LessThan      |                     findByAgeLessThan                     |              … where x.age < ?1              |
|   LessThanEqual   |                  findByAgeLessThanEqual                   |             … where x.age <= ?1              |
|    GreaterThan    |                   findByAgeGreaterThan                    |              … where x.age > ?1              |
| GreaterThanEqual  |                 findByAgeGreaterThanEqual                 |             … where x.age >= ?1              |
|       After       |                   findByStartDateAfter                    |           … where x.startDate > ?1           |
|      Before       |                   findByStartDateBefore                   |           … where x.startDate < ?1           |
|      IsNull       |                      findByAgeIsNull                      |            … where x.age is null             |
| IsNotNull,NotNull |                   findByAge(Is)NotNull                    |            … where x.age not null            |
|       Like        |                    findByFirstnameLike                    |         … where x.firstname like ?1          |
|      NotLike      |                  findByFirstnameNotLike                   |       … where x.firstname not like ?1        |
|   StartingWith    |                findByFirstnameStartingWith                | … where x.firstname like ?1 (参数增加前缀 %) |
|    EndingWith     |                 findByFirstnameEndingWith                 | … where x.firstname like ?1 (参数增加后缀 %) |
|    Containing     |                 findByFirstnameContaining                 | … where x.firstname like ?1 (参数被 % 包裹)  |
|      OrderBy      |               findByAgeOrderByLastnameDesc                | … where x.age = ?1 order by x.lastname desc  |
|        Not        |                     findByLastnameNot                     |           … where x.lastname <> ?1           |
|        In         |               findByAgeIn(Collection ages)                |             … where x.age in ?1              |
|       NotIn       |              findByAgeNotIn(Collection ages)              |           … where x.age not in ?1            |
|       True        |                    findByActiveTrue()                     |           … where x.active = true            |
|       False       |                    findByActiveFalse()                    |           … where x.active = false           |
|    IgnoreCase     |                 findByFirstnameIgnoreCase                 |    … where UPPER(x.firstame) = UPPER(?1)     |

> 注意除了 find 的前缀之外，我们查看 PartTree 的源码，还有如下几种前缀：

```
private static final String QUERY_PATTERN = "find|read|get|query|stream";
private static final String COUNT_PATTERN = "count";
private static final String EXISTS_PATTERN = "exists";
private static final String DELETE_PATTERN = "delete|remove";
```

使用的时候要配合不同的返回结果进行使用，例如：

```
interface UserRepository extends CrudRepository<User, Long> {
     long countByLastname(String lastname);//查询总数
     long deleteByLastname(String lastname);//根据一个字段进行删除操作
     List<User> removeByLastname(String lastname);
}
```

大家也可以通过 Intellij IDEA ：Edit -> Find -> Find In Path 工具查找到关键字对应的枚举在哪个类里面，如下：

![enter image description here](http://images.gitbook.cn/457f3310-2c50-11e8-8971-5563b2ee93dd)

所以在这里作者介绍一工作中的小技巧，直接查看源码就可以知道框架支持了哪些关键字。

Type 枚举的关键源码如下：

```
public static enum Type {
    BETWEEN(2, new String[]{"IsBetween", "Between"}),
    IS_NOT_NULL(0, new String[]{"IsNotNull", "NotNull"}),
    IS_NULL(0, new String[]{"IsNull", "Null"}),
    LESS_THAN(new String[]{"IsLessThan", "LessThan"}),
    LESS_THAN_EQUAL(new String[]{"IsLessThanEqual", "LessThanEqual"}),
    GREATER_THAN(new String[]{"IsGreaterThan", "GreaterThan"}),
    GREATER_THAN_EQUAL(new String[]{"IsGreaterThanEqual", "GreaterThanEqual"}),
    BEFORE(new String[]{"IsBefore", "Before"}),
    AFTER(new String[]{"IsAfter", "After"}),
    NOT_LIKE(new String[]{"IsNotLike", "NotLike"}),
    LIKE(new String[]{"IsLike", "Like"}),
    STARTING_WITH(new String[]{"IsStartingWith", "StartingWith", "StartsWith"}),
    ENDING_WITH(new String[]{"IsEndingWith", "EndingWith", "EndsWith"}),
    IS_NOT_EMPTY(0, new String[]{"IsNotEmpty", "NotEmpty"}),
    IS_EMPTY(0, new String[]{"IsEmpty", "Empty"}),
    NOT_CONTAINING(new String[]{"IsNotContaining", "NotContaining", "NotContains"}),
    CONTAINING(new String[]{"IsContaining", "Containing", "Contains"}),
    NOT_IN(new String[]{"IsNotIn", "NotIn"}),
    IN(new String[]{"IsIn", "In"}),
    NEAR(new String[]{"IsNear", "Near"}),
    WITHIN(new String[]{"IsWithin", "Within"}),
    REGEX(new String[]{"MatchesRegex", "Matches", "Regex"}),
    EXISTS(0, new String[]{"Exists"}),
    TRUE(0, new String[]{"IsTrue", "True"}),
    FALSE(0, new String[]{"IsFalse", "False"}),
    NEGATING_SIMPLE_PROPERTY(new String[]{"IsNot", "Not"}),
    SIMPLE_PROPERTY(new String[]{"Is", "Equals"});
....}
```

### 方法的查询策略的属性表达式（Property Expressions）

属性表达式只能引用托管（泛化）实体的直接属性，如前一个示例所示。在查询创建时，已经确保解析的属性是托管实体的属性，但是，还可以通过遍历嵌套属性定义约束。假设一个 Person 实体对象里面有一个 Address 的属性里面包含一个 ZipCode 属性。

在这种情况下，方法名为：

```
List findByAddressZipCode(String zipCode);
```

创建及其查找的过程是：解析算法首先将整个 part（AddressZipCode）解释为属性，并使用该名称（uncapitalized）检查域类的属性，如果算法成功，则使用该属性，如果不是，则算法拆分了从右侧的驼峰部分的信号源到头部和尾部，并试图找出相应的属性。在我们的例子中，AddressZip 和 Code 如果算法找到一个具有该头部的属性，那么它需要尾部，并从那里继续构建树，然后按照刚刚描述的方式将尾部分割，如果第一个分割不匹配，则算法将分割点移动到左（Address，ZipCode），然后继续。

虽然这在大多数情况下应该起作用，但算法可能会选择错误的属性。假设 Person 该类也有一个 addressZip 属性，该算法将在第一个分割轮中匹配，并且基本上选择错误的属性，最后失败（因为该类型 addressZip 可能没有 code 属性）。

要解决这个歧义，可以在方法名称中使用手动定义遍历点，所以我们的方法名称最终会如此：

```
List findByAddress_ZipCode(ZipCode zipCode);
```

当然 Spring JPA 里面是将下划线视为保留字符，但是强烈建议遵循标准 Java 命名约定（即不使用属性名称中的下划线，而是使用骆驼案例），属性命名的时候注意下这个特性。

可以到 PartTreeJpaQuery.class 查询一下相关的 method 的 name 的拆分和实现逻辑，也可以利用开发工具的 Search anywhere 视图输入 PropertyExpression，然后 Find Used 就可以跟出很多源码，然后设置个断点，就可以进行分析了。

### 查询结果的处理

#### 参数选择（Sort/Pageable）分页和排序

##### 特定类型的参数，Pageable 并动态 Sort 地将分页和排序应用于查询

案例：在查询方法中使用 Pageable、Slice 和 Sort。

```
Page<User> findByLastname(String lastname, Pageable pageable);
Slice<User> findByLastname(String lastname, Pageable pageable);
List<User> findByLastname(String lastname, Sort sort);
List<User> findByLastname(String lastname, Pageable pageable);
```

第一种方法允许将 org.springframework.data.domain.Pageable 实例传递给查询方法，以动态地将分页添加到静态定义的查询中，Page 知道可用的元素和页面的总数，它通过基础框架里面触发计数查询来计算总数。由于这可能是昂贵的，这取决于所使用的场景，说白了，当用到 Pageable 的时候会默认执行一条 cout 语句。而 Slice 的用作是，只知道是否有下一个 Slice 可用，不会执行count，所以当查询较大的结果集时，只知道数据是足够的，而相关的业务场景也不用关心一共有多少页。

排序选项也通过 Pageable 实例处理，如果只需要排序，需在 org.springframework.data.domain.Sort 参数中添加一个参数即可，正如看到的，只需返回一个 List 也是可能的。在这种情况下，Page 将不会创建构建实际实例所需的附加元数据（这反过来意味着必须不被发布的附加计数查询），而仅仅是限制查询仅查找给定范围的实体。

##### 限制查询结果

案例：在查询方法上加限制查询结果的关键字 First 和 top。

```
User findFirstByOrderByLastnameAsc();
User findTopByOrderByAgeDesc();
Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);
Slice<User> findTop3ByLastname(String lastname, Pageable pageable);
List<User> findFirst10ByLastname(String lastname, Sort sort);
List<User> findTop10ByLastname(String lastname, Pageable pageable);
```

查询方法的结果可以通过关键字来限制 first 或 top，其可以被可互换使用，可选的数值可以追加到顶部/第一个以指定要返回的最大结果的大小。如果数字被省略，则假设结果大小为 1，限制表达式也支持 Distinct 关键字。此外，对于将结果集限制为一个实例的查询，支持将结果包装到一个实例中 Optional。如果将分页或切片应用于限制查询分页（以及可用页数的计算），则在限制结果中应用。

#### 查询结果的不同形式（List/Stream/Page/Future）

Page 和 List 在上面的案例中都有涉及下面将介绍的几种特殊的方式。

##### 流式查询结果

可以通过使用 Java 8 Stream 作为返回类型来逐步处理查询方法的结果，而不是简单地将查询结果包装在 Stream 数据存储中，特定的方法用于执行流。

示例：使用 Java 8 流式传输查询的结果 Stream。

```
@Query("select u from User u")
Stream<User> findAllByCustomQueryAndStream();
Stream<User> readAllByFirstnameNotNull();
@Query("select u from User u")
Stream<User> streamAllPaged(Pageable pageable);
```

注意：流的关闭问题，try cache 是一种用关闭方法。

```
Stream<User> stream;
try {
   stream = repository.findAllByCustomQueryAndStream()
   stream.forEach(…);
} catch (Exception e) {
   e.printStackTrace();
} finally {
   if (stream!=null){
      stream.close();
   }
}
```

##### 异步查询结果

可以使用 Spring 的异步方法执行功能异步执行存储库查询，这意味着方法将在调用时立即返回，并且实际的查询执行将发生在已提交给 Spring TaskExecutor 的任务中，比较适合定时任务的实际场景。

```
@Async
Future<User> findByFirstname(String firstname); (1)
@Async
CompletableFuture<User> findOneByFirstname(String firstname); (2)
@Async
ListenableFuture<User> findOneByLastname(String lastname);(3)
```

- 使用 java.util.concurrent.Future 的返回类型。
- 使用 java.util.concurrent.CompletableFuture 作为返回类型。
- 使用 org.springframework.util.concurrent.ListenableFuture 作为返回类型。

> 所支持的返回结果类型远不止这些，可以根据实际的使用场景灵活选择，其中 Map 和 Object[] 的返回结果也支持，这种方法不太推荐使用，应为没有用到对象思维，不知道结果里面装的是什么。

下表列出了 Spring Data JPA Query Method 机制支持的方法的返回值类型。

> 某些特定的存储可能不支持全部的返回类型。 只有支持地理空间查询的数据存储才支持 GeoResult、GeoResults、GeoPage 等返回类型。

| 返回值类型        | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| void              | 不返回结果，一般是更新操作                                   |
| Primitives        | Java 的基本类型，一般常见的是统计操作（如 long、boolean 等）Wrapper types Java 的包装类 |
| T                 | 最多只返回一个实体，没有查询结果时返回 null。如果超过了一个结果会抛出 IncorrectResultSizeDataAccessException 的异常。 |
| Iterator          | 一个迭代器                                                   |
| Collection        | A 集合                                                       |
| List              | List 及其任何子类                                            |
| Optional          | 返回 Java 8 或 Guava 中的 Optional 类。查询方法的返回结果最多只能有一个，如果超过了一个结果会抛出 IncorrectResultSizeDataAccessException 的异常 |
| Option            | Scala 或者 javaslang 选项类型                                |
| Stream            | Java 8 Stream                                                |
| Future            | Future，查询方法需要带有 @Async 注解，并开启 Spring 异步执行方法的功能。一般配合多线程使用。关系数据库，实际工作很少有用到 |
| CompletableFuture | 返回 Java8 中新引入的 CompletableFuture 类，查询方法需要带有 @Async 注解，并开启 Spring 异步执行方法的功能 |
| ListenableFuture  | 返回 org.springframework.util.concurrent.ListenableFuture 类，查询方法需要带有 @Async 注解，并开启 Spring 异步执行方法的功能 |
| Slice             | 返回指定大小的数据和是否还有可用数据的信息。需要方法带有 Pageable 类型的参数 |
| Page              | 在 Slice 的基础上附加返回分页总数等信息。需要方法带有 Pageable 类型的参数 |
| GeoResult         | 返回结果会附带诸如到相关地点距离等信息                       |
| GeoResults        | 返回 GeoResult 的列表，并附带到相关地点平均距离等信息        |
| GeoPage           | 分页返回 GeoResult，并附带到相关地点平均距离等信息           |

而我们要看引用的那个 Spring Data 的实现子模块，以 Spring Data JPA 为例，看看 JPA 默认帮实现了哪些返回值类型。

![enter image description here](http://images.gitbook.cn/0507dff0-2c53-11e8-8971-5563b2ee93dd)

> 还是通过工具分析 JpaRepository 帮我们实现了哪些返回类型，这样不至于直接看官方文档的时候一头雾水。

##### **Projections 对查询结果的扩展**

Spring JPA 对 Projections 的扩展的支持，个人觉得这是个非常好的东西，从字面意思上理解就是映射，指的是和 DB 的查询结果的字段映射关系。一般情况下，我们是返回的字段和 DB 的查询结果的字段是一一对应的，但有的时候，需要返回一些指定的字段，不需要全部返回，或者返回一些复合型的字段，还得自己写逻辑。Spring Data 正是考虑到了这一点，允许对专用返回类型进行建模，以便更有选择地将部分视图对象。

假设 Person 是一个正常的实体，和数据表 Person 一一对应，我们正常的写法如下：

```
@Entity
class Person {
   @Id
   UUID id;
   String firstname, lastname;
   Address address;
   @Entity
   static class Address {
      String zipCode, city, street;
   }
}
interface PersonRepository extends Repository<Person, UUID> {
   Collection<Person> findByLastname(String lastname);
}
```

（1）但是我们想仅仅返回其中的 name 相关的字段，应该怎么做呢？如果基于 projections 的思路，其实是比较容易的。只需要声明一个接口，包含我们要返回的属性的方法即可。如下：

```
interface NamesOnly {
  String getFirstname();
  String getLastname();
}
```

Repository 里面的写法如下，直接用这个对象接收结果即可，如下：

```
interface PersonRepository extends Repository<Person, UUID> {
  Collection<NamesOnly> findByLastname(String lastname);
}
```

Ctroller 里面直接调用这个对象可以看看结果。

原理是，底层会有动态代理机制为这个接口生产一个实现实体类，在运行时。

（2）查询关联的子对象，一样的道理，如下：

```
interface PersonSummary {
  String getFirstname();
  String getLastname();
  AddressSummary getAddress();
  interface AddressSummary {
    String getCity();
  }
}
```

（3）@Value 和 SPEL 也支持：

```
interface NamesOnly {
  @Value("#{target.firstname + ' ' + target.lastname}")
  String getFullName();
  …
}
```

PersonRepository 里面保持不变，这样会返回一个 firstname 和 lastname 相加的只有 fullName 的结果集合。

（4）对 Spel 表达式的支持远不止这些：

```
@Component
class MyBean {
  String getFullName(Person person) {
    …//自定义的运算
  }
}
interface NamesOnly {
  @Value("#{@myBean.getFullName(target)}")
  String getFullName();
  …
}
```

（5）还可以通过 Spel 表达式取到方法里面的参数的值。

```
interface NamesOnly {
  @Value("#{args[0] + ' ' + target.firstname + '!'}")
  String getSalutation(String prefix);
}
```

（6）这时候有人会在想，只能用 interface 吗？dto 支持吗？也是可以的，也可以定义自己的 Dto 实体类，需要哪些字段我们直接在 Dto 类当中暴漏出来 get/set 属性即可，如下：

```
class NamesOnlyDto {
  private final String firstname, lastname;
//注意构造方法
  NamesOnlyDto(String firstname, String lastname) {
    this.firstname = firstname;
    this.lastname = lastname;
  }
  String getFirstname() {
    return this.firstname;
  }
  String getLastname() {
    return this.lastname;
  }
}
```

（7）支持动态 Projections，想通过泛化，根据不同的业务情况，返回不通的字段集合。

```
PersonRepository做一定的变化，如下：
interface PersonRepository extends Repository<Person, UUID> {
  Collection<T> findByLastname(String lastname, Class<T> type);
}
```

我们的掉用方，就可以通过 class 类型动态指定返回不同字段的结果集合了，如下：

```
void someMethod(PersonRepository people) {
//我想包含全字段，就直接用原始entity（Person.class）接收即可
  Collection<Person> aggregates = people.findByLastname("Matthews", Person.class);
//如果我想仅仅返回名称，我只需要指定Dto即可。
  Collection<NamesOnlyDto> aggregates = people.findByLastname("Matthews", NamesOnlyDto.class);
}
```

最后，Projections 的应用场景还是挺多的，望大家好好体会，这样可以实现更优雅的代码，去实现不同的场景。不必要用数组，冗余的对象去接收查询结果。

#### 实现机制介绍

通过 QueryExecutorMethodInterceptor 这个类的源代码，我们发现，该类实现了 MethodInterceptor 接口，也就是说它是一个方法调用的拦截器， 当一个 Repository 上的查询方法，譬如说 findByEmailAndLastname 方法被调用，Advice 拦截器会在方法真正的实现调用前，先执行这个 MethodInterceptor 的 invoke 方法。这样我们就有机会在真正方法实现执行前执行其他的代码了。

然而对于 QueryExecutorMethodInterceptor 来说，最重要的代码并不在 invoke 方法中，而是在它的构造器 QueryExecutorMethodInterceptor(RepositoryInformationr、Object customImplementation、Object target) 中。

最重要的一段代码是这段：

```
for (Method method : queryMethods) { 
     // 使用lookupStrategy，针对Repository接口上的方法查询Query
     RepositoryQuery query = lookupStrategy.resolveQuery(method, repositoryInformation, factory, namedQueries); invokeListeners(query);
     queries.put(method, query);
}
```

通过这个思路我们就可以找到很多具体的实现方法，其中有个重要类 PartTree，包含了主要算法逻辑，一图胜千言，我们来看一下网友提供的图。

![enter image description here](http://images.gitbook.cn/120aa550-2c55-11e8-a38a-fba8c3666ea6)