使用包含 .WRITE 的 UPDATE 在 **nvarchar(max)** 列中添加和删除数据

set 列名.write(表达式,@offset,@length) 

可以理解把 从 @offset 到 @length的字符串替换成 **表达式,**

* 当@offset是null时，则更新操作会在“列名”现有的值结尾附加“表达式”的值

* 如果@length是null，更新操作会移除从@offset到“列名”的值结尾的所有数据

* 如果@offset和@length都是NULL， 和offset是Null 逻辑一样， 都在值结尾附加“表达式”的值