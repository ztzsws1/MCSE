
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

嵌套的顺序与 SELECT后查询的列的顺序有关系

除了 AUTO 模式的试探性方法生成的 XML 形状之外，还可以编写 FOR XML 查询来生成 XML 层次结构。
# EXPLICIT
# PATH

