# PNS Sales Import 
Email import program (Sales)

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/EmailSalesImportApp

### Library

* [GSON](https://github.com/google/gson) - GSON
* [JavaMail](https://mvnrepository.com/artifact/javax.mail/mail/1.4) - JavaMail is a Java API used to send and receive email via SMTP, POP3 and IMAP
* [Zip4J](http://www.lingala.net/zip4j/)
* [Apache POI](https://poi.apache.org/) - Java Excel API

## Flow

![Chart](https://holland.pk/uptow/i4/bf9133c60a9632da68d8a5f2c9bbe741.png)


## Add new BU File

E.g new BU Sales file need to be added ("XXX")

#### EmailAttachmentReceiver.java

1. add the following code at the end of EmailAttachmentReceiver.java constructor

```
fileList.add("XXX");
```

2. add the following code at the end of ExcelEditor.java constructor

```
lsList.add("XXX");
```

#### ExcelEditor.java

3. add the following code after categoryMapping (suppose the new BU code is YY)

```
private ArrayList<SaleData> lsImport(String file){
	HashMap<String, String> categoryMapping = new HashMap<>();
	...
	categoryMapping.put("XXX", "YY");
	...
```

## Note

Some kind of item will represent individual BU

56344 -> PF

86867 -> PH

73126 / 73124 -> MF

Code in ExcelEditor.java
```
case "D" :{
    if(itemCode.equals("56344")){
        saleDataTw.setItemCategory("PF");
        saleDataTw.setSaleCategory("D");
    }
    else{
        saleDataTw.setSaleCategory(category);
    }
    break;
}
case "C":{
    if(itemCode.equals("86867")){
        saleDataTw.setItemCategory("PH");
        saleDataTw.setSaleCategory("C");
    }
    else{
        saleDataTw.setSaleCategory(category);
    }
    break;
}
case "F" : {
    if(itemCode.equals("73126") || itemCode.equals("73124")){
        saleDataTw.setItemCategory("MF");
        saleDataTw.setSaleCategory("MF");
    }
    else{
        saleDataTw.setSaleCategory("F");
    }
    break;
}
```