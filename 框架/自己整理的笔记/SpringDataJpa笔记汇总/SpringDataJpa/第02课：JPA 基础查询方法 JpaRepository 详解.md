## 第02课：JPA 基础查询方法 JpaRepository 详解

本篇内容我们一起学习 Spring Data Common 里面的公用基本方法，本章不仅介绍用法，还会介绍一个更好的学习过程。

### Spring Data Common 的 Repository

Repository 位于 Spring Data Common 的 lib 里面，是 Spring Data 里面做数据库操作的最底层的抽象接口，最顶级的父类，源码里面其实什么方法都没有，仅仅起到一个标识作用。管理域类以及域类的 ID 类型作为类型参数，此接口主要作为标记接口来捕获要使用的类型，并帮助用户发现扩展此接口的接口。Spring 底层做动态代理的时候发现只要是它的子类或者实现类，都代表储存库操作。

Repository 的源码如下：

```
package org.springframework.data.repository;
import org.springframework.stereotype.Indexed;
@Indexed
public interface Repository<T, ID> {

}
```

有了这个类，我们就能顺腾摸瓜，找到好多 Spring Data JPA 自己提供的基本接口和操作类，及其实现方法，这个接口定义了所有 Repostory 操作的实体和 ID 的泛型参数。当不是继承任何就可，只要继承这个接口，就可以使用 Spring JPA 里面提供的很多约定的方法查询和注解查询，后面章节会详细介绍。

### Repository 的类层次关系（Diagms/Hierarchy/Structure）

我们来根据 Repository 这个基类，顺腾摸瓜看看 Spring Data JPA 里面都有些什么？同时将介绍学习的方法，这样不管碰到学习任何一个框架时，方法都雷同，逐步从入门到精通，提高学习效率。

1）我们用工具 Intellij Idea，打开类 Repository.class，然后单击 Navigate -> Type Hierchy，会得到如下视图：

![enter image description here](http://images.gitbook.cn/f61dc670-2aaf-11e8-9017-67397396aa0f)

通过该层次结构视图，就会明白基类 Repository 的用意，需要对工程里面的所有 Repository 了如执掌，项目里面有哪些，Spring 的项目里面有哪些一目了然。我们通过上面的类的结构图，可以看得出来 Repository 可以分为三个部分：

- 即本篇要介绍的正常的 JpaRepository 这条线的操作。
- Reactive**Repository 这条线响应式编程，主要支持目前的 NoSQL 方面的操作，因为 NoSQL 大部分的操作都是分布式的，所以足可以看的出来 Spring Data 的野心，想提供关于所有 Data 方面的操，目前主要有 Cassandra、MongoDB 的实现，与 JPA 属于平级项目。
- RxJava2CrudRepository 这条线是为了支持 RxJava 2 做的标准的响应式编程的接口。

2）通过 Intellij Idea，打开类上面 Example 1 里面的 UserRepository.java，单击鼠标右键 show diagrams 用图表的方式查看类的关系层次，打开如下图所示：

![enter image description here](http://images.gitbook.cn/c9c95e50-2ab3-11e8-b513-03942d4be539)

3）通过 Intellij Idea，打开类 QueryDslJpaRepository，单击鼠标右键 show diagrams 用图表的方式查看类的关系层次，打开如下图所示：

![enter image description here](http://images.gitbook.cn/d2d8b450-2ab3-11e8-b513-03942d4be539)

4）通过 Intellij Idea，打开类上面的 Example 1 里面的 UserRepository.java，单击 Navigate | File Structure 命令，可以查看此类的结构及有哪些方法，以此类推到其他类上，打开如下图所示：

![enter image description here](http://images.gitbook.cn/e14072d0-2ab3-11e8-acca-d972d7a925f0)

> 以上三种视图是开发过程中会经常用到的视图，而通过上面的图就可以知道如果要学习 JPA 或者是 solr 等其他 Spring Data 实现的时候需要掌握哪些东西，本篇内容以 JPA 为主线来讲解。

我们来看一个 Repository 的实例：

```
package com.example.example2.repository;
import com.example.example2.entity.User;
import org.springframework.data.repository.Repository;
import java.util.List;
public interface UserRepository extends Repository<User,Integer> {
    /**
     * 根据名称进行查询用户列表
     * @param name
     * @return
     */
    List<User> findByName(String name);
    /**
     * 根据用户的邮箱和名称查询
     *
     * @param email
     * @param name
     * @return
     */
    List<User> findByEmailAndName(String email, String name);
}
```

### CrudRepository 方法详解

通过上面类关系图可以看到 CrudRepository 提供了公共的通用的 CRUD 方法。

#### CrudRepository interface 内容

```
package org.springframework.data.repository;
import java.util.Optional;
@NoRepositoryBean
public interface CrudRepository<T, ID> extends Repository<T, ID> {
    <S extends T> S save(S entity);(1)
    <S extends T> Iterable<S> saveAll(Iterable<S> entities);(2)
    Optional<T> findById(ID id);(3)
    boolean existsById(ID id);(4)
    Iterable<T> findAll();(5)
    Iterable<T> findAllById(Iterable<ID> ids);(6)
    long count();(7)
    void deleteById(ID id);(8)
    void delete(T entity);(9)
    void deleteAll(Iterable<? extends T> entities);(10)
    void deleteAll();(11)
}
```

- （1）保存实体方法。

原理：我们通过刚才的类关系查看其实现类，SimpleJpaRepository 里面的实现方法如下：

```
    @Transactional
    public <S extends T> S save(S entity) {
        if (entityInformation.isNew(entity)) {
            em.persist(entity);
            return entity;
        } else {
            return em.merge(entity);
        }
    }
```

我们发现他是先出查一下传进去的实体是不是存在，然后判断是新增还是更新，是不是存在根据两种机制，一种是根据主键来判断，还有一种是根据 Version 来判断，后面介绍 Version 的时候详解，所以如果去看 JPA 的控制台打印出来的 SQL 最少会有两条，一条是查询，一条是 Insert 或者 Update。

- （2）批量保存，原理和（1）相同，我们去看实现的话，就是 for 循环调用上面的 save 方法。
- （3）根据主键查询实体，返回 JDK 1.8 的 Optional，这可以避免 null exception。
- （4）根据主键判断实体是否存在。
- （5）查询实体的所有列表。
- （6）根据主键列表查询实体列表。
- （7）查询总数。
- （8）根据主键删除，查看源码会发现，其是先查询出来再进行删除。
- （9）根据 entity 进行删除。
- （10）批量删除。
- （11）删除所有，原理：通过刚才的类关系查看其的实现类，SimpleJpaRepository 里面的 delete 实现方法如下，都是调用 delete 进行删除。

```
    @Transactional
    public void deleteById(ID id) {
        Assert.notNull(id, ID_MUST_NOT_BE_NULL);
        delete(findById(id).orElseThrow(() -> new EmptyResultDataAccessException(
                String.format("No %s entity with id %s exists!", entityInformation.getJavaType(), id), 1)));
    }
    @Transactional
    public void delete(T entity) {
        Assert.notNull(entity, "The entity must not be null!");
        em.remove(em.contains(entity) ? entity : em.merge(entity));
    }
    @Transactional
    public void deleteAll(Iterable<? extends T> entities) {
        Assert.notNull(entities, "The given Iterable of entities not be null!");
        for (T entity : entities) {
            delete(entity);
        }
    }
```

> 我们发现关于 Update、Delete、Save 等操作 JPA 自己也会先查询一下，再去做保存操作，不存在抛出异常。特别强调了一下 Delete 和 Save 方法，是因为看到实际工作中，有同事会画蛇添足，自己在做 Save 的时候先去 Find 一下，其实是没有必要的，Spring JPA 底层都考虑到了。所以这里其实是想告诉大家，当我们用任何第三方方法的时候最好先查一下其源码和逻辑或者 API 再写出优雅的代码。

#### CrudRepository Interface 的使用案例

使用也很简单，只需要自己的 Repository 继承 CrudRepository 即可。第01课的案例修改如下：UserCrudRepository 继承 CrudRepository：

```
package com.example.example2.repository;
import com.example.example2.entity.User;
import org.springframework.data.repository.CrudRepository;
import java.util.List;
public interface UserCrudRepository extends CrudRepository<User,Integer> {
}
```

第01课的案例 UserController，修改如下：

```
package com.example.example2;
import com.example.example2.entity.User;
import com.example.example2.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;
import java.util.Optional;
@Controller
@RequestMapping(path = "/demo")
public class UserController {
    @Autowired
    private UserCrudRepository userRepository;

    @GetMapping(path = "/add")
    public void addNewUser(@RequestParam String name, @RequestParam String email) {
        User n = new User();
        n.setName(name);
        n.setEmail(email);
        userRepository.save(n);
    }

    @GetMapping(path = "/all")
    @ResponseBody

    public Iterable<User> getAllUsers() {
        return userRepository.findAll();
    }

    @GetMapping(path = "/info")
    @ResponseBody

    public Optional<User> findOne(@RequestParam Integer id) {
        return userRepository.findById(id);
    }

    @GetMapping(path = "/delete")

    public void delete(@RequestParam Integer id) {
        userRepository.deleteById(id);
    }
}
```

然后启动运行就可以直接看效果了。

### PagingAndSortingRepository 方法详解

通过类的关系图，我们可以看到 PagingAndSortingRepository 继承 CrudRepository 所有他的基本方法，它都有增加了分页和排序等对查询结果进行限制的一些基本的、常用的、通用的一些分页方法。

#### PagingAndSortingRepository interface 内容

> 一样，我们也来查看一下 PagingAndSortingRepository 的源码看看提供了哪些方法。

```
package org.springframework.data.repository;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
    Iterable<T> findAll(Sort sort); （1）
    Page<T> findAll(Pageable pageable); （2）
}
```

- （1）根据排序取所有的对象的集合。
- （2）根据分页和排序进行查询，并用 Page 对象封装。Pageable 对象包含分页和 Sort 对象。

PagingAndSortingRepository 和 CrudRepository 都是 Spring Data Common 的标准接口，如果我们采用 JPA 那它对应的实现类就是 Spring Data JPA 的 Model 里面的 SimpleJpaRepository。如果是其他 NoSQL 实现 MongoDB，那它的实现就在 Spring Data MongoDB 的 Model 里面。

来看一下 Page 查询的实现内容如下：

```
    public Page<T> findAll(Pageable pageable) {
        if (isUnpaged(pageable)) {
            return new PageImpl<T>(findAll());
        }
        return findAll((Specification<T>) null, pageable);
    }
```

看源码发现这些查询都会用到后面章节要讲的 Specification 查询方法。

#### PagingAndSortingRepository 使用案例

也是只需要继承 PagingAndSortingRepository 的接口即可，其他不要做任何改动，UserPagingAndSortingRepository 修改如下：

```
package com.example.example2.repository;
import com.example.example2.entity.User;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.repository.PagingAndSortingRepository;
public interface UserPagingAndSortingRepository extends PagingAndSortingRepository<User,Long> {
    Page<User> findByName(String name, Pageable pageable) throws Exception;
}
```

UserController 修改如下：

```
@Controller
@RequestMapping(path = "/demo")
public class UserController {
    /**
     * 验证排序和分页查询方法，Pageable的默认实现类：PageRequest
     * @return
     */
    @GetMapping(path = "/page")
    @ResponseBody
    public Page<User> getAllUserByPage() {
        return userPagingAndSortingRepository.findAll(
                new PageRequest(1, 20,new Sort(new Sort.Order(Sort.Direction.ASC,"name"))));
    }
    /**
     * 排序查询方法，使用Sort对象
     * @return
     */
    @GetMapping(path = "/sort")
    @ResponseBody
    public Iterable<User> getAllUsersWithSort() {
        return userPagingAndSortingRepository.findAll(new Sort(new Sort.Order(Sort.Direction.ASC,"name")));
    }
}
```

### JpaRepository 方法详解

JpaRepository 到这里可以进入到分水岭了，上面的那些都是 Spring Data 为了兼容 NoSQL 而进行的一些抽象封装，而从 JpaRepository 开始是对关系型数据库进行抽象封装，从类图可以看得出来它继承 PagingAndSortingRepository 类，也就继承了其所有方法，并且其实现类也是 SimpleJpaRepository。从类图上还可以看出 JpaRepository 继承和拥有了 QueryByExampleExecutor 的相关方法，而 QueryByExampleExecutor 的详细用法会在后面的章节中详细介绍，先来看一下 JpaRepository 有哪些方法：

```
package org.springframework.data.jpa.repository;

import java.util.List;

import javax.persistence.EntityManager;

import org.springframework.data.domain.Example;
import org.springframework.data.domain.Sort;
import org.springframework.data.repository.NoRepositoryBean;
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.repository.query.QueryByExampleExecutor;

/**
 * JPA specific extension of {@link org.springframework.data.repository.Repository}.
 *
 * @author Oliver Gierke
 * @author Christoph Strobl
 * @author Mark Paluch
 */
@NoRepositoryBean
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID>, QueryByExampleExecutor<T> {

    /*
     * (non-Javadoc)
     * @see org.springframework.data.repository.CrudRepository#findAll()
     */
    List<T> findAll();

    /*
     * (non-Javadoc)
     * @see org.springframework.data.repository.PagingAndSortingRepository#findAll(org.springframework.data.domain.Sort)
     */
    List<T> findAll(Sort sort);
    List<T> findAllById(Iterable<ID> ids);
    <S extends T> List<S> saveAll(Iterable<S> entities);
    void flush();
    <S extends T> S saveAndFlush(S entity);
    void deleteInBatch(Iterable<T> entities);
    void deleteAllInBatch();
    T getOne(ID id);
    <S extends T> List<S> findAll(Example<S> example);
    <S extends T> List<S> findAll(Example<S> example, Sort sort);
}
```

通过源码和 CrudRepository 相比较其支持了 Query By Example、批量删除、提高删除效率、手动刷新数据库的更改方法，将默认实现的查询结果变成了 List。

**JpaRepository 使用方法也一样，只需要继承它即可，如下面的例子：**

```
package com.example.example2.repository;
import com.example.example2.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
public interface UserJpaRepository extends JpaRepository<User,Long> {
}
```

### Repository 的实现类 SimpleJpaRepository

SimpleJpaRepository 是 JPA 整个关联数据库的所有 Repository 的接口实现类，如果想进行扩展，可以继承此类，如 QueryDsl 的扩展，还有默认的处理机制。如果将此类里面的实现方法看透了，基本上 JPA 的 API 就能掌握大部分，同时也是 Spring JPA 的动态代理的实现类，包括我们后面讲的 Query Method。

我们可以通过 Debug 试图看一下动态代理过程，如图：

![enter image description here](http://images.gitbook.cn/368a3460-2abe-11e8-acca-d972d7a925f0)

SimpleJpaRepository 的部分源码如下：

```
@Repository
@Transactional(readOnly = true)
public class SimpleJpaRepository<T, ID> implements JpaRepository<T, ID>, JpaSpecificationExecutor<T> {
    private static final String ID_MUST_NOT_BE_NULL = "The given id must not be null!";
    private final JpaEntityInformation<T, ?> entityInformation;
    private final EntityManager em;
    private final PersistenceProvider provider;
    private @Nullable CrudMethodMetadata metadata;
    ......
    @Transactional
    public void deleteAllInBatch() {
        em.createQuery(getDeleteAllQueryString()).executeUpdate();
    }
    ......
```

可以看出 SimpleJpaRepository 的实现机制还挺清晰的，通过 EntityManger 进行实体的操作，JpaEntityInforMation 里面存着实体的相关信息，还有 crud 方法的元数据等等，后面章节还会经常提到此类，慢慢介绍。