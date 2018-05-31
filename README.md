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

### Database Table 

* [procurement].[dbo].[PorkPurchaseInfo] - Main record table
* [procurement].[dbo].[shop_fp] - Location FP Code mapping table
* [inventory_app].[dbo].[pork_shop_transfer] - Shop transfer
* [inventory_app].[dbo].[delivery_timing] - Delivery time code
* [inventory_app].[dbo].[supplier] - Supplier menu
* [inventory_app].[dbo].[location_type] - Shop Code menu & default BU

### Sample 

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

## Procurement 超市屠宰數輸入

JS Library - datatable, jquery, bootstrap

JS - /js/procurement/pork/freshpork.js

URL - /app/dept/procurement/fp

### Database Table 

* [inventory_app].[dbo].[pork_inventory] - Stock inventory
* [procurement].[dbo].[PnsPorkPurchase] - for pns 
* [procurement].[dbo].[BGPorkPurchase] - for wc

### Related Stored Procedures

* [procurement].[dbo].[cal_bg_pork_inventory] - wc (億高)
* [procurement].[dbo].[cal_ed_pork_inventory] - pns mainland pork (建華企業)

### Sample

calculate 建華企業 purchase date from 2018/05/01 to now

```
exec [procurement].[dbo].[cal_ed_pork_inventory] '2018-05-01'
```

## Procurement 發票匯入調整

See WebAPI [Doc](#)

## Reporting

### Excel based

* 豬數查詢 - /app/dept/procurement/pcheck
* 建企億高豬數 (採購) - /app/dept/procurement/purchasecheck

### Reporting service

Reference file - /resources/views/procurement/include/coreuiheader.ftl

## Finance Bank Reconciliation

Controller - FinancePageController.java

### Database Table 

* [finance].[dbo].[ChequeBook] - Cheque book menu
* [finance].[dbo].[Bank] - Bank account menu
* [finance].[dbo].[APTransaction] - payable cheque record
* [finance].[dbo].[APPaymentType] - payable type
* [finance].[dbo].[ARTransaction] - receive record
* [finance].[dbo].[ARPaymentType] - receive type
* [finance].[dbo].[BankRecPrivilege] - privilege for open new cheque book

## Finance Cheque Register / Autopay

URL - /app/dept/finance/chequereg

URL - /app/dept/finance/autopay

JS - /js/finance/chequereg.js

JS - /js/finance/autopay.js

import file template - /template/chequetemp.xlsx

### API Sample

Get Cheque by Bank account
Query Parameter : bankPK

```
get("/app/dept/finance/bank/cheque/get",getAllChequeByBank(), new JsonTransformer());
```

Get Cheque book list
Parameter : bankCode

```
get("/app/dept/finance/bank/cheque/book/get/:bankCode", getChequeBookByBank(), new JsonTransformer());
```

Update cheque status

```
post("/app/dept/finance/bank/cheque/update/status", updateChequeStatus(), new JsonTransformer());
```

Import Cheque

Parameter : ArrayList<ChequeData>

```
post("/app/dept/finance/bank/cheque/update/import", importCheque(), new JsonTransformer());
```

## Finance Unpresented Cheque

URL - /app/dept/finance/ap

JS - /js/finance/ap.js

### API Sample

Get all unpresented cheque

```
get("/app/dept/finance/bank/cheque/get/u", getUnpresentedChequeByBank(), new JsonTransformer());
```

## Finance AR Transaction

URL - /app/dept/finance/ar

JS - /js/finance/ar.js

### API Sample

Get Deposit record by bank

Query Parameter : bankPK

```
get("/app/dept/finance/bank/ar/get/u",getUnPaidARTransactionByBank(), new JsonTransformer());
```

Update status

```
post("/app/dept/finance/bank/ar/update/status", updateARStatus(), new JsonTransformer());
```










