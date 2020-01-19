

```
CREATE SEQUENCE [schema_name . ] sequence_name  
    [ AS [ built_in_integer_type | user-defined_integer_type ] ]  
    [ START WITH <constant> ]  
    [ INCREMENT BY <constant> ]  
    [ { MINVALUE [ <constant> ] } | { NO MINVALUE } ]  
    [ { MAXVALUE [ <constant> ] } | { NO MAXVALUE } ]  
    [ CYCLE | { NO CYCLE } ]  
    [ { CACHE [ <constant> ] } | { NO CACHE } ]  
    [ ; ]
```


<H2>参数</H2>

**sequence_name** 
指定数据库中标识序列的唯一名称。 类型为 sysname 。

**[built_in_integer_type | user-defined_integer_type]**

序列可定义为任何整数类型。 允许使用下面的类型。

* tinyint - 范围为 0 到 255
* smallint - 范围为 -32,768 到 32,767
* int - 范围为 -2,147,483,648 到 2,147,483,647
* bigint - 范围为 -9,223,372,036,854,775,808 到 9,223,372,036,854,775,807
* decimal 或 numeric，小数位数为 0。

基于这些允许类型之一的任何用户定义数据类型（别名类型）。

如果未提供任何数据类型，则将 bigint 数据类型用作默认数据类型 。

**START WITH <constant>**

序列对象返回的第一个值。 START 值必须小于或等于序列对象的最大值并大于或等于其最小值 。 新序列对象的默认起始值是升序序列对象的最小值和降序序列对象的最大值。

**INCREMENT BY <constant>**

每次调用 NEXT VALUE FOR 函数时序列对象值递增（如果为负数，则为递减）的值 。 如果增量是负值，则序列对象为降序，否则为升序。 增量不能为 0。 新序列对象的默认增量为 1。

**[ MINVALUE <constant> | NO MINVALUE ]**

指定序列对象的边界。 一个新序列对象的默认最小值是该序列对象的数据类型的最小值。 对于 tinyint 数据类型，此值为零，对于所有其他数据类型则为负数。

**[ MAXVALUE <constant> | NO MAXVALUE]**

指定序列对象的边界。 一个新序列对象的默认最大值是该序列对象的数据类型的最大值。

**[ CYCLE | NO CYCLE ]**

此属性指定当超过序列对象的最小值或最大值时，序列对象是应从最小值（对于降序序列对象，则为最大值）重新开始，还是应引发异常。 新序列对象的默认循环选项是 NO CYCLE。

**循环 SEQUENCE 从最小值或最大值（而不是从起始值）重新开始。**

**[ CACHE [<constant> ] | NO CACHE ]**


创建Sequence

```
CREATE SEQUENCE myFirstSequence
AS INT
START WITH 1
INCREMENT BY 1
MINVALUE 1
MAXVALUE 100
CYCLE;
```

创建测试表

```
CREATE TABLE MySequenceTest
(
    id INT PRIMARY KEY,
    Number INT
);

```

插入数据

```
INSERT INTO dbo.MySequenceTest
(
    id,
    Number
)
VALUES
(NEXT VALUE FOR myFirstSequence, NEXT VALUE FOR myFirstSequence);
```