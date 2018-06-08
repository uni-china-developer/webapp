# Webservice
webservice for [link](http://192.168.11.244:8080/app/shop/)

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/WebService

Production path : \\192.168.11.245\Java\project\WebService

Service Name : JavaWebService

Default port : 4567

### Library
* [GSON](https://github.com/google/gson) - GSON
* [Sparkjava](http://sparkjava.com/) - A micro framework for creating web applications
* [Freemarker](https://freemarker.apache.org/index.html) - FreeMarker Java Template Engine
* [slf4j](https://www.slf4j.org/legacy.html) - Simple Logging Facade for Java (SLF4J)
* [SQLJDBC4] - SQL server connector

### Database 

Server : bildb1

* [YGSale].[dbo].[Octopus]- Octopus info
* [YGSale].[dbo].[Outlet] - Shop info
* [YGSale].[dbo].[EmailOctopus] - Octopus transaction from email
* [YGSale].[dbo].[POSEodPay] - Transaction from pos
* [YGSale].[dbo].[Terminal] - POS Terminal info

### Data Sync SP

```
exec [YGSale].[dbo].[update_eodpay] 
```

## Sample 

Page : [link](http://192.168.11.244:8080/app/shop/octopus/)

### GET

Path Parameter : date 
Query Parameter : dtype (t - Transaction date, r - Receive date)

```
get("/sales/yg/octopus/get/:date", SalesController.getYGOctopusData);
```

Example :

```
http://192.168.11.245:4567/sales/yg/octopus/get/2018-06-06?dtype=t
```



