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

![Chart](https://holland.pk/uptow/i4/869f1f16af308a95bf2267010764420b.png)


## Add new BU File

E.g new BU Sales file need to be added ("XXX")

1. add the following code at the end of EmailAttachmentReceiver.java constructor

```
fileList.add("XXX");
```

2. add the following code at the end of ExcelEditor.java constructor

```
lsList.add("UNI CHINA PARTIAL DUCK");
```

3. add the following code after (suppose the new BU code is YY)

```
private ArrayList<SaleData> lsImport(String file){
	HashMap<String, String> categoryMapping = new HashMap<>();
	...
	categoryMapping.put("XXX", "YY");
	...
```