## Multi-table query using resultMap in Mybatis

### 1. One to One Query in Mybatis

`resultMap` is a powerful tag in Mybatis mapper config and we can use it to query for more than one table and associate the results by their primary keys. For example, I have two Mysql tables named `account` and `user` and two Java class for both:

`user`

```sql
create table user
(
    id       int auto_increment
        primary key,
    username varchar(50) not null,
    password varchar(50) not null
)
```

`account`

```sql
create table account
(
    id    int    not null comment '编号'
        primary key,
    uid   int    null comment '用户编号',
    money double null comment '金额',
    constraint FK_Reference
        foreign key (uid) references user (id)
)
```

class `User`

```java
@Data
public class User implements Serializable {
    private Integer id;
    private String username;
    private String password;
}
```

class `Account`

```java
@Data
public class Account implements Serializable {
    private Integer id;
    private Integer uid;
    private Double money;
    private User user;
}
```

we can figure out that table`account` is associated to the table`user` by the foreign key `account(uid)`.So if we want to find out all the information about an account(include its user, username and password), we can achieve this easily in `SQL`:

```sql
select u.*, a.id as aid, a.uid, a.money from account a, user u where u.id = a.uid;
```

But if we want to achieve this in Mybatis, we must use `resultMap`. The following is the config of a resultMap for the example.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dao.AccountMapper">
    <resultMap id="accountUserMap" type="domain.Account">
        <id property="id" column="aid"></id>
        <result property="uid" column="uid"></result>
        <result property="money" column="money"></result>
        <association property="user" column="uid" javaType="domain.User">
            <id property="id" column="id"></id>
            <result property="username" column="username"></result>
            <result property="password" column="password"></result>
        </association>
    </resultMap>
    <select id="findAll" resultMap="accountUserMap">
        select u.*, a.id as aid, a.uid, a.money from account a, user u where u.id = a.uid;
    </select>
</mapper>
```

The tag `resultMap` has many attributes:

- `id`: A unique identifier in this namespace that can be used to reference this result map.
- `type`:  This property indicates which class the entire resultMap is wrapped into



The tag `resultMap` has two common sub-tags:

- `id & result`: These are the most basic of result mappings. Both *id* and *result* map a single column value to a single property or field of a simple data type (String, int, double, Date, etc.).

  The only difference between the two is that *id* will flag the result as an identifier property to be used when comparing object instances. This helps to improve general performance, but especially performance of caching and nested result mapping (i.e. join mapping)

 Both of these two have the following attributes:

- `property`: A field of a JavaBean that will be mapped to a column.
- `column`: A column of the results of the SQL query.

One of the sub-tags that enables the `resultMap` to achieve union-query is `association`. It has the following attributes:

- `property`: It indicates the filed of a JavaBean which will be associated.
- `column`: It indicates the field of the JavaBean that can be passed to enable the union-query.
- `javaType`: It indicates this **association** could be mapped to which Java class.

Mybatis also support annotation development, for example:

```java
package dao;

import domain.Account;
import org.apache.ibatis.annotations.One;
import org.apache.ibatis.annotations.Result;
import org.apache.ibatis.annotations.Results;
import org.apache.ibatis.annotations.Select;

import java.util.List;

public interface AccountMapper {

    @Select("select * from account")
    @Results(id = "accountMap", value = {
            @Result(id = true, column = "id", property = "id"),
            @Result(column = "money", property = "money"),
            @Result(column = "uid", property = "uid"),
            @Result(column = "uid", property = "user", one = @One(select = "dao.UserMapper.findById"))
    })
    List<Account> findAll();
}
```



### 2. One-to-Many Query in Mybatis

Let's think about how to query all the information of a `User` class instance which is defined like this:

class `User`:

```java
@Data
public class User implements Serializable {
    private Integer id;
    private String username;
    private String password;
    List<Account> accounts;
}
```

Because there is a situation where one user could have more than one account, class `User` should have a member variable named accounts. And if we want to query all the information of a user using the following SQL:

```sql
select * from user u left outer join account a on u.id = a.uid;
```

we should write a `UserMapper.xml`like this:

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="dao.UserMapper">
    <resultMap id="userAccountMap" type="domain.User">
        <id property="id" column="id"></id>
        <result property="username" column="username"></result>
        <result property="password" column="password"></result>
        <collection property="accounts" ofType="domain.Account">
            <id property="id" column="aid"></id>
            <result property="uid" column="uid"></result>
            <result property="money" column="money"></result>
        </collection>
    </resultMap>

    <select id="findAll" resultMap="userAccountMap">
        select * from user u left outer join account a on u.id = a.uid;
    </select>
</mapper>
```

The new sub-tag of `resultMap` is `collection`.It should be configured by using two attributes:

- `property`: It indicates which member variable of class `User` contains multiple results.
- `ofType`: It indicates the multiple results of the collection belongs to which Java class.

### 3. Many-to-Many Query in Mybatis

To achieve this, we must focus on SQL instead of Mybatis syntax.

### 4. SqlMapConfig.xml Configuration

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org.dtd/mybatis-3-config.dtd">

<configuration>
    <environments default="mysql">
        <environment id="mysql">
            <transactionManager type="JDBC"></transactionManager>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/JavaTester"/>
                <property name="username" value=${username}/>
                <property name="password" value=${password}/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```

