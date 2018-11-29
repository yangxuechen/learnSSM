### 基于XML配置开发AspectJ  
1.pom.xml,添加依赖  
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
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.9</version>
        </dependency>
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.8.8</version>
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
/**
 * 切面类，在此类中编写各种类型的通知
 */
public class MyAspect {

    /**
     * 前置通知，使用JoinPoint接口作为参数获得目标对象信息
     * @param jp
     */
    public void before(JoinPoint jp){
        System.out.print("前置通知：模拟权限控制");
        System.out.println(",目标对象:"+jp.getTarget()+" ,被增强处理的方法："+jp.getSignature().getName());
    }

    /**
     * 后置返回通知
     * @param jp
     */
    public void afterReturning(JoinPoint jp){
        System.out.print("后置返回通知：模拟删除临时文件");
        System.out.println(" ,被增强处理的方法："+jp.getSignature().getName());
    }

    /**
     * 环绕通知
     * @param pjp
     * @return
     * @throws Throwable
     * 必须throws Throwable
     */
    public Object around(ProceedingJoinPoint pjp) throws Throwable{
        //开始
        System.out.println("环绕开始：执行目标方法前，模拟开启事务");
        //执行当前目标方法
        Object obj=pjp.proceed();
        //结束
        System.out.println("环绕结束：执行目标方法后，模拟关闭事务");

        return obj;
    }

    /**
     * 异常通知
     *
     */

    public void except(Throwable e){
        System.out.println("异常通知："+"程序执行异常"+e.getMessage());
    }

    /**
     * 后置通知，（最终通知）
     */
    public void after(){
        System.out.println("最终通知：模拟释放资源");
    }
}

```
5.创建配置文件，并编写相关配置  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!--  AspectJ-->
    <!-- 定义目标对象-->
    <bean id="testDao" class="edu.njxz.demo.dynamicProxy.TestDaoImpl"></bean>
    <!-- 定义切面-->
    <bean id="myAspect" class="edu.njxz.demo.aspectJ.MyAspect"></bean>
    <!-- AOP配置-->
    <aop:config>
        <!-- 配置切面-->
        <aop:aspect ref="myAspect">
            <!-- 配置切入点-->
            <aop:pointcut id="myPointCut" expression="execution(* edu.njxz.demo.dynamicProxy.*.*(..))"></aop:pointcut>
            <!-- 将通知与切入点关联-->
            <!-- 关联前置通知-->
            <aop:before method="before" pointcut-ref="myPointCut"></aop:before>
            <!-- 关联后置返回通知-->
            <aop:after-returning method="afterReturning" pointcut-ref="myPointCut"></aop:after-returning>
            <!-- 关联环绕通知-->
            <aop:around method="around" pointcut-ref="myPointCut"></aop:around>
            <!-- 关联异常通知，没有异常将不会执行增强，throwing属性设置通知第二个参数名称-->
            <aop:after-throwing method="except" pointcut-ref="myPointCut" throwing="e"></aop:after-throwing>
            <!-- 关联后置通知，不管目标方法是否成功都要执行-->
            <aop:after method="after" pointcut-ref="myPointCut"></aop:after>
        </aop:aspect>
    </aop:config>
</beans>
```  
6.创建测试类  
```java
public class Test {

    public static void main(String[] args) {
        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("app1.xml");

        //从容器中获取增强后的目标对象
        TestDao testDaoAdvice =(TestDao) applicationContext.getBean("testDao");

        //执行方法
        testDaoAdvice.save();
    }

}
```
7.测试运行结果  
![aspectREsult](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/aspectJResult.jpg)