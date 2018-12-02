### Mybatis映射器 传递多个参数  
**一.使用Map接口传递多个参数**  
1.数据库操作接口(UserMapper.java)中实现的方法(查询姓氏=user_name 且userId < count 的所有用户 )    
```java
   //使用Map接口传递多个参数
    List<User> selectByParams(Map<String,Object> map);
```  
2.UserMapper.xml文件  
```xml
    <!-- 使用Map接口传递多个参数 查询姓氏=user_name 且userId < count 的所有用户 -->
    <select id="selectByParams" resultMap="BaseResultMap" parameterType="map">
        select * from p_user
        where user_name like concat('%',#{user_name},'%')
        and user_id &lt; #{count}
    </select>
```  
3.测试方法  
```java
@Controller("userController")                                                
public class UserController {                                                
                                                                             
    @Autowired                                                               
    private UserMapper userMapper;                                           
                                                                             
    public void test(){
        //查询多个用户                                                             
        Map<String,Object> map=new HashMap<String, Object>();                
        map.put("user_name","卡");                                            
        map.put("count",50);                                                 
                                                                             
        List<User> userList=userMapper.selectByParams(map);
                                                                             
        for(User u:userList){                                                
            System.out.println(u.getUserId()+" "+u.getUserName());           
        }                                                                    
    }                                                                        
}                                                                            
```  
**二.使用Java Bean传递多个参数**  
1.新建一个JavaBean  
```java
public class SelectUserParam {
    private String user_name;
    private int count;

    public String getUser_name() {
        return user_name;
    }

    public int getCount() {
        return count;
    }

    public void setUser_name(String user_name) {
        this.user_name = user_name;
    }

    public void setCount(int count) {
        this.count = count;
    }
}

```
2.数据库操作的接口类中添加方法  
```java
    //使用Java Bean传递多个参数
    List<User> selectByJavaBean(SelectUserParam param);
```
3.UserMapper.xml  
```xml
    <select id="selectByJavaBean" resultMap="BaseResultMap" parameterType="edu.njxz.demo.springMybatis.bean.SelectUserParam">
         select * from p_user
        where user_name like concat('%',#{user_name},'%')
        and user_id &lt; #{count}
    </select>
```  
4.测试方法  
```java
@Controller("userController")                                               
public class UserController {                                               
                                                                            
    @Autowired                                                              
    private UserMapper userMapper;                                          
                                                                            
    public void test(){                                                     
        
        SelectUserParam param=new SelectUserParam();                        
        param.setUser_name("卡");                                            
        param.setCount(50);                                                 
                                                                            
        List<User> userList=userMapper.selectByJavaBean(param);             
                                                                     
                                                                            
        for(User u:userList){                                               
            System.out.println(u.getUserId()+" "+u.getUserName());          
        }                                                                   
    }                                                                       
}                                                                           
```