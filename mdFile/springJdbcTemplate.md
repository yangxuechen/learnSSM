### Spring JdbcTemplate  
1.新建Maven项目，配置pom.xml，引入需要的jar包  
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
    </dependencies>


</project>
```  
2.创建并编辑配置文件  
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

     <!--使用context命名空间，通过spring扫描指定包及其所有子包下所有bean的实现类，进行注解解析-->
    <context:component-scan base-package="edu.njxz.demo"/>

    <!-- 配置数据源-->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <!-- Mysql数据库驱动-->
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <!-- 连接数据库的url-->
        <property name="url" value="jdbc:mysql://localhost:3306/pblog?characterEncoding=utf8"></property>
        <!-- 连接数据库的用户名-->
        <property name="username" value="root"></property>
        <!-- 连接数据库的密码-->
        <property name="password" value="Root"></property>
    </bean>

    <!-- 配置Jdbc模板-->
    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
        <property name="dataSource" ref="dataSource"></property>
    </bean>

</beans>
```
3.创建实体类  
```java
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
4.创建数据访问层Dao  
```java
public interface UserDao {
    int update(String sql,Object[] param);
    List<User> query(String sql,Object[] param);
}
```
5.实现UserDao的接口类  
```java
@Repository("userDao")
public class UserDaoImpl implements UserDao {

    //使用配置文件中的jdbc模板
    @Autowired
    private JdbcTemplate jdbcTemplate;

    /**
     * 更新方法
     * @param sql
     * @param param
     * @return
     */
    public int update(String sql, Object[] param) {
        return jdbcTemplate.update(sql,param);
    }

    /**
     * 查询方法
     * @param sql
     * @param param
     * @return
     */
    public List<User> query(String sql, Object[] param) {
        RowMapper<User> rowMapper=new BeanPropertyRowMapper<User>(User.class);

        return jdbcTemplate.query(sql,rowMapper,param);
    }
}

``` 
6.创建测试类  
```java
public class Test {
    public static void main(String[] args) {
        //把beans.xml的类加载到容器
        ApplicationContext applicationContext=new ClassPathXmlApplicationContext("beans.xml");

        //从容器获取目标对象
        UserDao userDao= (UserDao) applicationContext.getBean("userDao");

        String insertSql="insert into p_user(user_name,user_pwd,user_email) values(?,?,?) ";
        Object param1[]={"神龟","123456","for@163.com"};

        userDao.update(insertSql,param1);

        String selectSql="select * from p_user ";
        List<User> userList=userDao.query(selectSql,null);
        for(User user:userList){
            System.out.println(user.getUserId()+" "+user.getUserName());
        }
    }
}

```  
7.运行结果  
![springJdbcTemplate](https://github.com/yangxuechen/learnSSM/blob/master/resource/sping-learn-images/springJdbcTemplate.jpg)  
