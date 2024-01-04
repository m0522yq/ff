<%--
  Created by IntelliJ IDEA.
  User: lenovo
  Date: 2024/1/3
  Time: 17:35
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>欢迎来到学生成绩管理系统</title>
</head>
<body>
<center>
    <h1>成绩录入</h1>
    <form action="cjlr" method="post">
        <table>
            <tr>
                <td>学生ID:</td>
                <td><input type="text" name="studentId"></td>
            </tr>
            <tr>
                <td>科目:</td>
                <td><input type="text" name="subject"></td>
            </tr>
            <tr>
                <td>成绩:</td>
                <td><input type="text" name="score"></td>
            </tr>
            <tr>
                <td colspan="2" align="center">
                    <input type="submit" value="提交">
                </td>
            </tr>
        </table>
    </form>
</center>
</body>
</html>
package dao;

import domain.Cj;
import utils.DAUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;
//成绩Dao
public class CjiDao {
//添加
    public boolean insert(Cj Cj) throws Exception {

        ResultSet resultSet = null;
        Connection connection = null;
        Statement statement = null;

        connection = DAUtils.getConnection();
        statement = connection.createStatement();


        String sql = "insert into cji(cid,xid,csex,cji) values (" +
                ""+Cj.getCid()+"," +
                "'"+Cj.getXid()+"'," +
                "'"+Cj.getCsex()+"'," +
                "'"+Cj.getCji()+"'" +
                ");";


        int i = statement.executeUpdate(sql);

        if (i > 0) {
            return true;
        }

        DAUtils.release(resultSet, statement, connection);
        return false;
    }



    //根据ID删除用户
    public boolean delect(int id) throws Exception {
        ResultSet resultSet = null;
        Connection connection = null;
        Statement statement = null;

        connection = DAUtils.getConnection();
        statement = connection.createStatement();

        String sql = "delete from cji where cid=" + id;
        long i = statement.executeLargeUpdate(sql);
        if (i > 0) {
            return true;
        }

        DAUtils.release(resultSet, statement, connection);

        return false;
    }


    //根据id查询客户信息
    public Cj find(int id) throws Exception {
        ResultSet resultSet = null;
        Connection connection = null;
        Statement statement = null;
        connection = DAUtils.getConnection();
        statement = connection.createStatement();
        String sql = "select * from cji where cid=" + id;
        resultSet = statement.executeQuery(sql);

        while (resultSet.next()) {
            Cj cj = new Cj();
            cj.setCid(resultSet.getInt("cid"));
            cj.setCname(resultSet.getString("xid"));
            cj.setCsex(resultSet.getString("csex"));
            cj.setCji(resultSet.getString("cji"));

            return cj;
        }

        DAUtils.release(resultSet, statement, connection);
        return null;

    }


    //根据id修改客户信息
    public boolean update(Cj Cj) throws Exception {
        ResultSet resultSet = null;
        Connection connection = null;
        Statement statement = null;

        connection = DAUtils.getConnection();
        statement = connection.createStatement();


        String sql = "UPDATE cji set xid= '"+ Cj.getCname()+"'," +
                "Csex='"+ Cj.getCsex()+"', " +
                "Cji='"+ Cj.getCji()+"' " +
                "WHERE cid="+ Cj.getCid()+";";
        long i = statement.executeLargeUpdate(sql);
        if (i > 0) {
            return true;
        }

        DAUtils.release(resultSet, statement, connection);

        return false;
    }

    //查询所有客户信息
    public static List<Cj> findAll() throws Exception {
        ResultSet resultSet = null;
        Connection connection = null;
        Statement statement = null;

        connection = DAUtils.getConnection();
        statement = connection.createStatement();

        String sql = "select * from cji";
        resultSet = statement.executeQuery(sql);
        ArrayList<Cj> list = new ArrayList<>();

        while (resultSet.next()) {

            Cj cj = new Cj();
            cj.setCid(resultSet.getInt("cid"));
            cj.setCname(resultSet.getString("xid"));
            cj.setCsex(resultSet.getString("csex"));
            cj.setCji(resultSet.getString("cji"));
            list.add(cj);
            System.out.println(cj);
        }
        DAUtils.release(resultSet, statement, connection);
        return list;
    }



}
package dlservice;

import dao.UserDao;
import domain.User;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
@WebServlet("/cjlr")
public class cjlr extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("utf-8");
        resp.setContentType("text/html;charset=utf-8");
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        UserDao userDao = new UserDao();;
        User Dl = userDao.dl(username,password);
        if (Dl != null){
            resp.getWriter().write("登录成功");
            req.getSession().setAttribute("user",Dl);
            resp.setHeader("refresh","1,url=cji.jsp");
        }else{
            resp.getWriter().write("登录失败");
            resp.setHeader("refresh","1,url=cjlr.jsp");
        }

    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
}
package dao;

import domain.User;
import utils.DAUtils;

import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

public class UserDao {
    public static UserDao userDao;

    public boolean insert(User user) throws SQLException {
        Connection connection = null;
        Statement statement = null;
        try {
            connection = DAUtils.getConnection();
            statement = connection.createStatement();


            String sql = "insert into manage(mid,mname,mpassword,mphone)" +
                    "values(" + null + ",'" + user.getUsername() + "','" +
                    user.getPassword() + "','" +
                    user.getPhone()+ "')";

            int i = statement.executeUpdate(sql);
            if (i > 0) { return true; }
            return false;
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }finally {
            DAUtils.release(statement,connection);
        }
        return false;
    }
    public User dl(String username,String password) {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet =null;
        try {
            connection = DAUtils.getConnection();
            statement = connection.createStatement();
            String sql = "select * from manage WHERE mname='"+username+"' AND mpassword="+password;
            resultSet = statement.executeQuery(sql);
            while (resultSet.next()){
                User user = new User();
                user.setId(resultSet.getInt("mid"));
                user.setUsername(resultSet.getString("mname"));
                user.setPassword(resultSet.getString("mpassword"));
                user.setPhone(resultSet.getString("mphone"));
                System.out.println(user);
                return user;
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }
        return null;
    }
    public User find(int id) {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet =null;
        try {
            connection = DAUtils.getConnection();
            statement = connection.createStatement();
            String sql = "select * from manage where id="+id;
            resultSet = statement.executeQuery(sql);
            while (resultSet.next()){
                User user = new User();
                user.setId(resultSet.getInt("mid"));
                user.setUsername(resultSet.getString("mname"));
                user.setPassword(resultSet.getString("mpassword"));
                user.setPhone(resultSet.getString("mphone"));
                return user;
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }

        return null;
    }
    public static List<User> findAll() {
        Connection connection = null;
        Statement statement = null;
        ResultSet resultSet =null;
        ArrayList<User> list = new ArrayList<>();
        try {
            connection = DAUtils.getConnection();
            statement = connection.createStatement();
            String sql = "select * from manage";
            resultSet = statement.executeQuery(sql);
            while (resultSet.next()){
                User user = new User();
                user.setId(resultSet.getInt("mid"));
                user.setUsername(resultSet.getString("mname"));
                user.setPassword(resultSet.getString("mpassword"));
                user.setPhone(resultSet.getString("mphone"));
                list.add(user);
            }
            return list;
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        }

        return null;
    }
    public static boolean delete(int id) throws SQLException {
        Connection connection = null;
        Statement statement = null;
        try {
            connection = DAUtils.getConnection();
            statement = connection.createStatement();
            String sql = "delete from manage where mid=" + id;
            int i = statement.executeUpdate(sql);
            if (i > 0) {
                return true;
            }
            return false;
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException throwables) {
            throwables.printStackTrace();
        } finally {
            DAUtils.release(statement,connection);
        }
        return false;
    }
    public boolean update(User user) throws SQLException, ClassNotFoundException {
        Connection connection = null;
        Statement statement = null;
        connection = DAUtils.getConnection();
        statement = connection.createStatement();
        String sql = "update manage set mname='" + user.getUsername()
                    +"',"+"mpassword='"+user.getPassword()
                    +"',"+"mphone='"+user.getPhone()
                    +"'"+"where mid="+user.getId();
        int i = statement.executeUpdate(sql);
        if (i > 0) {
            return true;
        }

        return false;
    }
}

