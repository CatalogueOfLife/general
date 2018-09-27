# Data formats
The Clearinghouse supports a variety of data formats.
If possible we recommend the extended Darwin Core Archive format to share 
taxonomic, nomenclatural or just bibliographical data.



## Darwin Core Archive
The recommended format for providing data to the Clearinghouse is in the form of
[Darwin Core Archives](DWCA.md). These archives package up column separated files, e.g. CSV or TAB delimited files.


## ACEF
The [CoL Data Submission Format](ACEF.md) (formerly known as Annual Checklist Exchange Format, ACEF) 
is supported when provided as a zipped archive of column separated files.


## TCS
The TDWG Taxonomic Concept Schema in XML is considered to be used for transfering nomenclatural data.


## General file recommendations
For all text files we strongly recommend to use the UTF-8 character encoding.

 - Use a **TAB column delimitor** for column seperated files
 - Do not quote values e.g. by using a quotation mark which is common for CSV files
 - Make sure to **replace all TAB characters** by a simple spaces
 - Use a **header row** if possible
 - **NULL values** should be given as an **empty string**, not ```\N``` or ```NULL```
 - **boolean** values should be encoded either as ```0```/```1``` or ```false```/```true```
 - **decimals** should follow the international convention and use a dot as the decimal separator (not a comma) and no thousands separator at all
 - **dates** should be given as ISO 8601 strings, e.g. 1978-03-21
 - **multivalues** should be delimited with a simple comma if possible, e.g. ```freshwater, brackish```
