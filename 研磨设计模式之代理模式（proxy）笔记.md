# 研磨设计模式之代理模式（proxy）笔记

## 1.场景问题

我们需要访问多字段大量的数据且很难避免，返回的字段多量也大很消耗内存，那么我们可不可以只返回我们很少的字段找到需要的数据我们我们在查看整条字段？如果不用模式那会返回所有数据。

## 2.使用代理模式解决问题

###### 定义：为其他对象提供一种代理以控制对象的访问。

解决思路：由于访问的多条数据，所以可以根据一个字段来返回数据。然后再根据需要去具体查询剩下的字段。

****

![无标题](C:\Users\Administrator\Desktop\无标题.png)

*******

Proxy:代理对象

Subject:目标接口

realsubject:具体的目标对象

```java
**代码示例**
public interface UserModelApi{
     public String getUserId() ;
     public void setUserId(String userId) ;
     public String getUserName() ;
     public void setUserName(String userName) ;
     public String getDepId() ;
     public void setDepId(String depId) ;
     public String getSex() ;
     public void setSex(String sex) ;
}
---------------------------------------------------------------------------------------------------
public class Proxy implements UserModelApi {
    /**
     * 持有被代理的具体的目标对象
     */
    private RealSubject realSubject = null;

    /**
     * 构造方法，传入被代理的具体的目标对象
     * @param realSubject
     */
    public Proxy(RealSubject realSubject){
        this.realSubject = realSubject;
    }
    private boolean loaded =false;
     public String getUserId() {
         return realSubject.getuserId();
     }
 
     public void setUserId(String userId) {
         realSubject.setUserId(userId);
     }
 
     public String getUserName() {
         return realSubject.getuserName();
     }
 
     public void setUserName(String userName) {
         realSubject.setuserName(userName);
     }
 
     public String getDepId() {
         if(!this.loaded){
             reload();
             this.loaded = true;
         }
         return realSubject.getdepId();
     }
 
     public void setDepId(String depId) {
         realSubject.setdepId (depId);
     }
 
     public String getSex() {
         if(!this.loaded){
             reload();
             this.loaded = true;
         }
         return realSubject.getsex();
     }
 
     public void setSex(String sex) {
         realSubject.setsex (sex);
     }
    ---------------------------------------------------------------------------------------
    private void reloaded(){
        System.out.println("重新输入查询数据库获取完整的数据，userId="+realSubject.getUserId());
        Connection conn = null;
        try{
            conn = this.getConnection();
            String sql ="select *from tbl_user ehere userId=?";
            PreparedStatement pstmt = conn.prepareStatement(sql);
            pstmt.setString (1,realSubject.getUserId());
            ResultSet rs = pstmt.executeQuery();
            if(rs.next){
                realSubject.setDepId(rs.getString("depid"));
                realSubject.setSex(rs.getString("sex"));
            }
            rs.close();
            pstmt.close();
        }catch(Exception err){
            err.printStackTrace();
        }finally{
            try{
                conn.close();
            }catch(SQLException a){
                a.printStrackTrace();
            }
        }
    }
     public String toString() {
         return 
                 "userId='" + getUserId() + '\'' +
                 ", userName='" + getUserName() + '\'' +
                 ", depId='" + getDepId() + '\'' +
                 ", sex='" + getSex() + '\'' +
    }
     private Connection getConnection() throws Exception{
        Class.forName("com.mysql.jdbc.Driver");
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/test","root",//改成自己的账号密码
                "root");
    }
   ------------------------------------------------------------------------------------------------
       public class UserManager {

    public Collection<UserModel> getUserByDepId(String depId){

        Collection<UserModel> col = new ArrayList<UserModel>();
        Connection conn = null;
        try {
            conn = this.getConnection();
            String sql = "select * from tbl_user u, tbl_dep d " +
                    "where u.depId=d.depId and d.depId like ?";
            PreparedStatement preparedStatement = conn.prepareStatement(sql);
            preparedStatement.setString(1,depId + "%");

            ResultSet rs = preparedStatement.executeQuery();
            while(rs.next()){
                UserModel um = new UserModel();
                um.setUserId(rs.getString("userId"));
                um.setUserName(rs.getString("name"));
                um.setDepId(rs.getString("depId"));
                um.setSex(rs.getString("sex"));

                col.add(um);
            }
            rs.close();
            preparedStatement.close();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        return col;
    }

    private Connection getConnection() throws Exception{
        Class.forName("com.mysql.jdbc.Driver");
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/test","root",//改成自己的账号密码
                "root");
    }
}
   ------------------------------------------------------------------------------------------------
    public class Client {
        public static void main(String[] args){
        UserManager userManager = new UserManager();
        Collection<UserModel> col = userManager.getUserByDepId("0101");
        System.out.println(col);
    }
}
    
    
    
    
    public class UserManager {

    public Collection<UserModel> getUserByDepId(String depId){

        Collection<UserModel> col = new ArrayList<UserModel>();
        Connection conn = null;
        try {
            conn = this.getConnection();
            String sql = "select * from tbl_user u, tbl_dep d " +
                    "where u.depId=d.depId and d.depId like ?";
            PreparedStatement preparedStatement = conn.prepareStatement(sql);
            preparedStatement.setString(1,depId + "%");

            ResultSet rs = preparedStatement.executeQuery();
            while(rs.next()){
                UserModel um = new UserModel();
                um.setUserId(rs.getString("userId"));
                um.setUserName(rs.getString("name"));
                um.setDepId(rs.getString("depId"));
                um.setSex(rs.getString("sex"));

                col.add(um);
            }
            rs.close();
            preparedStatement.close();
        } catch (Exception e) {
            e.printStackTrace();
        }finally {
            try {
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        return col;
    }

    private Connection getConnection() throws Exception{
        Class.forName("com.mysql.jdbc.Driver");
        return DriverManager.getConnection("jdbc:mysql://localhost:3306/test","root",//改成自己的账号密码
                "root");
    }
}
    
}
```

## 3.代理的分类

- 虚代理：根据需要来创建很大的对象，该对象只有在使用的时候才会使用。
- 远程连接：用来在不同的空间上代表同一个对象，这个不同的空间可以使本机。
- copy-on-write：在客户端操作的时候，只有对象确实改变了。
- 保护代理：控制对原始对象的访问，如果需要，可以给不同的用户提供不同的访问权限。
- catch代理：为那些昂贵的操作提供临时的空间。防火墙代理：保护对象不被恶意用户访问。
- 同步代理：使用多个用户能够同时访问目标对象。
- 智能指引：在访问对象时候执行一些附加操作。