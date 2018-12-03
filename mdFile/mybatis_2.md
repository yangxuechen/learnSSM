### \<insert\>元素   
**主键(自动递增)回填**  
```xml
     <!-- 添加一个用户，成功后将主键值回填给user对象的属性userId-->
     <insert id="insertUser" parameterType="edu.njxz.demo.springMybatis.bean.User" keyProperty="userId" useGeneratedKeys="true">
       insert into p_user(user_name,user_pwd,user_email) values (#{userName},#{userPwd},#{userEmail})
     </insert>
``` 
**自定义主键**  
```xml
 <!-- 自定义主键，当数据库不支持主键递增或者取消了主键递增-->
    <insert id="insertUser" parameterType="edu.njxz.demo.springMybatis.bean.User">
       <!-- 先使用selectKey元素定义主键 然后在定义sql-->
        <selectKey>
            select if(max (userId) is null 1,max (userId)+1 as newUserId from p_user )
        </selectKey>
       insert into p_user(user_id,user_name,user_pwd,user_email) values (#{userId},#{userName},#{userPwd},#{userEmail})
     </insert>
```

