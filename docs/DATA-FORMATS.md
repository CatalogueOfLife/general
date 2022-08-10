# Data formats
The Clearinghouse supports a variety of data formats for both imports and exports.
The recommended format is ColDP.


## ColDP
The [Catalogue of Life Data Package (ColDP)](https://github.com/CatalogueOfLife/coldp/blob/master/README.md) is the recommended format that is least lossy and most expressive. It is a CSV/TSV file based archive that is more relational and normalised than Darwin Core archives.
It also supports sharing literature references as BibTex, taxonomic treatments as individual files and offers various other imrovements over classic DwC.


## Darwin Core Archive
Another well known format in the biodiversity community are [Darwin Core Archives](https://dwc.tdwg.org/text/). Similar to ColDP these archives package up column separated files, e.g. CSV or TAB delimited files, but are restricted by a *star schema* that especially limits sharing of structured references. Only Taxon core [DwC archives known as checklists](https://github.com/gbif/ipt/wiki/BestPracticesChecklists) are supported.

### Name relations extension
A new [name relation extension](https://rs.gbif.org/sandbox/extension/col-name-relation.xml) with the rowType http://rs.catalogueoflife.org/terms/dwc/NameRelation 
which mimicks the [ColDP name relation](https://github.com/CatalogueOfLife/coldp/blob/master/README.md#namerelation) entity.


### EML COL metadata
In addition to the GBIF EML profile COL supports a few custom properties inside the `<additionalMetadata>` block
which are defined in the ColDP [metadata.yaml](https://github.com/CatalogueOfLife/coldp/blob/master/metadata.yaml) profile and missing from regular EML files:

```
<dataset>
  ...
  <additionalMetadata>
    <metadata>
      <gbif>...</gbif>
      <col>
        <confidence>5</confidence>
        <completeness>95</completeness>
        <version>v.48 (06/2018)</version>
      </col>
    </metadata>
  </additionalMetadata>
</dataset>
```



## ACEF
The [CoL Data Submission Format](CoL_Standard_Dataset_v7_23Sep2014.pdf) (formerly known as Annual Checklist Exchange Format, ACEF) 
is supported when provided as a zipped archive of column separated files. It is the legacy format of CoL, but provides a [relational model fcussing on species](ACEF-ERD.png).
It is very limited when it comes to higher taxa and nomenclature.


## TextTree
The most simple way to share taxonomic trees with unstructured names in a [very human readable format](https://github.com/gbif/text-tree/blob/master/README.md).
No references or extension data is supported, but synonyms and basionyms can be included in the tree.


## Newick Trees
The Newick format is a simple format to represent graph-theoretical trees with edge lengths using parentheses and commas.
It is often used for sharing phylogenetic trees.
The New Hampshire eXtended format (which we implement) uses Newick comments to encode additional key value pairs, i.e. the id, scientificName ond rank specifically:
 
 - `:ND=` [string]  node identifier - if this is being used, it has to be unique within each phylogeny
 - `:S=` [string] species name of the species/phylum at this node
 - `:R=` [string] rank
 

https://en.wikipedia.org/wiki/Newick_format
http://www.phylosoft.org/NHX/


## Excel Spreadsheet
It is possible to import data directly from Excel spreadsheets if they conform to some simple conventions.
Sharing data with Excel spreadsheets is not a new format, but rather a different serialisation of the already supported format ColDP, DwC-A or ACEF.
All data must be available in a single spreadsheet using the modern XSLX format.
Each CSV data file is represented in Excel as a separate Sheet with a required header row that informs about the column semantics.
[ColDP already provides some templates](https://github.com/CatalogueOfLife/coldp/tree/master/templates) to be used for sharing.


## General file recommendations
For all text files we strongly recommend to use the UTF-8 character encoding.

 - If you can prefer tab separated **TSV files** over CSV files
 - Do not quote values e.g. by using a quotation mark which is common for CSV files
 - Make sure to **replace all TAB characters** by a simple spaces
 - Use a **header row** 
 - **NULL values** should be given as an **empty string**, not ```\N``` or ```NULL```
 - **boolean** values should be encoded either as ```0```/```1``` or ```false```/```true```
 - **decimals** should follow the international convention and use a dot as the decimal separator (not a comma) and no thousands separator at all
 - **dates** should be given as ISO 8601 strings, e.g. 1978-03-21
 - **multivalues** should be delimited with a simple comma if possible, e.g. ```freshwater, brackish```
