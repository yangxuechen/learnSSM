### spring基于注解的装配  
1.创建bean的实现类  
```java
@Component()
public class Teacher {
    @Value("t1")
    private String id;
    @Value("James")
    private String name;

    public String getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public void setId(String id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```  
2.配置注解  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <!-- 使用context命名空间，通过spring扫描指定包及其所有子包下所有bean的实现类，进行注解解析-->
    <context:component-scan base-package="edu.njxz.demo.bean"/>

</beans>
```  
3.测试bean实例  
```java
public class Test1 {
    public static void main(String[] args) {

        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");
        Teacher teacher= (Teacher) applicationContext.getBean("teacher");
        System.out.println(teacher.getId());
    }
}
```