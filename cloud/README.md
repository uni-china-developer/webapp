# Cloud Webapp (Ordering & Sales report system)

### System Overview

![System](https://holland.pk/uptow/i4/cc6597522d296ba954f96c3c6a43bad6.png)

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/CloudWeb

> Folder structure options and naming conventions for software projects

static (Root)

.
├── download		# temp files for download
└── fileUpload		# for upload operation


### Library

* [WAFFLE](https://github.com/Waffle/waffle) - Windows Authentication Framework
* [GSON](https://github.com/google/gson) - GSON
* [Unirest](http://unirest.io/) - http request library
* [OPENCSV](http://opencsv.sourceforge.net/) - convert data to csv
* [Sparkjava](http://sparkjava.com/) - A micro framework for creating web applications
* [Apache POI](https://poi.apache.org/) - Java Excel API
* [Freemarker](https://freemarker.apache.org/index.html) - FreeMarker Java Template Engine
* [slf4j](https://www.slf4j.org/legacy.html) - Simple Logging Facade for Java (SLF4J)

## Baodim

### Database

* [baodim_pns_app].[dbo].[baodim_item] - Item master
* [baodim_pns_app].[dbo].[shop_master$] - Shop master
* [baodim_pns_app].[dbo].[shop_list$] - Available shop lsit
* [baodim_pns_app].[dbo].[baodim_user_role] - System role, control which user can access '區經' area
* [baodim_pns_app].[dbo].[baodim_shop_permission] - Shop permission
* [baodim_pns_app].[dbo].[request_shop_header] - Shop request header
* [baodim_pns_app].[dbo].[request_shop_detail] - Shop request detail
* [baodim_pns_app].[dbo].[request_summary_header] - All Shop request header
* [baodim_pns_app].[dbo].[stocktake_shop_header] - Stock take header
* [baodim_pns_app].[dbo].[stocktake_shop_detail] - Stock take detail

### Uses Case

![usecase](https://holland.pk/uptow/i4/444c68b2c654ef03a7e0fe6f6d9e2b50.png)

### Code

API URL : /baodim/api

#### BaodimPageController

##### Get

Order
```
get ("/request", getRequest());

...

String date = request.queryParams("date");
String shop = request.queryParams("shop");
```

Stock
```
get ("/stock", getStock());

...

String date = request.queryParams("date");
String shop = request.queryParams("shop");
```

Summary

```
get ("/summary", getSummary());

...

String date = request.queryParams("date");
```

##### Post

Request JSON 
```
{
	rqdate : "",
	shopcode : "",
	editor : "",
	status : "",
	item : []
}
```

Code
```
post("/request", sendRequest());
```

Item Array base on the receive format. 

In case of the request URL is 
```
/baodim/api/request?date=2018-06-08&shop=PNS031
```

The response json is 
```
{"columns":
["pns_item_code",
"bdss_item_code",
"pns_item_name",
"bdss_item_name",
"unit_name",
"unit_code",
"shop_code",
"shop_name",
"pns_shop_code",
"rq_entry",
"rq_amount",
"yrq_amount",
"xrq_amount",
"vrq_amount",
"xsst_qty",
"vsst_qty",
"retail_price"] 
...
```

Then your item array should be the same like
```
["83586","FG-031-00224","叉燒包","叉燒包 (百佳)","包","PCS","PNS031","第一城","31","2018-06-08PNS03183586",-1.0,-1.0,6.0,-1.0,-1.0,0.0,7.0]
```

##### Shop Master

In case of there is a new shop PNS001 line 7 need to be added to system

1. update / insert  [baodim_pns_app].[dbo].[shop_master$]
```
insert into [baodim_pns_app].[dbo].[shop_master$]
( [shop_code],[shop_name],[pns_shop_code])
values ('PNS001', 'Name', '1')

update [baodim_pns_app].[dbo].[shop_master$]
set [car_line] = 7
```

2. insert avaliable shop list [baodim_pns_app].[dbo].[shop_list$]
```
insert into [baodim_pns_app].[dbo].[shop_list$]
(pns_shop_code) 
values (1)
```

3. insert shop permission to car7
```
insert into [baodim_pns_app].[dbo].[baodim_shop_permission]
([user_name],[shop_code])
values ('car7', 'PNS001')
```

## PNS Kit (MB)

### Database

* [pns_order_app].[dbo].[mbms_item_mapping] - Item master
* [pns_order_app].[dbo].[mbms_item_stock] - Kit Stock (over all / packs)
* [pns_order_app].[dbo].[kit_hdr_order] - Order header
* [procurement].[dbo].[PnsOrderFromExcel] - Request Order
* [procurement].[dbo].[PnsOrderReceive] - Receive Order

### Uses Case

![kitusecase](https://holland.pk/uptow/i4/3aadaca62fac7be4b06a3e4c4c6d8a5e.png)

### SP

* pns_order_app.dbo.udsp_cal_mbms_export - stock out (to shop)
* pns_order_app.dbo.udsp_cal_mbms_purchase - stock in (purchase)
* pns_order_app.dbo.udsp_cal_mbms_stock - will be executed after _export or _purchase is done 

### Code

API URL : /kit/api

#### PnsKitAPIController

##### Get

Purchase
```
get ("/:bu/purchase", getPurchase());

...

String bu = request.params("bu");
String date = request.queryParams("date");
```

Receive
```
get ("/:bu/receive", getReceive());

...

String bu = request.params("bu");
String date = request.queryParams("date");
```
##### Post

Request JSON 
```
{
	date : "",
	status : "",
	editUser : "",
	item : [{}]
}
```
