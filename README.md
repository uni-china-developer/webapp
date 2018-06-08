# Webapp (procument finance report portal)


### System Overview

![System](https://holland.pk/uptow/i4/95a7529aef6fb0cdc752eef4d99865ca.png)

##### Front-End

* [Interface](http://192.168.11.245:8000/)
* [Cloud Interface](http://web.uni-china.com.hk:8000/)
* [WC PHP](http://192.168.11.244:8080/)

##### WebAPI 

* Main purpose is proxy (Cloud <--VPN--> API <-> DataBase)
* Email service

##### SP Update

* WC Mysql -> hklerpdb 

```
exec WellcomeSales.dbo.udsp_replicate_data_mysql
``` 

* YG Mysql -> bildb1

```
exec YGSale.dbo.update_eodpay -- dayend

exec YGSale.dbo.udsp_ebs_pos_header -- pos transaction header

exec YGSale.dbo.udsp_ebs_pos_detail -- pos transaction detail

exec YGSale.dbo.update_ebs_item_scale -- pos item properties

exec YGSale.dbo.udsp_ebs_cashentries -- pos dayend cash entries
```

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/WebApp

Production path : \\192.168.11.245\Java\project\WebApp

Service Name : JettyServlet

Default port : 8000

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

> Folder structure options and naming conventions for software projects

### Folder Structure

    .
    ├── resources           # project resources
    │   ├── sql             # sql files
    │   ├── static          # css / js files
    │   └── views           # freemarker template (html)
    ├── src                 # java sources
    └── static              # external folder
        ├── download        # temp files for download
        ├── wcrsa           # wc sales images folder shortcut
        └── fileUpload      # for upload operation

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

See WebAPI [Doc](https://github.com/uni-china-developer/webapp/tree/master/webapi#%E7%99%BC%E7%A5%A8%E5%8C%AF%E5%85%A5%E8%AA%BF%E6%95%B4)

## Procurement Reporting

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

## Finance MYOB export (Sales) & Sales adjustment 

Parameter : company 

* wellother (wc 億高)
* crother (cr 華潤)
* psother (pns 百佳)
* pscother (pns c 百佳 欄豬)
* pschi (pns cp 盈駿 小包豬)
* ygother (yg 金年升)
* arother (ar 全升)

```
get("/app/dept/finance/sale/get/export/:company", getExport(), new JsonTransformer());
```

### MYOB 億高 (上載圖片)

FileCopy source : file://192.168.11.205/f$/UC Development/Program Source/FileCopy

Upload URL : [live](http://web.uni-china.com.hk:8000/app/dept/uploadpage/main?user=L999)

Since WC users will use this service to upload images to server

FileCopy objective is copy images from Cloud Server to our public drive so that office staff can review the images

Intratnet URL : [live](http://192.168.11.245:8000/app/dept/finance/wellother)

### Database Table 

Adjustment Table

* [finance].[dbo].[adj_well_acc] - wc sales adjustment table 
* [finance].[dbo].[adj_wcr_acc] - cr sales adjustment table
* [finance].[dbo].[adj_ps_acc] - pns sales adjustment table
* [finance].[dbo].[adj_yg_acc] - yg & ar sales adjustment table

Source Table (data import from external storage)

* [WellcomeSales].[dbo].[WellcomeSales] - wc & cr sales table
* [ParknSale].[dbo].[PnSSales] - pns sales table
* [YGSale].[dbo].[POSEodPay] - yg sales table (pos)
* [YGSale].[dbo].[YGSales] - yg / ar sales (non-pos)

### Related Stored Procedures

Sync WC & CR Data to SQL Server (delete insert)

```
exec [WellcomeSales].[dbo].[udsp_replicate_data_mysql]
```

Sync WC data to adjustment table (delete insert)

```
exec [finance].[dbo].[auto_update_wc_sales]
```

Sync CR data to adjustment table (delete insert)

```
exec [finance].[dbo].[auto_update_cr_sales]
```

Sync YG data to adjustment table (delete insert)

```
exec [finance].[dbo].[auto_update_yg_sales]
```

update YG sales to adjustment table (update only)

```
exec [finance].[dbo].[auto_sync_sys_ygsales]
```

### MYOB Parameter

* [finance].[dbo].[adj_item] - YG (account code e.g. 41000)
* [finance].[dbo].[adj_ps_item] - PNS (cust id & item no)
* [finance].[dbo].[adj_wcr_item] - WC (item no)
* [finance].[dbo].[adj_well_item] - CR (item no)

### Special

* [finance].[dbo].[adj_ps_recate] - When Export MYOB CSV, (BD) bu sales will be grouped to (D) bu sales 

## Guide - How to add new shop

### PNS

1. Login to [cloudapp](http://web.uni-china.com.hk:8000/)

2. 設定 -> 進階

3. Select BU (top right hand corner)

4. click 新增 button

5. input shop detail

6. 確定 ->  complete

### WC & CR

1. Login to 192.168.11.244 (MySql Database)

2. insert / update shop data to following table

* pigtest.store - shop master
* pigtest.storeorderlimit - shop order menu list
* pigtest.saleslimit - shop sales bu list

3. Login to 192.168.11.245 (SQL Server Database)

4. insert / update shop data to following table

* [WellcomeSales].[dbo].[WellcomeStore] - Shop detail
* [WellcomeSales].[dbo].[WCRShop] - Shop Alias
* [WellcomeSales].[dbo].[wc_shop_discount_group] - Discount group
* [finance].[dbo].[wc_commission_ratio] - Commission

### YG

1. Login to 192.168.11.226 (SQL Server)

2. insert / update shop data to following table

* [YGSale].[dbo].[Outlet] - Shop Code / Name base info
* [YGSale].[dbo].[Terminal] - POS Terminal
* [YGSale].[dbo].[Octopus] - POS Octopus device mapping table

3. Login to 192.168.11.245 (SQL Server)

4. insert / update shop data to following table

* [procurement].[dbo].[shop_fp] - Location FP Code mapping table
* [inventory_app].[dbo].[location_type] - Shop Code menu & default BU











