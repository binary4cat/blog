---
title: ASP.NET存储过程操作
date: 2015-04-06T17:21:22+08:00
tags: ["SQL Server","转载"]
permalink: ASP-NET-stored-procedure-operations
categories: ["cSharp"]
---
原文地址：http://www.cnblogs.com/libingql/archive/2010/05/02/1726104.html

　　存储过程是存放在数据库服务器上的预先编译好的sql语句。使用存储过程，可以直接在数据库中存储并运行功能强大的任务。存储过程在第一应用程序执行时进行语法检查和编译，编译好的版本保存在高速缓存中。在执行重复任务时，存储过程可以提高性能和一致性。由于存储过程可以将一系列对数据库的操作放在数据库服务器上执行，因而可以降低Web服务器的负载，提高整个系统的性能。

### 1、创建存储过程
```
USE Northwind
GO
CREATE PROC [DBO].[GetOrdersByOrderDate]
    @OrderDate DATETIME
AS
    SELECT
        [OrderID],[CustomerID],[OrderDate]
    FROM
        [Orders]
    WHERE
        [OrderDate] >= @OrderDate


USE Northwind
GO
EXEC GetByOrderDate '1996-10-10 0:00:00'
```
<!--more-->
### 2、调用存储过程
```
SqlConnection conn = new SqlConnection(ConfigurationManager.ConnectionStrings["Northwind"].ConnectionString);

            try
            {
                SqlCommand cmd = new SqlCommand();
                cmd.CommandText = "[GetOrdersByOrderDate]";
                cmd.Connection = conn;

                cmd.CommandType = CommandType.StoredProcedure;

                cmd.Parameters.Add("@OrderDate", SqlDbType.DateTime, 8).Value = "1998-5-5";

                conn.Open();

                SqlDataAdapter da = new SqlDataAdapter();
                da.SelectCommand = cmd;

                DataSet ds = new DataSet();

                da.Fill(ds);

                gvOrders.DataSource = ds;
                gvOrders.DataBind();
            }
            catch (SqlException ex)
            {
                throw new Exception(ex.Message, ex);
            }
            finally
            {
                conn.Close();
            }
        }
```

### 3、输出参数
存储过程可以通过输出参数来返回值。
```
USE Northwind
GO
CREATE PROC [DBO].[GetOrderDetailsTotalByProductID]
    @ProductID INT,
    @Total MONEY OUTPUT
AS
    SELECT @Total = SUM([UnitPrice]*[Quantity])
    FROM
        [Order Details]
    WHERE
        [ProductID] = @ProductID
```
```
SqlConnection conn = new SqlConnection(ConfigurationManager.ConnectionStrings["Northwind"].ConnectionString);

            try
            {
                SqlCommand cmd = new SqlCommand();
                cmd.CommandText = "[GetOrderDetailsTotalByProductID]";
                cmd.Connection = conn;

                cmd.CommandType = CommandType.StoredProcedure;

                cmd.Parameters.Add("@ProductID", SqlDbType.Int, 4).Value = 21;
                cmd.Parameters.Add("@Total", SqlDbType.Money).Direction = ParameterDirection.Output;

                conn.Open();
                cmd.ExecuteNonQuery();

                lblTotal.Text = cmd.Parameters["@Total"].Value.ToString();
            }
            catch (SqlException ex)
            {
                throw new Exception(ex.Message, ex);
            }
            finally
            {
                conn.Close();
            }
```

### 4、Return 返回值
使用返回值表示存储过程的执行状态，它类似于输出参数，其区别：
(1)、返回值只能返回sql整数值；
(2)、返回值不能在存储过程内部声明，它使用Transcat-SQL的RETURN语句返回；
```
USE Northwind
GO
CREATE PROC [DBO].[GetOrdersByCustomerID]
    @CustomerID INT,
    @OrderDate DATETIME OUTPUT
AS
    SELECT @OrderDate = MAX([OrderDate])
    FROM
        [Orders]
    WHERE
        [CustomerID] = @CustomerID

    IF @OrderDate IS NULL
        RETURN 0
    ELSE
        RETURN 1
```
```
SqlConnection conn = new SqlConnection(ConfigurationManager.ConnectionStrings["Northwind"].ConnectionString);

            try
            {
                SqlCommand cmd = new SqlCommand();
                cmd.CommandText = "[GetOrdersByEmployeeID]";
                cmd.Connection = conn;

                cmd.CommandType = CommandType.StoredProcedure;

                cmd.Parameters.Add("@ReturnValue", SqlDbType.Int, 4).Direction = ParameterDirection.ReturnValue;
                cmd.Parameters.Add("@EmployeeID", SqlDbType.Int, 4).Value = 1;
                cmd.Parameters.Add("@OrderDate", SqlDbType.DateTime).Direction = ParameterDirection.Output;

                conn.Open();
                cmd.ExecuteNonQuery();

                lblReturnValue.Text = cmd.Parameters["@ReturnValue"].Value.ToString();
                lblOrderDate.Text = cmd.Parameters["@OrderDate"].Value.ToString();
            }
            catch (SqlException ex)
            {
                throw new Exception(ex.Message, ex);
            }
            finally
            {
                conn.Close();
            }
```
