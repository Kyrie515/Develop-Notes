# Spring Notes

### IoC in Spring 

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

