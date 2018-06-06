# Purchase Data Import 
Excel import program (Cost)

## Getting Started
svn checkout : file://192.168.11.205/f$/UC Development/Program Source/purchase

### Library
* [GSON](https://github.com/google/gson) - GSON
* [Apache POI](https://poi.apache.org/) - Java Excel API

## Classes in Main

* ExcelReader - import PNS Cost excel files / WC CR Chicken / WC CR Beef files / YG Chicken files
* YGAccessAutoRpt - Sales from M$ Access ( G:\PTSys\AutoReport\access ) 
* LocalPorkOrder - deprecated
* FreshPorkOrder - deprecated
* PackPorkCategory - deprecated
* YGPorkWeight - Pork weight excel (Updated by operation) may conflict with Joe.T Data
* ProcurementPorkTransaction - Pork Purchase Record (without weight data, for reference only)

## ExcelReader

About PNS (B),  if new shop signed up , new shop info should also be inserted to [procurement].[dbo].[beef_outlet]