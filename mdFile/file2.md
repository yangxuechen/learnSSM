## spring的两种注入方式  

### 一.设值注入  
1.新建一个接口类  
```java
public interface Behaviour {
    public String learn();
}
``` 
2.实现新建的接口类  
```java
  public class Learn implements Behaviour {
      public String learn() {
          return "人会学习";
      }
  }
```  
3.新建一个People对象  
```java
  public class People {
      private String name;  //姓名
      private int age;      //年龄
      private Behaviour behaviour;  //行为
  
      public void canDo(){
          System.out.println(behaviour.learn());
      }
  
      //set方法
      public void setBehaviour(Behaviour behaviour) {
          this.behaviour = behaviour;
      }
  
      public String getName() {
          return name;
      }
  
      public int getAge() {
          return age;
      }
  
      public void setName(String name) {
          this.name = name;
      }
  
      public void setAge(int age) {
          this.age = age;
      }
  }

```  
4.配置spring的配置文件，  
```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
 
     <bean id="people" class="edu.njxz.demo.bean.People">
         <property name="name" value="yxc"></property>
         <property name="age" value="21"></property>
         <property name="behaviour" ref="learn"></property>
     </bean>
 
     <bean name="learn" class="edu.njxz.demo.bean.Learn"></bean>
 
 </beans>
```  
5.测试运行  
```java
public class Test1 {
    public static void main(String[] args) {

        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");

        //从容器中获取bean
        People people= (People) applicationContext.getBean("people");
        System.out.println(people.getName()+" "+people.getAge());
        people.canDo();
    }
}
```  
6.运行效果图  
![sp_4](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/sp_4.jpg)  
### 二.构造注入  
1.还是上面的代码，将People类进行简单的修改  
```java
package edu.njxz.demo.bean;

public class People {
    private String name;  //姓名
    private int age;      //年龄
    private Behaviour behaviour;  //行为

    public void canDo(){
        System.out.println(behaviour.learn());
    }


    
    //构造方法
   public People(Behaviour behaviour){
        this.behaviour=behaviour;
   }
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}

```    
2.将配置文件进行简单的修改  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="people" class="edu.njxz.demo.bean.People">
        <property name="name" value="yxc"></property>
        <property name="age" value="21"></property>
        <!--<property name="behaviour" ref="learn"></property>-->
        <!--使用构造注入，为People实例注入Behavior实例 -->
        <constructor-arg ref="learn"></constructor-arg>
    </bean>

    <bean name="learn" class="edu.njxz.demo.bean.Learn"></bean>

</beans>
```  
3.运行效果图  
![sp_4](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/sp_4.jpg)  
