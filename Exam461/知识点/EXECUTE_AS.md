很多地方对EXECUTE AS 的三个参数CALLER, SELF, OWNER的说明不直观.

这里简单地举例说明: 

**A需要拥有一个过程P1用来对表T1进行操作, B建立了这个过程,给C赋与了EXECUTE权限来运行这个STORED PROCEDURE**

* EXECUTE AS CALLER: 查看C(运行P1的人)有没有权限读T1

* EXECUTE AS SELF: 查看B(P1的创建者)有没有权限读T1

* EXECUTE AS OWNER: 查看A有没有权限读T1,以后换成D拥有P1就查看D有没有权限读T1