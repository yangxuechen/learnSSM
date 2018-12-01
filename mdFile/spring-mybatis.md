### spring整合mybatis  
1.使用Maven构建java项目，修改pom.xml文件，添加所需的依赖jar包  
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
        <!-- spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>5.0.6.RELEASE</version>
        </dependency>
        <!-- junit测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>
        <!-- 数据库连接驱动-->
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
        <!-- mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.2</version>
        </dependency>
        <!-- mybatis与spring整合的中间jar-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.2</version>
        </dependency>
        <!-- 数据源-->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.2.2</version>
        </dependency>

    </dependencies>


</project>
```  
2.创建实体类，其中实体类的属性名与表字段对应  
```sql
CREATE TABLE `p_user` (
  `user_id` int(11) NOT NULL AUTO_INCREMENT,
  `user_name` varchar(20) DEFAULT NULL,
  `user_pwd` varchar(20) DEFAULT NULL,
  `user_email` varchar(30) DEFAULT NULL,
  PRIMARY KEY (`user_id`)
) ENGINE=InnoDB AUTO_INCREMENT=26 DEFAULT CHARSET=utf8;
```
```java
package edu.njxz.demo.springMybatis.bean;

public class User {
    private int userId;
    private String userName;
    private String userPwd;
    private String userEmail;

    public int getUserId() {
        return userId;
    }

    public String getUserName() {
        return userName;
    }

    public String getUserPwd() {
        return userPwd;
    }

    public String getUserEmail() {
        return userEmail;
    }

    public void setUserId(int userId) {
        this.userId = userId;
    }

    public void setUserName(String userName) {
        this.userName = userName;
    }

    public void setUserPwd(String userPwd) {
        this.userPwd = userPwd;
    }

    public void setUserEmail(String userEmail) {
        this.userEmail = userEmail;
    }
}

```  
3.创建UserMapper接口  
```java
@Repository("userMapper")
@Mapper
public interface UserMapper {
    public int insertUser(User user);
    public List<User> selectAllUsers();
}
```  
4.创建UserMapper.xml文件(因为使用Maven构建项目，将UserMapper.xml文件放在resource文件夹下，如果放在其他目录下，最后运行项目会报找不到该文件的错误)  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="edu.njxz.demo.springMybatis.mapper.UserMapper">

  <resultMap id="BaseResultMap" type="edu.njxz.demo.springMybatis.bean.User">
    <id column="user_id" jdbcType="INTEGER" property="userId" />
    <result column="user_name" jdbcType="VARCHAR" property="userName" />
    <result column="user_pwd" jdbcType="VARCHAR" property="userPwd" />
    <result column="user_email" jdbcType="VARCHAR" property="userEmail" />
  </resultMap>
     <insert id="insertUser" parameterType="edu.njxz.demo.springMybatis.bean.User">
       insert into p_user(user_name,user_pwd,user_email) values (#{userName},#{userPwd},#{userEmail})
     </insert>

     <select id="selectAllUsers" resultMap="BaseResultMap" >
       select * from p_user
     </select>
</mapper>
```  
5.创建mybatis配置文件mybatis-config.xml(同样放在resource文件夹目录下)  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- 告诉Mybatis去哪找映射文件-->
    <mappers>
        <mapper resource="mapper/UserMapper.xml"></mapper>

    </mappers>
</configuration>
```  
6.创建spring配置文件app3.xml  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:tx="http://www.springframework.org/schema/tool"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd">

    <!-- spring整合mybatis-->

    <!-- 指定需要扫描的包（包括子包）使注解生效-->
    <context:component-scan base-package="edu.njxz.demo.springMybatis"></context:component-scan>
    <!-- 配置数据源-->
    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">

        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/pblog?characterEncoding=utf8"></property>
        <property name="username" value="root"></property>
        <property name="password" value="Root"></property>
        <!-- 最大连接数-->
        <property name="maxWait" value="30"></property>
        <property name="maxIdle" value="10"></property>
        <property name="initialSize" value="5"></property>
    </bean>

    <!-- 添加事物支持-->
    <bean id="txManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
    <!-- 开启事务注解-->

    <!-- 配置Mybatis工厂，同时指定数据源，并与Mybatis完美整合-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
        <property name="configLocation" value="classpath:mybatis-config.xml"></property>
    </bean>

    <!-- Mapper代理开发，使用spring自动扫描Mybatis的接口并装配
      spring将指定包中所有被@Mapper注解标注的接口自动装配为Mybatis的映射接口
    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!-- mybatis组件的扫描器-->
        <property name="basePackage" value="edu.njxz.demo.springMybatis.mapper"></property>
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"></property>
    </bean>
</beans>
```  
7.创建一个UserController来测试UserMapper中定义的方法是否能有效执行  
```java
@Controller("userController")
public class UserController {

    @Autowired
    private UserMapper userMapper;

    public void test(){
        User user=new User();
        user.setUserName("卡哇伊");
        user.setUserEmail("133@qq.com");
        user.setUserPwd("123456");
        //添加一个用户
        userMapper.insertUser(user);

        //查询所有用户
        List<User> userList=userMapper.selectAllUsers();
        for(User u:userList){
            System.out.println(u.getUserName());
        }
    }
}

```  
8.定义测试类，进行测试运行  
```java
public class Test {
    public static void main(String[] args) {
        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("app3.xml");

        UserController uc=(UserController) applicationContext.getBean("userController");
        uc.test();
    }
}
```  
9.运行结果图  
![spring-mybatis](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/spring-mybatis.jpg)  
