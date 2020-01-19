<h1>XACT_STATE 与 @@TRANCOUNT  </h1>


<h3>@@TRANCOUNT</h3>

返回在当前连接上执行的 BEGIN TRANSACTION 语句的数目。

BEGIN TRANSACTION 语句将 @@TRANCOUNT 增加 1

 ROLLBACK TRANSACTION 将 @@TRANCOUNT 递减到 0

 但 ROLLBACK TRANSACTION savepoint_name 除外，它不影响 @@TRANCOUNT 

 COMMIT TRANSACTION 或 COMMIT WORK 将 @@TRANCOUNT 递减 1

 **下面的示例演示嵌套的 BEGIN 和 COMMIT 语句对 @@TRANCOUNT 变量产生的效果。**
 ```
PRINT @@TRANCOUNT  
--  The BEGIN TRAN statement will increment the  
--  transaction count by 1.  
BEGIN TRAN  
    PRINT @@TRANCOUNT  
    BEGIN TRAN  
        PRINT @@TRANCOUNT  
--  The COMMIT statement will decrement the transaction count by 1.  
    COMMIT  
    PRINT @@TRANCOUNT  
COMMIT  
PRINT @@TRANCOUNT  
--Results  
--0  
--1  
--2  
--1  
--0  
 ```

**下面的示例演示嵌套的 BEGIN TRAN 和 ROLLBACK 语句对 @@TRANCOUNT 变量产生的效果。**

```
PRINT @@TRANCOUNT  
--  The BEGIN TRAN statement will increment the  
--  transaction count by 1.  
BEGIN TRAN  
    PRINT @@TRANCOUNT  
    BEGIN TRAN  
        PRINT @@TRANCOUNT  
--  The ROLLBACK statement will clear the @@TRANCOUNT variable  
--  to 0 because all active transactions will be rolled back.  
ROLLBACK  
PRINT @@TRANCOUNT  
--Results  
--0  
--1  
--2  
--0 
```


<h3>XACT_STATE</h3>

用于报告当前正在运行的请求的用户事务状态的标量函数。 XACT_STATE 指示请求是否有活动的用户事务，以及是否能够提交该事务。

| 返回值  | 含义  | 
| :------------ |:---------------:|
| 1      | 当前请求有活动的用户事务。 请求可以执行任何操作，包括写入数据和提交事务。 | 
| 0      | 当前请求没有活动的用户事务        |  
| -1 | 当前请求具有活动的用户事务，但出现了致使事务被归类为无法提交的事务的错误。 请求无法提交事务或回滚到保存点；它只能请求完全回滚事务。 请求在回滚事务之前无法执行任何写操作。 请求在回滚事务之前只能执行读操作。 事务回滚之后，请求便可执行读写操作并可开始新的事务。<br>当最外面的批处理运行完时，数据库引擎 会自动回滚所有不可提交的活跃事务。 如果事务进入不可提交状态时未发送错误消息，则当批处理结束时，将向客户端应用程序发送一个错误消息。 此消息指示检测到并回滚了一个不可提交的事务|   


XACT_STATE 和 @@TRANCOUNT 函数都可用于检测当前请求是否具有活动的用户事务。 **@@TRANCOUNT 不能用于确定事务是否已分类为不可提交的事务。 XACT_STATE 不能用于确定是否有嵌套事务。**



```
USE AdventureWorks2012;  
GO  
  
-- SET XACT_ABORT ON will render the transaction uncommittable  
-- when the constraint violation occurs.  
SET XACT_ABORT ON;  
  
BEGIN TRY  
    BEGIN TRANSACTION;  
        -- A FOREIGN KEY constraint exists on this table. This   
        -- statement will generate a constraint violation error.  
        DELETE FROM Production.Product  
            WHERE ProductID = 980;  
  
    -- If the delete operation succeeds, commit the transaction. The CATCH  
    -- block will not execute.  
    COMMIT TRANSACTION;  
END TRY  
BEGIN CATCH  
    -- Test XACT_STATE for 0, 1, or -1.  
    -- If 1, the transaction is committable.  
    -- If -1, the transaction is uncommittable and should   
    --     be rolled back.  
    -- XACT_STATE = 0 means there is no transaction and  
    --     a commit or rollback operation would generate an error.  
  
    -- Test whether the transaction is uncommittable.  
    IF (XACT_STATE()) = -1  
    BEGIN  
        PRINT 'The transaction is in an uncommittable state.' +  
              ' Rolling back transaction.'  
        ROLLBACK TRANSACTION;  
    END;  
  
    -- Test whether the transaction is active and valid.  
    IF (XACT_STATE()) = 1  
    BEGIN  
        PRINT 'The transaction is committable.' +   
              ' Committing transaction.'  
        COMMIT TRANSACTION;     
    END;  
END CATCH;  
GO  ```
