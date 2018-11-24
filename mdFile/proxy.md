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


