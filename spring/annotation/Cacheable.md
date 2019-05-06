# Spring注解解读系列-Cacheable

spring 3.1增加了对cache的支持，使开发者方便的处理业务cache，其中最常用的便是Cacheable了。

先来预览一下这个注解

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Cacheable{
}
```

其中`@Target({ElementType.METHOD, ElementType.TYPE})` 表示该注解应用在class或者method上。该方法作用在method上表示该method支持cacheable，当作用在class上时表明该class的所有方法都支持cacheable。当一个方法支持cache时，spring会在第一次调用该方法时将该方法的返回值缓存起来，下次再调用时直接从缓存获取而不需要调用方法了。

1. value

```java
@AliasFor("cacheNames")
String[] value() default {};
```

value是annotation的默认属性，例如`Cacheable("xxx")`就表示value/cacheNames = xxx

2. cacheNames

```java
@AliasFor("value")
String[] cacheNames() default {};
```

表示该值要存入哪个Cache，是必填字段，也可以是数组。

3. key

```java
String key() default "";
```

key，即存入cache中的key。支持spEL表达式，当key为空时有默认的生成策略，见属性`keyGenerator`。下面我们来看几个case。

```java
// 1. #参数名称
@Cacheable(value = "cache", key = "#id")
public User getUser(String id) 
// 2. #p参数索引(从0开始)
@Cacheable(value = "cache", key = "#p0")
public User getUser(String id)
// 3. 支持参数为符合对象
@Cacheable(value = "cache", key = "#user.id")
public User getUser(User user) 
// 4. root对象
@Cacheable(value = "cache", key = "#root.methodName")
@Cacheable(value = "cache", key = "#user.method.name")
@Cacheable(value = "cache", key = "#user.args[0]")
```

4. keyGenerator

```java
String keyGenerator() default "";
```

当key为空时，使用keyGenerator来生成key，keyGenerator是KeyGenerator的一个bean。默认生成策略是：

```java
// 1. 无参数 key=0
// 2. 有一个参数 key=参数
// 3. 多个参数 key=所有参数的hashcode
@see org.springframework.cache.interceptor.DefaultKeyGenerator
```

