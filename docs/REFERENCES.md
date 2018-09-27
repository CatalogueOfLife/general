# Literature citations
Modelling and maintaining literature data can become a major burden. 
Currently the Catalogue of Life is using a simplistic model useful when aggregating but not actively managing literature:

  - authors: Complete author string
  - year: Year(s) of publication
  - title: Title of the paper or book
  - source: Journal Name or Publisher Details for a book incl volume, edition, etc

This model is also followed by the GBIF bibliography extension for checklists which uses the corresponding Dublin Core terms:

 - dc:creator
 - dc:date
 - dc:title
 - dc:source
 - dc:identifier
 
In order to find DOIs and BHL pages it is useful to know the metadata in a better structured way, especially having a handle on the journals and books. Using a more structured metadata standard one can also format a citation to various styles as needed, including using an abbreviated and full form for the title or journal.

## CSL JSON / Citeproc-js
There are various standards existing so sharing becomes simpler and with tools ready to be used. Specifically for JSON Rod Page put together a nice [overview](https://github.com/rdmpage/bibliographic-metadata-json).

After [consultations](https://github.com/Sp2000/colplus/issues/23) we have settled on the [CSL JSON](http://citeproc-js.readthedocs.io/en/latest/csl-json/markup.html) standard used by citeproc-js and [CrossRef](https://github.com/CrossRef/rest-api-doc/blob/master/api_format.md) to represent reference metadata. Further CSL resources:

 - A mapping from [Zotero to CSL](https://aurimasv.github.io/z2csl/typeMap.xml)

There is the need to parse citations from various source formats into CSL JSON (at least single citation and the ACEF/DC style above). We are exploring the [anystyle parser](https://anystyle.io/) for this which can be taught domain specific styles like abbreviated botanical citations.


## Reference Format comparison

### CoL 
Dublin Core

 - authors
 - title
 - year
 - source

### WoRMS
http://www.marinespecies.org/aphia.php?p=taxdetails&id=866133
Selenoribates ghardaqensis Abd el Hamid, 1973 

links to article:
http://www.marinespecies.org/aphia.php?p=sourcedetails&id=221323

```
Name: Abd el Hamid, M. E. (1973). Acari (Oribatei) aus Ägypten: Selenoribates ghardaqensis nov. sp. am Roten Meer. Anzeiger der Österreichischen Akademie der Wissenschaften Wien. 8: 53-55.
SourceID: 221323
Authors: Abd el Hamid, M. E.
Year: 1973
Title: Acari (Oribatei) aus Ägypten: Selenoribates ghardaqensis nov. sp. am Roten Meer
Journal: Anzeiger der Österreichischen Akademie der Wissenschaften Wien
Suffix: 8: 53-55
Type: Publication
```

or as BibTex:
```
@article{WoRMS:SourceID:221323, 
    author = {Abd el Hamid, M. E.},
    journal = {Anzeiger der Österreichischen Akademie der Wissenschaften Wien},
    pages = {53-55},
    title = {Acari (Oribatei) aus Ägypten: Selenoribates ghardaqensis nov. sp. am Roten Meer},
    url = {http://www.marinespecies.org/aphia.php?p=sourcedetails&id=221323},
    volume = {8},
    year = {1973}
}
```

### CrossRef
uses CSL-JSON

### ZooBank
http://zoobank.org/NomenclaturalActs/BC1EEDF2-A6A5-4CC7-86EB-658F81B7F41D
Atelura formicaria Heyden, 1855

```Publication: Heyden, C. H. G. von. 1855 Nachricht über eine in Gesellschaft der Ameisen lebende Lepismene. Entomologische Zeitung 16: 368-370.
Page: 368
Figure(s):

Journal Article: Heyden, C. H. G. von. 1855. Nachricht über eine in Gesellschaft der Ameisen lebende Lepismene. Entomologische Zeitung 16: 368-370.
Full Title:	Nachricht über eine in Gesellschaft der Ameisen lebende Lepismene
Author(s):	Heyden, C. H. G. von
Journal:	Entomologische Zeitung
Volume:	16
Number:	
Pages:	368-370
Date Published:	1855
DOI:	
Language:	German
```

### Plazi
Quedius mutilatus Eppelsheim, 1888
http://tb.plazi.org/GgServer/html/03D5FE7E7048BF26FDCDFD70FD45FA17
http://treatment.plazi.org/id/03D5FE7E7048BF26FDCDFD70FD45FA17.rdf

Salnitska, Maria & Solodovnikov, Alexey, 2018, Taxonomy of the poorly known Quedius mutilatus group of wingless montane species from Middle Asia (Coleoptera: Staphylinidae: Staphylinini), European Journal of Taxonomy 401, pp. 1-17: 4-9

### ION
http://www.organismnames.com/details.htm?lsid=1057713
Atelura formicaria

### ITIS
https://www.itis.gov/servlet/SingleRpt/SingleRpt?search_topic=TSN&search_value=741147#null
Selenoribates ghardaqensis  Abdel-Hamid, 1973
-> no original publication


### BHL
tbd

### BioNames
tbd


# Examples

```json
{
  "key": 6,
  "datasetKey": 5,
  "verbatimKey": 32572,
  "citation": "Gard. Dict. Abr., ed. 4. (1754)",
  "doi": "https://doi.org/10.5962/bhl.title.79061",
  "year": 1754,
  "csl": {
    "type": "book",
    "author": Array[2][
      {
        "family": "Miller",
        "given": "Philip"
      }
    ],
    "issued": {
      "date-parts": Array[1][
        Array[1][
          1754
        ]
      ]
    },
    "edition": "4",
    "title": "The gardeners dictionary : containing the methods of cultivating and improving all sorts of trees, plants, and flowers, for the kitchen, fruit, and pleasure gardens, as also those which are used in medicine : with directions for the culture of vineyards, and making of wine in England ..."
  },
  "parsed": true
}
```
