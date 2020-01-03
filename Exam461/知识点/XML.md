
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

# AUTO
# EXPLICIT
# PATH

