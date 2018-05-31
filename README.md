# Webapp (procument finance report portal)
http://192.168.11.245:8000/

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/WebApp

### Library
* [WAFFLE](https://github.com/Waffle/waffle) - Windows Authentication Framework
* [GSON](https://github.com/google/gson) - GSON
* [Unirest](http://unirest.io/) - http request library
* [OPENCSV](http://opencsv.sourceforge.net/) - convert data to csv
* [Sparkjava](http://sparkjava.com/) - A micro framework for creating web applications
* [Mysql](https://dev.mysql.com/downloads/connector/j/5.1.html) - Mysql connector
* [Apache POI](https://poi.apache.org/) - Java Excel API
* [Freemarker](https://freemarker.apache.org/index.html) - FreeMarker Java Template Engine
* [slf4j](https://www.slf4j.org/legacy.html) - Simple Logging Facade for Java (SLF4J)
* [SQLJDBC4] - SQL server connector
* [xerces](http://xerces.apache.org/) - XML parsers

## Structure

resources/sql - sql statements
resources/static - webapp css / js / static contents
resources/views - FreeMarker Java template

Java main class - engine.RouteEngine.java - init all api url class

## Code Tracing

E.g. http://192.168.11.245:8000/app/dept/procurement/purchase

'purchase' as path parameter

Search key word 'procurement/' in the project folder

found ProcurementPageController.java
```
get("/app/dept/procurement/:buCode", getFunctionPage());
...
private Route getFunctionPage(){
	...
	
	return RouteEngine.getFreeMarkerEngine().render(new ModelAndView(attributes,"procurement/purchase.ftl"));
	
	...
}
```

## API Tracing

Open broswer developer tools -> Network -> XHR

http://192.168.11.245:8000/app/dept/procurement/record/purchase?loadDate=xxxx&_=1527738255926

Search key word 'record/purchase' in the project folder 

```
get ("/app/dept/procurement/record/purchase", getPurchaseRecord(), new JsonTransformer());
...

private Route getPurchaseRecord(){
	return (request, response) -> {
		String loadDate = request.queryParams("loadDate");

		return new PostResponse(InventoryDao.getPurchaseRecord(loadDate));
	};
}
...
```

## Procurement 採購出入庫

JS Library - datatable, jquery, bootstrap

JS - /js/procurement/pork/purchase.js

URL - /app/dept/procurement/purchase

Database Table 

* [procurement].[dbo].[PorkPurchaseInfo] - Main record table
* [procurement].[dbo].[shop_fp] - Location FP Code mapping table
* [inventory_app].[dbo].[pork_shop_transfer] - Shop transfer
* [inventory_app].[dbo].[delivery_timing] - Delivery time code
* [inventory_app].[dbo].[supplier] - Supplier menu
* [inventory_app].[dbo].[location_type] - Shop Code menu & default BU

Get record

Query Parameter : loadDate

```
get ("/app/dept/procurement/record/purchase", getPurchaseRecord(), new JsonTransformer());
```

Update Record

```
post("/app/dept/procurement/record/:action", recordCUD(), new JsonTransformer());
```

Export XLS

Query Parameter : loadDate

```
get ("/app/dept/procurement/record/csv", getRecordCSV(), new JsonTransformer());
```








