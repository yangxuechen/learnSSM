### 基于代理类的Aop实现  
1.pom.xml文件  
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
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.47</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.2.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>commons-logging</groupId>
            <artifactId>commons-logging</artifactId>
            <version>1.2</version>
        </dependency>
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
    </dependencies>


</project>
```
2.创建目标类接口  
```java
public interface TestDao {
    void save();
    void delete();
}
```  
3.实现接口类  
```java
public class TestDaoImpl  implements TestDao{
    public void save() {
        System.out.println("保存");
    }

    public void delete() {
        System.out.println("删除");
    }
}
```  
4.创建切面类  
```java
public class MyAspect implements MethodInterceptor {
    public Object invoke(MethodInvocation invocation) throws Throwable {
                //增强方法
        check();
        except();

        //执行目标方法
        Object obj=invocation.proceed();

        //增强方法
        log();
        minitor();

        return obj;

    }
    

    public void check(){
        System.out.println("模拟权限控制");
    }

    public void except(){
        System.out.println("模拟异常处理");
    }

    public void log(){
        System.out.println("模拟日志记录");
    }

    public void minitor(){
        System.out.println("模拟性能检测");
    }
}
```  
5.配置切面并指定代理  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--定义目标对象 -->
    <bean id="testDao" class="edu.njxz.demo.dynamicProxy.TestDaoImpl">
    </bean>

    <!-- 创建一个切面-->
    <bean id="myAspect" class="edu.njxz.demo.springAop.MyAspect">
    </bean>

    <!-- 使用spring代理工厂定义一个名为testDaoProxy的代理对象-->
    <bean id="testDaoProxy" class="org.springframework.aop.framework.ProxyFactoryBean">
        <!-- 指定代理实现的接口-->
        <property name="proxyInterfaces" value="edu.njxz.demo.dynamicProxy.TestDao"></property>
        <!-- 指定目标对象-->
        <property name="target" ref="testDao"></property>
        <!-- 指定切面，织入环绕通知-->
        <property name="interceptorNames" value="myAspect"></property>
        <!-- 指定代理方式，true &ndash;&gt;CGLIB动态代理 默认false,指定JDK动态代理 -->
        <property name="proxyTargetClass" value="true"></property>
    </bean>
</beans>
```  
6.创建测试类  
```java
public class Test {
    public static void main(String[] args) {
        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("applicationContext.xml");

        //从容器中获取增强后的目标对象
        TestDao testDaoAdvice =(TestDao)applicationContext.getBean("testDaoProxy");

        //执行方法
        testDaoAdvice.save();
        System.out.println("***************");
        testDaoAdvice.delete();
    }
}
```  
7.测试运行结果  
![springAopResult](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/springAopResult.jpg)  
