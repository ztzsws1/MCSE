<h1>LAG   ... OVER</h1>

**LAG 以当前行之前的给定物理偏移量来提供对行的访问。 在 SELECT 语句中使用此分析函数可将当前行中的值与先前行中的值进行比较。**

**后一行与前一行比较**


语法：
```
LAG (scalar_expression [,offset] [,default])  
    OVER ( [ partition_by_clause ] order_by_clause )
```
参数：

**scalar_expression**
要根据指定偏移量返回的值。 这是一个返回单个（标量）值的任何类型的表达式。 scalar_expression 不能为分析函数 。
**offset**
当前行（从中获得取值）后的行数。 如果未指定，则默认值为 1。 offset 可以是列、子查询或其他表达式，它们的计算值为正整数，或可隐式转换为 bigint 。 offset 不能是负数值或分析函数 。
**default**
偏移量 超出分区范围时返回的值。 如果未指定默认值，则返回 NULL。 default 可以是列、子查询或其他表达式，但它不能是分析函数 。 default 的类型与 scalar_expression 的类型必须兼容 。
**OVER ( [ partition_by_clause ] order_by_clause )**
partition_by_clause 将 FROM 子句生成的结果集划分为要应用函数的分区 。 如果未指定，则此函数将查询结果集的所有行视为单个组。 order_by_clause 在应用函数之前确定数据的顺序 。 当指定 partition_by_clause 时，它确定分区中数据的顺序 。 需要 order_by_clause 。


<h3>A. 比较年度之间的值</h3>

```
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, YEAR(QuotaDate) AS SalesYear, SalesQuota AS CurrentQuota,   
       LAG(SalesQuota, 1,0) OVER (ORDER BY YEAR(QuotaDate)) AS PreviousQuota  
FROM Sales.SalesPersonQuotaHistory  
WHERE BusinessEntityID = 275 and YEAR(QuotaDate) IN ('2005','2006');  
```

结果:

```
BusinessEntityID SalesYear   CurrentQuota          PreviousQuota  
---------------- ----------- --------------------- ---------------------  
275              2005        367000.00             0.00  
275              2005        556000.00             367000.00  
275              2006        502000.00             556000.00  
275              2006        550000.00             502000.00  
275              2006        1429000.00            550000.00  
275              2006        1324000.00            1429000.00  
```

<h3>B. 比较分区中的值</h3>

```
USE AdventureWorks2012;  
GO  
SELECT TerritoryName, BusinessEntityID, SalesYTD,   
       LAG (SalesYTD, 1, 0) OVER (PARTITION BY TerritoryName ORDER BY SalesYTD DESC) AS PrevRepSales  
FROM Sales.vSalesPerson  
WHERE TerritoryName IN (N'Northwest', N'Canada')   
ORDER BY TerritoryName; 
```

结果：

```
TerritoryName            BusinessEntityID SalesYTD              PrevRepSales  
-----------------------  ---------------- --------------------- ---------------------  
Canada                   282              2604540.7172          0.00  
Canada                   278              1453719.4653          2604540.7172  
Northwest                284              1576562.1966          0.00  
Northwest                283              1573012.9383          1576562.1966  
Northwest                280              1352577.1325          1573012.9383  
```


<h1>LEAD ...OVER </h1>

**LEAD 以当前行之后的给定物理偏移量来提供对行的访问。 在 SELECT 语句中使用此分析函数可将当前行中的值与后续行中的值进行比较。**

**当前行与后一行比较**


语法：
```
LAG (scalar_expression [,offset] [,default])  
    OVER ( [ partition_by_clause ] order_by_clause )
```


<h3>A. 比较年度之间的值</h3>

```
USE AdventureWorks2012;  
GO  
SELECT BusinessEntityID, YEAR(QuotaDate) AS SalesYear, SalesQuota AS CurrentQuota,   
    LEAD(SalesQuota, 1,0) OVER (ORDER BY YEAR(QuotaDate)) AS NextQuota  
FROM Sales.SalesPersonQuotaHistory  
WHERE BusinessEntityID = 275 and YEAR(QuotaDate) IN ('2005','2006');  
```

结果:

```
BusinessEntityID SalesYear   CurrentQuota          NextQuota  
---------------- ----------- --------------------- ---------------------  
275              2005        367000.00             556000.00  
275              2005        556000.00             502000.00  
275              2006        502000.00             550000.00  
275              2006        550000.00             1429000.00  
275              2006        1429000.00            1324000.00  
275              2006        1324000.00            0.00   
```

<h3>B. 比较分区中的值</h3>

```
USE AdventureWorks2012;  
GO  
SELECT TerritoryName, BusinessEntityID, SalesYTD,   
       LEAD (SalesYTD, 1, 0) OVER (PARTITION BY TerritoryName ORDER BY SalesYTD DESC) AS NextRepSales  
FROM Sales.vSalesPerson  
WHERE TerritoryName IN (N'Northwest', N'Canada')   
ORDER BY TerritoryName;  
```

结果：

```
TerritoryName            BusinessEntityID SalesYTD              NextRepSales  
-----------------------  ---------------- --------------------- ---------------------  
Canada                   282              2604540.7172          1453719.4653  
Canada                   278              1453719.4653          0.00  
Northwest                284              1576562.1966          1573012.9383  
Northwest                283              1573012.9383          1352577.1325  
Northwest                280              1352577.1325          0.00  
```

