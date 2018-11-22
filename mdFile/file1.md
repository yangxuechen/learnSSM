### Intellij IDEA 使用Maven构建spring项目  
1.新建一个Maven项目  
![sp_1.jpg](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/sp_1.jpg)  
项目的结构图  
![sp_2](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/sp_2.jpg)  
2.配置pom.xml,引入项目需要的依赖,这里引入了spring,junit  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>edu.njxz.demo</groupId>
    <artifactId>springTest</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.6.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
    </dependencies>


</project>
```  
3.新建一个People类  
```java
package edu.njxz.demo.bean;

public class People {
    private String name;  //姓名
    private int age;      //年龄

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
4.创建spring的beans.xml,通过spring的配置文件来完成对象的注入  
```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
  
      <bean id="people" class="edu.njxz.demo.bean.People">
          <property name="name" value="yxc"></property>
          <property name="age" value="21"></property>
      </bean>
  </beans>
```  
5.进行测试  
```java
package edu.njxz.demo.test;

import edu.njxz.demo.bean.People;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class Test1 {
    public static void main(String[] args) {

        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");

        //从容器中获取bean
        People people= (People) applicationContext.getBean("people");
        System.out.println(people.getName()+" "+people.getAge());
    }
}

```  
6.控制台的打印结果  
![sp_3](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/sp_3.jpg)