
在 FOR XML 子句中，指定以下模式之一：
# RAW

RAW 模式将为 SELECT 语句所返回行集中的每行生成一个 `<row>` 元素

* FOR XML RAW

```
USE AdventureWorks2012;  
GO  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID=122 or ProductModelID=119  
FOR XML RAW, ELEMENTS;  
GO  
```

下面是部分结果：

`<row ProductModelID="122" Name="All-Purpose Bike Stand" />`

`<row ProductModelID="119" Name="Bike Wash" />`


* FOR XML RAW, ELEMENTS

```
USE AdventureWorks2012;  
GO  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID=122 or ProductModelID=119  
FOR XML RAW, ELEMENTS;  
GO  
```
结果如下：

```
<row>  
  <ProductModelID>122</ProductModelID>  
  <Name>All-Purpose Bike Stand</Name>  
</row>  
<row>  
  <ProductModelID>119</ProductModelID>  
  <Name>Bike Wash</Name>  
</row>  
```

* FOR XML RAW, ELEMENTS XSINIL 

FOR XML RAW, ELEMENTS 生成的XML中， 如果有的列是NULL， 那么这列的Element不会生成对应的xml节点， 针对这种情况， **FOR XML RAW, ELEMENTS XSINIL** 可以解决这个问题

```
USE AdventureWorks2012;  
GO  
SELECT ProductID, Name, Color  
FROM Production.Product  
FOR XML RAW, ELEMENTS XSINIL ; 
```

结果如下：

```
<row xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">  
  <ProductID>1</ProductID>  
  <Name>Adjustable Race</Name>  
  <Color xsi:nil="true" />  
</row>  
...  
<row>  
  <ProductID>317</ProductID>  
  <Name>LL Crankarm</Name>  
  <Color>Black</Color>  
</row>
```

* 重命名 `<row>` 元素， FOR XML RAW ('ProductModel'), ELEMENTS  

```
SELECT ProductModelID, Name   
FROM Production.ProductModel  
WHERE ProductModelID=122  
FOR XML RAW ('ProductModel'), ELEMENTS  
GO  
```
结果如下：

```
<ProductModel>  
  <ProductModelID>122</ProductModelID>  
  <Name>All-Purpose Bike Stand</Name>  
</ProductModel>   
```

* 为 FOR XML 生成的 XML 指定根元素
在查询的结果集中， 如果FOR XML后， 是N条 xml结构， 利用 **`ROOT('MyRoot')`**后， 可以生成带有跟节点的一个XML

```
USE AdventureWorks2012;  
GO  
SELECT ProductModelID, Name   
FROM Production.ProductModel  
WHERE ProductModelID=122 or ProductModelID=119 or ProductModelID=115  
FOR XML RAW, ROOT('MyRoot')  
GO
```
结果如下：
```
<MyRoot>  
  <row ProductModelID="122" Name="All-Purpose Bike Stand" />  
  <row ProductModelID="119" Name="Bike Wash" />  
  <row ProductModelID="115" Name="Cable Lock" />  
</MyRoot>  
```



* XMLDATA 和 XMLSCHEMA （不重要）

```
USE AdventureWorks2012;  
GO  
SELECT ProductModelID, Name  
FROM Production.ProductModel  
WHERE ProductModelID=122 or ProductModelID=119  
FOR XML RAW, XMLDATA  
GO  
```

结果如下
```
<Schema name="Schema1" xmlns="urn:schemas-microsoft-com:xml-data"   
        xmlns:dt="urn:schemas-microsoft-com:datatypes">  
  <ElementType name="row" content="empty" model="closed">  
    <AttributeType name="ProductModelID" dt:type="i4" />  
    <AttributeType name="Name" dt:type="string" />  
    <attribute type="ProductModelID" />  
    <attribute type="Name" />  
  </ElementType>  
</Schema>  
<row xmlns="x-schema:#Schema1" ProductModelID="122" Name="All-Purpose Bike Stand" />  
<row xmlns="x-schema:#Schema1" ProductModelID="119" Name="Bike Wash" />  
```

* 支持二进制数据 BINARY BASE64

默认情况下， 无论FOR XML RAW 还是 FOR XML RAW, ELEMENTS 都不支持二进制数据， 在 FOR XML RAW 后增加 BINARY BASE64参数，就可以支持， 代码如下：

```
USE AdventureWorks2012;
GO
SELECT ProductPhotoID, ThumbNailPhoto
FROM Production.ProductPhoto
WHERE ProductPhotoID=1
FOR XML RAW, BINARY BASE64 ;
GO
```

结果如下：

```
<row ProductModelID="1" ThumbNailPhoto="/9j/4AAQSkZJRgABAQEAtAC0AAD/2wBDAAEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQEBAQH/2wBDAQEBAQEBAQEBAQEBAQEBAQEB"/>
```

# AUTO

AUTO 模式将基于指定 SELECT 语句的方式来使用试探性方法在 XML 结果中生成嵌套。

**生成的 XML 中的 XML 层次结构（即元素嵌套）基于由 SELECT 子句中指定的列所标识的表的顺序**

**因此，在 SELECT 子句中指定的列名的顺序非常重要**

**最左侧第一个被标识的表形成所生成的 XML 文档中的顶级元素**

**由 SELECT 语句中的列所标识的最左侧第二个表形成顶级元素内的子元素，依此类推。**

**如果 SELECT 子句中列出的列名来自由 SELECT 子句中以前指定的列所标识的表，该列将作为已创建的元素的属性添加，而不是在层次结构中打开一个新级别。 如果已指定 ELEMENTS 选项，该列将作为属性添加。**

* FOR XML AUTO

例如：

```
SELECT Cust.CustomerID,   
       OrderHeader.CustomerID,  
       OrderHeader.SalesOrderID,   
       OrderHeader.Status,  
       Cust.CustomerType  
FROM Sales.Customer Cust, Sales.SalesOrderHeader OrderHeader  
WHERE Cust.CustomerID = OrderHeader.CustomerID  
ORDER BY Cust.CustomerID  
FOR XML AUTO  
```

下面是部分结果：

```
<Cust CustomerID="1" CustomerType="S">  
  <OrderHeader CustomerID="1" SalesOrderID="43860" Status="5" />  
  <OrderHeader CustomerID="1" SalesOrderID="44501" Status="5" />  
  <OrderHeader CustomerID="1" SalesOrderID="45283" Status="5" />  
  <OrderHeader CustomerID="1" SalesOrderID="46042" Status="5" />  
</Cust>  
```

**CustomerID 引用 Cust 表。 因此，创建一个 `<Cust>` 元素，CustomerID 作为其属性添加**

**接下来的三列 OrderHeader.CustomerID、OrderHeader.SaleOrderID 和 OrderHeader.Status 引用 OrderHeader 表。 因此，为 `<Cust>` 元素添加 `<OrderHeader>` 子元素，这三列作为 `<OrderHeader>` 的属性添加**

**接着，Cust.CustomerType 列再次引用 Cust 表，该表已由 Cust.CustomerID 列标识。 因此，不创建新元素， 而是为以前创建的 `<Cust>` 元素添加 CustomerType 属性。**

**查询为表名指定别名。 这些别名显示为相应的元素名。**

下面的查询与上一个查询类似，不同的是 SELECT 子句先指定 OrderHeader 表中的列，再指定 Cust 表中的列。 因此，首先创建 <OrderHeader> 元素，然后为该元素添加 <Cust> 子元素。

```
select OrderHeader.CustomerID,  
       OrderHeader.SalesOrderID,   
       OrderHeader.Status,  
       Cust.CustomerID,   
       Cust.CustomerType  
from Sales.Customer Cust, Sales.SalesOrderHeader OrderHeader  
where Cust.CustomerID = OrderHeader.CustomerID  
for xml auto  
```
结果如下：

```
<OrderHeader CustomerID="1" SalesOrderID="43860" Status="5">  
  <Cust CustomerID="1" CustomerType="S" />  
</OrderHeader>
```
原因就是SELECT后的条件， 先是OrderHeader， 后是Cust。

**如果 SELECT 子句中指定了星号 (*) 通配符，则以前面所述的方式根据查询引擎所返回的行的顺序确定嵌套。**

* FOR XML AUTO, ELEMENTS

同FOR XML RAW中的ELEMENTS一样， ELEMENTS的意识是把XML中的属性变成XML节点

# EXPLICIT

 EXPLICIT 模式允许对 XML 的形状进行更多控制。 您可以随意混合属性和元素来确定 XML 的形状。由于执行查询而生成的结果行集需要具有特定的格式。此行集格式随后将映射为 XML 形状。使用 EXPLICIT 模式能够随意混合属性和元素、创建包装和嵌套的复杂属性、创建用空格分隔的值（例如 OrderID 属性可能具有一列排序顺序 ID 值）以及混合内容。但是，编写EXPLICIT模式的查询会比较麻烦。可以使用某些新的 FOR XML功能（例如编写嵌套 FOR XML RAW/AUTO/PATH 模式查询和 TYPE 指令），而不使用 EXPLICIT 模式来生成层次结构。嵌套 FOR XML 查询可以生成使用 EXPLICIT模式可生成的任何XML。

 由于EXPLICIT使用比较复杂， 实际应用是用不到， 所以放弃此内容。

# PATH

* **列名以 at 符号 (@) 开头**

如果列名以 at 符号 (@) 开头且不包含斜线标记 (/)，就会创建包含相应列值的 **row** 元素的属性

例如，下面的查询返回包含两列（@PmId 和 Name）的行集。 在生成的 XML 中，会向相应的 **row** 元素添加 PmId 属性并为其分配 ProductModelID 值 。

```
SELECT ProductModelID as "@PmId",  
       Name  
FROM Production.ProductModel  
WHERE ProductModelID=7  
FOR XML PATH;
```
结果如下：
```
<row PmId="7">  
  <Name>HL Touring Frame</Name>  
</row> 
```

请注意，在同一级别中，属性必须出现在其他任何节点类型（例如元素节点和文本节点）之前。 以下查询将返回一个错误：

```
SELECT Name,  
       ProductModelID as "@PmId"  
FROM Production.ProductModel  
WHERE ProductModelID=7  
FOR XML PATH;
```
请注意，在同一级别中，属性必须出现在其他任何节点类型（例如元素节点和文本节点）之前。 以下查询将返回一个错误：

```
SELECT Name,  
       ProductModelID as "@PmId"  
FROM Production.ProductModel  
WHERE ProductModelID=7  
FOR XML PATH;
```

* **列名不以 at 符号 (@) 开头**

如果列名不以 at 符号 (@) 开头、不是 XPath 节点测试之一且不包含斜线标记 (/)，就会创建作为行元素（默认为 row）的子元素的 XML 元素。

```
SELECT 2+2 as result  
for xml PATH;
```

结果如下：

```
<row>  
  <result>4</result>  
</row>  
```

* **列名不以 at 符号 (@) 开头，但包含斜线标记 (/)**

如果列名不以 at 符号 (@) 开头，但包含斜线标记 (/)，列名就指明了 XML 层次结构

```
SELECT 
[HumanResources].[Employee].BusinessEntityID "@EmpID",   
       FirstName  "EmpName/First",   
       MiddleName "EmpName/Middle",   
       LastName   "EmpName/Last"  
  FROM [AdventureWorks2012].[HumanResources].[Employee]
  JOIN Person.Person ON Person.BusinessEntityID = Employee.BusinessEntityID
  FOR XML PATH
```

结果如下：

```
<row EmpID="263">
  <EmpName>
    <First>Jean</First>
    <Middle>E</Middle>
    <Last>Trenary</Last>
  </EmpName>
</row>
<row EmpID="78">
  <EmpName>
    <First>Reuben</First>
    <Middle>H</Middle>
    <Last>D'sa</Last>
  </EmpName>
</row>

```