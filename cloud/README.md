# Cloud Webapp (Ordering & Sales report system)

### System Overview

![System](https://holland.pk/uptow/i4/cc6597522d296ba954f96c3c6a43bad6.png)

This document include : 

* [Pns Kit](#pns-kit-mb)
* [Baodim](#baodim)
* [WC](https://github.com/uni-china-developer/webapp#database-table-3)
* [PNS](#pns-豬)
* [WC Upload image service](#wc-upload-image-service)

### Architecture

![architecture](https://holland.pk/uptow/i4/4ea7c0692c9dbd6b90e005b7d2b09444.png)

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/CloudWeb

Production path : /opt/java

Default port : 8000

> Folder structure options and naming conventions for software projects

### static (Root)

    .
    ├── download        # temp files for download
    └── fileUpload      # for upload operation


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

## PNS (豬)

### Uses Case

![porkorder](https://holland.pk/uptow/i4/f1800d8763734d8c9aaf6e83572e7bc3.png)

### AM 落豬

![ampork](https://holland.pk/uptow/i4/bbd2e1b78c267747a963e791fbe99e50.png)

### shan 落豬 (background change to yellow after cutoff time)

![shanm](https://holland.pk/uptow/i4/512ac1a6be064a7965a3ae9b341c35bb.png)

* cutoff time 

![ordertask](https://holland.pk/uptow/i4/4264c335a4e923d71c8fcb319943c44e.png)

ue -> bu code : C
ui -> bu code : D & BD 

#### Shop Permission

* 功能表 > 設定 > 選擇BU > 帳號設定 > 搜尋 login name

![permission](https://holland.pk/uptow/i4/140c53142590529db4d47dc568434388.png)

* 分店權限(BU) > 新增

### Order template

#### Setting

* 功能表 > 設定 > 選擇BU > 範本設定

![porktemplate](https://holland.pk/uptow/i4/0abf12c8ae6bfda85241147a326d055b.png)

* Database
```
[pns_order_app].[dbo].[favourite_header] - Template header

[pns_order_app].[dbo].[favourite_detail] - Template detail
```

## PNS (Topseal雞)

### Uses Case

![topealchi](https://holland.pk/uptow/i4/a592e11fc8c5c1896744d07638f2ce95.png)

### AM

![topsealchiam](https://holland.pk/uptow/i4/2df0fff02d89ab4416550539d2377e9b.png)

### Ching

* day + 2 改貨
![topsealchiching](https://holland.pk/uptow/i4/9dd7ed07543820af05d5bbb45f6cbce4.png)


* day + 1 發貨
![topsealchiching2](https://holland.pk/uptow/i4/80c165d721e4266e1765ac95c62b0a1b.png)


#### Note

1. 預貨通知 - email to 正大下一個星期的預貨數
2. day + 1 & Firday 發貨通知 - email to 宏輝發貨通知
3. day + 2 審批 - email to 正大兩日前改數
4. 公眾假期需要人手 generate 發貨通知 ([link](http://hklerpdb1/ReportServer/Pages/ReportViewer.aspx?%2fTopseal+Order%2fTC+Delivery+report&rs:Command=Render))

#### Database

Order status
```
SELECT [order_no],[order_status]
  FROM [pns_order_app].[dbo].[hdr_order]
  where bu_code = 'TC'
```

Auto create order sp
```
exec pns_order_app.dbo.udsp_auto_create_tc_order
```

Order item
```
SELECT [uc_code]
      ,[bu_code]
      ,[seq]
      ,[short_name]
      ,[report_name]
      ,[no_of_pack]
      ,[price]
  FROM [pns_order_app].[dbo].[app_order_item]
  where bu_code = 'TC'
```

Email setting table
```
[pns_order_app].[dbo].[app_mail_setting_prod]
```  

Stock state
```
SELECT [state_code]
      ,[bu_code]
      ,[state_description]
      ,[seq]
      ,[usage]
  FROM [pns_order_app].[dbo].[st_state]
  where bu_code = 'TC'
```

Stock enties
```
SELECT [entry_no]
      ,[st_date]
      ,[shop_code]
      ,[bu_code]
      ,[st_state]
      ,[qty]
      ,[updated_user]
      ,[updated_time]
  FROM [pns_order_app].[dbo].[st_stock_enties]
  where bu_code = 'TC'
```

## PNS (Topseal鴨)

### Uses Case

![topealchi](https://holland.pk/uptow/i4/a592e11fc8c5c1896744d07638f2ce95.png)

### AM Stocktake

![duckstockam](https://holland.pk/uptow/i4/9a48981d15cdc5a86137f1f395319d8c.png)

### Ching interface

![chingduck](https://holland.pk/uptow/i4/acd33551e7b95fc125d7de3f5a2d1dd7.png)

### Ching 預貨

![chingpreduck](https://holland.pk/uptow/i4/ab2c43237a944208bbfad5876a3baf41.png)

### Operation 收貨

![receiveduck](https://holland.pk/uptow/i4/3b931166bca94ccab3eaec63e12349da.png)

#### Note

1. 預貨通知 - email to 正大下一個星期的預貨數
2. 發貨通知 - email to 宏輝發貨通知

#### Database

Stock
```
[pns_order_app].[dbo].[td_item_stock_detail]

[pns_order_app].[dbo].[td_item_stock_header]
```

Preset order
```
[pns_order_app].[dbo].[td_pre_header]

[pns_order_app].[dbo].[td_pre_total]
```

Request order
```
[pns_order_app].[dbo].[td_request_shop_detail]

[pns_order_app].[dbo].[td_request_shop_header]
```

Summary header
```
[pns_order_app].[dbo].[td_request_summary_header]
```

Stock item info
```
[pns_order_app].[dbo].[td_st_item]
```

Email setting table
```
[pns_order_app].[dbo].[app_mail_setting_prod]
```

## WC Upload image service

![wcuploadf](https://holland.pk/uptow/i4/7ef18896f4965ed99adad2963544e450.png)

### Important note

1. Images uploaded on this server will keep 15 days only
2. Images uploaded on this server will be copied to our public drive by program
3. Image Copy program start every hour between 9:00 AM and 12:00 AM by task scheduler
4. There is a delay (worst case is 59 minutes) between cloud server and our server on syncing images
5. Task scheduler setup on 245 server
