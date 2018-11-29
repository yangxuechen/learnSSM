### 基于注解开发AspectJ  
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
@Repository("testDao")
public class TestDaoImpl  implements TestDao{
    public void save() {
        System.out.println("保存");
    }

    public void delete() {
        System.out.println("删除");
    }
}
``` 
4.创建切面类，并进行注解  
```java

@Aspect  //对应<aop:aspect ref="myAspect">
@Component  //对应<bean id="myAspect" class="">
public class MyAspect {

    /**
     * 定义切入点  @Pointcut 相当于  <aop:pointcut id="myPointCut" expression="execution(* edu.njxz.demo.dynamicProxy.*.*(..))"></aop:pointcut>
     */
    @Pointcut("execution(* edu.njxz.demo.dynamicProxy.*.*(..))")
    private void myPointCut(){

    }
    /**
     * 前置通知，使用JoinPoint接口作为参数获得目标对象信息
     * @param jp
     */
    @Before("myPointCut()")
    public void before(JoinPoint jp){
        System.out.print("前置通知：模拟权限控制");
        System.out.println(",目标对象:"+jp.getTarget()+" ,被增强处理的方法："+jp.getSignature().getName());
    }

    /**
     * 后置返回通知
     * @param jp
     */
    @AfterReturning("myPointCut()")
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
    @Around("myPointCut()")
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

    @AfterThrowing(value = "myPointCut()",throwing = "e")
    public void except(Throwable e){
        System.out.println("异常通知："+"程序执行异常"+e.getMessage());
    }

    /**
     * 后置通知，（最终通知）
     */
    @After("myPointCut()")
    public void after(){
        System.out.println("最终通知：模拟释放资源");
    }
}

``` 
5.创建配置文件  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">

    <!-- 指定需要扫描的包，是注解生效-->
    <context:component-scan base-package="edu.njxz.demo.aspectAnnotation"></context:component-scan>
    <context:component-scan base-package="edu.njxz.demo.dynamicProxy"></context:component-scan>
    <!-- 启动基于注解的AspectJ支持-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
```
6.测试运行  
```java
public class Test {

    public static void main(String[] args) {
        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("app2.xml");

        //从容器中获取增强后的目标对象
        TestDao testDaoAdvice =(TestDao) applicationContext.getBean("testDao");

        //执行方法
        testDaoAdvice.save();
    }

}
```
