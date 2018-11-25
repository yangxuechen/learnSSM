### spring的核心接口  
**一.beanFactory**  
1.新建一个Student类  
```java
package edu.njxz.demo.bean;

public class Student {
    private String id; //学号
    private String name; //姓名

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
2.在spring的配置文件注入bean  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="student" class="edu.njxz.demo.bean.Student">
        <property name="id" value="1"></property>
        <property name="name" value="yxc"></property>
    </bean>

</beans>
```  
3.通过BeanFactory来获取javaBean  
````java
public class Test1 {
    public static void main(String[] args) {
        
        //初始化spring容器 加载配置文件
        BeanFactory beanFactory=new XmlBeanFactory(new FileSystemResource("E:\\IDEAProjects\\springTest\\src\\main\\resources\\beans.xml"));
        
        //通过容器获取student实例
        Student s= (Student) beanFactory.getBean("student");
        System.out.println(s.getId()+" "+s.getName());
    }
}
````  
**二.ApplicationContext**  
1.通过ClassPathXmlApplicationContext创建  
```java
public class Test1 {
    public static void main(String[] args) {


         ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");

         Student student= (Student) applicationContext.getBean("student");
         System.out.println(student.getId());

    }
}
```  
2.通过FileSystemXmlApplicationContext创建  
```java
 public class Test1 {
     public static void main(String[] args) {
         
           ApplicationContext applicationContext=new FileSystemXmlApplicationContext("E:\\IDEAProjects\\springTest\\src\\main\\resources\\beans.xml");
 
           Student student= (Student) applicationContext.getBean("student");
           System.out.println(student.getId());
 

     }
 }
```  
3.通过web服务器实例化ApplicationContext容器  




