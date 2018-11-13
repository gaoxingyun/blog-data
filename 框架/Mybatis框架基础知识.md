---
title: orm框架基础知识
date: 2017-03-04 20:36:42
categories:
- 框架
tags:
- 框架
- java
---


## Mybatis基础知识

## 文档

- [mybatis3官方文档](http://www.mybatis.org/mybatis-3/zh/index.html)

#### 锁

- 在锁冲突较小的时候，使用乐观锁可以大大增加性能（减少类锁的开销），但是当并发较高，锁冲突特别严重的时候，由于乐观锁需要不断重试，反而会降低性能。另外，乐观锁不仅仅可以使用version字段，也可以使用时间戳，或数据库列字段等任何可以判断数据是否被修改的字段。乐观锁字段按具体业务选择，可增加并发性能，以version字段作为乐观锁，每次只能有一个事务更新成功，会造成大量操作失败，互不冲突的更新操作，宜使用不同的字段作为乐观锁。
##### 乐观锁

- 乐观锁是根据数据库的一个字段，在更新的时候判断数据有没有被改变。sql脚本类似如下：
```sql
update user set name='admin', version=version+1 where id='1' and version=1;
```

#### 缓存

##### 默认特性

1. 映射语句文件中的所有 select 语句将会被缓存。
2. 映射语句文件中的所有 insert,update 和 delete 语句会刷新缓存。
3. 缓存会使用 Least Recently Used(LRU,最近最少使用的)算法来收回。
4. 根据时间表(比如 no Flush Interval,没有刷新间隔), 缓存不会以任何时间顺序 来刷新。
5. 缓存会存储列表集合或对象(无论查询方法返回什么)的 1024 个引用。
6. 缓存会被视为是 read/write(可读/可写)的缓存,意味着对象检索不是共享的,而 且可以安全地被调用者修改,而不干扰其他调用者或线程所做的潜在修改


##### 实现原理

##### 缺点

- 由于二级缓存是在namespace下的，当在不同的namespace下修改数据库时，会导致数据一致性问题。而且当namespace下进行delete，update时，会刷新namespace下所有数据。

##### 缺陷避免

- 使用单表操作，不要联接查询。

## 插件

- [Mybatis最入门---分页查询（拦截器分页原理及实现](https://blog.csdn.net/ABCD898989/article/details/51261163)



## 问题

- 报错  org.mybatis.spring.MyBatisSystemException: nested exception is org.apache.ibatis.builder.xml.IncompleteStatementException: Could not find parameter map java.util.Map  
解决：搜索文件*.xml 搜索词: parameterMap 然后将parameterMap 改为parameterType

#### 缓存问题

```java

import java.lang.annotation.*;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface CacheData {

    /**
     * 缓存key前缀
     *
     * @return
     */
    String value() default "";
}

```

```java

import com.roncoo.pay.common.core.cache.key.CacheKey;
import com.roncoo.pay.common.core.cache.redis.RedisUtils;
import com.roncoo.pay.service.annotation.CacheData;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

@Slf4j
@Aspect
@Component
public class CacheDataAspect {

    @Pointcut("@annotation(com.xxx.pay.service.annotation.CacheData)")
    public void cacheDataAspect() {

    }

    @Around(value = "@annotation(cacheData)")
    public Object around(ProceedingJoinPoint joinPoint, CacheData cacheData) throws Throwable {
        log.debug("cacheData注解切面执行，{}{}", joinPoint.getThis(), cacheData.value());
        String cacheKey = cacheData.value();
        Object[] args = joinPoint.getArgs();
        for (int i = 0; i < args.length; i++) {
            cacheKey = cacheKey + CacheKey.KEY_SEPARATOR + args[i];
        }
        log.debug("cacheData切面获得缓存Key: {}", cacheKey);
        Object value = RedisUtils.get(cacheKey);
        log.debug("cacheData切面获得缓存数据：{}", value);
        if (value != null) {
            log.debug("cacheData切面获得缓存数据不为空，直接返回");
            return value;
        }
        value = joinPoint.proceed();
        log.debug("cacheData切面从方法查询数据：{}", value);
        if (value != null) {
            log.debug("cacheData切面从方法查询数据不为空，保存数据到redis中");
            RedisUtils.save(cacheKey, value);
        }
        return value;
    }
}

```

- 使用
```
@CacheData("profix")
```