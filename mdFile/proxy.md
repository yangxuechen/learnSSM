### java的静态代理和动态代理  

**代理模式**  
代理是一种模式，提供了对目标对象的间接访问方式，即通过代理访问目标对象。如此便于在目标实现的基础上增加额外的功能操作，前拦截，后拦截等，以满足自身的业务需求，同时代理模式便于扩展目标对象功能的特点也为多人所用。  
![proxy](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/proxy.jpg)  
**一.静态代理**  
1.新建目标对象实现的接口  
```java
//目标对象实现的接口
public interface BussinessInterface {
    //业务方法
    void bussiness();
}
```  
2.接口实现类  
```java
//目标对象实现类
public class Bussiness implements BussinessInterface {
    public void bussiness() {
        System.out.println("执行业务逻辑方法...");
    }
}
```  
3.代理类  
```java
public class BussinessProxy implements BussinessInterface {
    BussinessInterface bussinessImpl;

    public BussinessProxy(BussinessInterface bussinessImpl) {
        this.bussinessImpl = bussinessImpl;
    }

    public void bussiness() {
        System.out.println("前拦截...");
        bussinessImpl.bussiness();
        System.out.println("后拦截...");
    }
}

```  
4.测试运行  
```java
public class Test {
    public static void main(String[] args) {

        //使用目标对象来完成业务操作
        BussinessInterface bussiness=new Bussiness();
        bussiness.bussiness();
        
        System.out.println("********************");

        //通过代理类来完成业务操作，在不对目标对象改造的情况下对目标对象的功能进行扩充
        BussinessInterface bussiness2=new BussinessProxy(bussiness);
        bussiness2.bussiness();
    }
}
```  
运行结果  
![result](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/result.jpg)  
**二.JDK动态代理**  
1.创建接口及实现类  
```java
public interface TestDao {
    void save();
    void delete();
}

```  
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
2.创建切面类  
```java
public class MyAspect {
    public void check(){
        System.out.println("模拟权限控制");
    }
    public void except(){
        System.out.println("模拟异常处理");
    }
    public void log(){
        System.out.println("模拟日志记录");
    }
    public void monitor(){
        System.out.println("性能检测");
    }
}
```  
3.创建代理类  
```java
public class JDKDynamicProxy implements InvocationHandler {

    //声明目标类接口对象(真实对象)
    private TestDao testDao;

    //创建代理的方法 建立代理对象和真实对象的代理关系 ，并返回代理对象
    public Object createProxy(TestDao testDao){
        this.testDao=testDao;

        //1.类加载器
        ClassLoader cld=JDKDynamicProxy.class.getClassLoader();
        //2.被代理对象实现的所有接口
        Class[] clazz=testDao.getClass().getInterfaces();
        //3.使用代理对象进行增强，返回代理后的对象
        return Proxy.newProxyInstance(cld,clazz,  this);
    }

    public Object invoke(Object o, Method method, Object[] objects) throws Throwable {
        //创建一个切面
        MyAspect myAspect=new MyAspect();
        //前增强
        myAspect.check();
        myAspect.except();
        //在目标类上调用方法并传入参数，相当于调用testDao中的方法
        Object obj=method.invoke(testDao,objects);
        //后增强
        myAspect.log();
        myAspect.monitor();
        return obj;
    }
}
```  
4.创建测试类  
```java
public class Test {
    public static void main(String[] args) {
        //创建代理对象
        JDKDynamicProxy jdkDynamicProxy=new JDKDynamicProxy();
        //创建目标对象
        TestDao testDao=new TestDaoImpl();

        //从代理对象中获取增强后的目标对象，该对象是一个被代理的对象
        TestDao testDaoAdvice=(TestDao) jdkDynamicProxy.createProxy(testDao);

        //执行方法
        testDaoAdvice.save();
        System.out.println("**********************");
        testDaoAdvice.delete();
    }
}
```  
5程序运行结果图  
![jdkDynamicProxy](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/jdkDynamicProxy.jpg)  



