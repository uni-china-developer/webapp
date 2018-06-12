# Webapi
API for Cloud and webapp

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/WebApp/WebAPI

Production path : \\192.168.11.245\Java\project\WebAPI

Service Name : WebAPI

Default port : 8080

> Folder structure options and naming conventions for software projects

### static (Root)

    .
    ├── cp                  # cp report folder
    ├── download            # temp files for download
    ├── processing          # cloud vision images
    ├── report              # 
    ├── tc                  # tc report folder
    ├── td                  # td report folder
    └── wc                  # wc report folder
        ├── op_am           # am report folder
        └── op_record       # record report folder

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
* [JavaMail](https://mvnrepository.com/artifact/javax.mail/mail/1.4) - JavaMail is a Java API used to send and receive email via SMTP, POP3 and IMAP
* [cloud-vision](https://cloud.google.com/vision/) - Derive insight from images with our powerful Cloud Vision API

## 發票匯入調整

URL: [live](http://192.168.11.244:8080/pur/)

PHP files : \\192.168.11.244\wamp64\www\pur

Header : \\192.168.11.244\wamp64\www\css3.php

CSS & JS : \\192.168.11.244\wamp64\www\static

### Use Cases

* 匯入電郵PDF 

ImageRecordApi.java

```
get( "/check/mail",        checkMail(), new JsonTransformer()); // check mail file
```

* 匯入至採購入庫

ImageRecordApi.java

```
post( "/export" ,           export(), new JsonTransformer()); // export data to purchase interface
```

### Database

* [access].[dbo].[image_to_order_data] - import data from PDF
* [access].[dbo].[image_to_order_bad_image] - temp storage of bad detect images
* [access].[dbo].[image_to_order_data_update] - used to merge table

## ScheduleManager

Report auto mail scheduler

Time table 
```
SELECT [email_id]
      ,[mail_subject]
      ,[mail_content]
      ,[runtime_hour]
      ,[runtime_minute]
      ,[folder_name]
  FROM [WellcomeSales].[dbo].[report_schedule]
```
![schedule](https://holland.pk/uptow/i4/403d6ed62b7a8b7cde08a0e745a8f7cb.png)

Output

![result](https://holland.pk/uptow/i4/cec35495af4dc793569e2cf660064f45.png)
