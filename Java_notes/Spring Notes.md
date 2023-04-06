# Spring Notes

### 1. IoC in Spring 

The main purpose of IoC is resolving inter-procedural couplings.

Below is a `ApplicationContext.xml` of Spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="accountService" class="service.impl.AccountServiceImpl"/>
    <bean id="accountDao" class="dao.impl.IAccountDaoImpl"/>
</beans>
```

Now we can use Spring IoC to get the bean object by their ids.

```java
package ui;


import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import service.IAccountService;
import service.impl.AccountServiceImpl;

/**
 * get core container of spring IoC
 */
public class Client {
    public static void main(String[] args) {
        //1. get core container object
        ApplicationContext ac = new ClassPathXmlApplicationContext("bean.xml");
        //2. get bean instance by id in bean.xml
        IAccountService accountService = (IAccountService) ac.getBean("accountService");
        accountService.saveAccount();

    }
}
```

Three implementation class of `ApplicationContext`:

- `ClassPathXmlApplicationContext`:It can load the configuration file under the class path, requiring that the configuration file must be under the class path.(recommended)
- `FileSystemXmlApplicationContext`：It can load a configuration file in any path, but it must have access rights.
- `AnnotationConfigApplicationContext`：It reads the annotation to create the container.

### 2. Management of Bean objects in Spring

#### a) Three Ways to Create Bean

The first way is using default constructor to create. If the class does not have a default constructor, then we can not create it.

```xml
<bean id="accountService" class="service.impl.AccountServiceImpl"></bean>
```

The second way is using factory method to create bean.

Assume there is a factory class:

```java
public class InstanceFactory {
    public IAccountService getInstance() {
        return new AccountServiceImpl();
    }
}
```

and we want to use this method to get a instance of `AccountServiceImpl`, then we could config this in `bean.xml`:

```xml
<bean id="instanceFactory" class="factory.InstanceFactory"></bean>
<bean id="accountService" factory-bean="instanceFactory" factory-method="getInstance"/>
```

The third way is using static factory's static method to create.

#### b) The Scope of Bean 

We can use the attribute `scope` of tag `bean` in `bean.xml` to adjust the scope of bean. The following values are available for this attribute:

- `singleton`: It guarantees the created bean is a singleton.
- `prototype`: It guarantees the created bean is multi-instance.
- `request`:It acts on the request scope of Web application.
- `session`:It acts on the session scope of Web application.
- `global-session`:It acts on the session scope of a cluster Web application.

#### c) The Life Circle of a Bean Object

The life circle of singleton is the same as spring container(`ApplicationContext`). If the container is alive, then the bean is alive; once the container is destroyed, then the bean is destroyed. The muti-instance bean object is different from this.

For muti-instance bean object, Spring framework creates it on lazy-load mode. This means the framework creates it for us when we use it. If we keep using the object, then the object is alive; after that, the object is destroyed by GC.



