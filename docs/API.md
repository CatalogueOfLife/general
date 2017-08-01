# Nomenclature
The Nomenclator API is designed in [RAML](https://github.com/Sp2000/colplus/blob/master/docs/api/nomenclator.raml) with the current draft being published at https://sp2000.github.io/colplus/api/nomenclator.html

## ScientificName
A scientific name is a surprisingly ambigous term. 
CoL deals and categorizes the following types of names:

 - Linnaean names, i.e. mono-, bi- or trinomials
 - Named hybrids and nothotaxa are not recognised in the CoL as a separate category   
 - Virus names

A name includes it's authorship; as well as nomenclatural comments in Botanical names. Two homonyms with different authors therefore represent two different name entities.

### Lexical variations

The same name can usually be represented by many different strings which we refer to as lexical variations. 
For each name a standard representation, the canonical form, exists.

Lexical variations exist for various reasons. 
Author spelling, transliterations, epither gender, additional infrageneric or infraspecific indications, cited species authors in infraspecific names are common reasons.
Listed here are 7 distinct names with some of their string representations:

```
 1. Aus bus Linnaeus 1758
    - Aus bus Linn. 1758
    - Aus bus Linn 1758
    - Aus bus L.
    - Aus ba Linn 1758.
    - Aus (Hus) bus L.
    
 2. Xus bus (Linn, 1758) 
    - Xus bus (Linn) Smith 
    
 3. Xus cus Smith, 1850
    - Xus cus Sm.
    
 4. Xus cus Jones 1900
 
 5. Xus bus cus Smith 1850
    - Xus bus subsp. cus Smith 1850
    
 6. Xus dus Pyle 2000
 
 7. Foo bar var. lion Smith 1850
    - Foo bar L. var. lion Smith
    - Foo bar subsp. dar var. lion Smith 1850
    - Foo bar Lin. subsp. dar Mill. var. lion Smith 1850
```

New names (**sp./gen. nov.**), new recombinations of the same epithet (**comb. nov.**), a name at a new rank (**stat. nov.**) 
or replacement names (**nom. nov.**) are all treated as distinct names. 

### Homotypic group & original name
Names based on the same type can be clustered together as a homotypic group of names.
A homotypic group not only includes all subsequent recombinations, but also any [replacement name](https://en.wikipedia.org/wiki/Nomen_novum) if existing.
These names are homotypic synonyms, also called nomenclatural or objective synonyms.

The first, originally published name is used to represent such a group as it is a clean proxy to the type specimen,
therefore every name in a homotypic group points back to the same original name.

Protonym in zoology and basionym in botany are terms for a very similar concept, but they do not include replacement names and are rather code specific 
so we will refer to *original name* here instead, inline with Darwin Core terminology.
Original names are not necessarily Code-compliant original descriptions, but usually they are. 

The above names can be clustered into four sets of homotypic synonym groups, shown with canonical authorship:

```
 1. Aus bus Linnaeus 1758
    - Aus ba Linnaeus 1758 [orthographic variant]
    - Xus bus (Linnaeus 1758) Smith 1850 [alternate combination] 
    
 2. Xus cus Smith 1850
    - Xus bus subsp. cus Smith 1850 [alternate rank]
    
 3. Xus cus Jones 1900 (heterotypic homonym of Xus cus Smith 1850)
    - Xus dus Pyle 2000 [replacement name for Xus cus Jones]
    
 4. Foo bar var. lion Smith 1850
```

#### comb. nov. in ICZN vs ICN

One of the most notable differences between [ICN](http://www.iapt-taxon.org/nomen/) 
and [ICZN](http://www.nhm.ac.uk/hosted-sites/iczn/code/) is the way names are cited when a species is placed in a different genus from the one it was originally published in (a comb. nov.). 
Botanists have a convention of always citing the authors of the original combination in brackets followed by the names of the authors of the combination. 
Zoologists don't follow this convention, they simply place the author of the original combination in brackets for the new combination and don't cite the authors who were first to make the new combination. 
This difference is cosmetic. Indeed [ICZN Recommendation 51G](http://www.nhm.ac.uk/hosted-sites/iczn/code/index.jsp?article=51) is that new combinations in zoology should be quoted in a similar way to the way they are quoted in botany.

*Basionym* is a well established term in the ICN where it is defined that 
a "basionym provides the final epithet, name, or stem of the new combination or name at new rank" (ICN Art. 6.10 & Art. 41).
It is the orginal combination of a name as viewed from a new combination. The basionym is therefore always relative to a new combination. 
A name can't be a basionym in its own right only relative to another name. 
ICZN does not mention the term basionym but the notion is clearly present in zoological nomenclature as zoologists also have the concept of the new recombination of a name. 


### Standards
[TCS](https://github.com/tdwg/tcs) and [DwC](https://github.com/tdwg/dwc) from TDWG deal with names.


### Rank
There is the need to deal with old ranks not accepted anymore - in semantic synonyms only; accepted names have only Code regulated ranks
The list given is intended to be interoperable between name providers for bacteria, viruses, fungi, plants, and animals. 
It is not assumed that in each taxonomic group all ranks have to be used. 
The enumeration attempts to strike a balance between listing all possible rank terms, and remaining comprehensible. 
Not included in the list are the botanical "notho-" ranks, which are used to designate hybrids (nothospecies, nothogenus) as this information is handled by the Name.notho property already.

Sources:

 - [GBIF](https://github.com/gbif/gbif-api/blob/master/src/main/java/org/gbif/api/vocabulary/Rank.java)
 - [TCS](https://github.com/tdwg/tcs/blob/master/TCS101/v101.xsd#L860) 
 - [TaxCat2](http://mansfeld.ipk-gatersleben.de/apex/f?p=185:78:::NO::P77_TAXCAT,P77_DB_CHECKBOX1,77_TAXCAT2RAD,77_SHOWRAD:%25,,0,s_All) 


### Name properties
 - scientificName: full string incl authorship and rank marker for infragenerics and infraspecfics in botany
 - canonicalName: plain name made from 1-3 name epithets only, exluding authors and rank markers
 - nomenclaturalCode: [Algae, Fungi and Plants](http://www.iapt-taxon.org/nomen/), [Animals](http://www.nhm.ac.uk/hosted-sites/iczn/code/), [Bacteria](https://www.ncbi.nlm.nih.gov/books/NBK8817/), [Virus](https://talk.ictvonline.org/information/w/ictv-information/383/ictv-code)
 - uninomial: for names at higher rank than species, e.g. genus, family name, etc. 
 - genus: the genus part of species name as well as independent uninomial if used in its own rank
 - infragenericEpithet: the infrageneric name 
 - specificEpithet
 - infraspecificEpithet
 - rank: rank of the name from enumeration above
 - notho: the part of the name which is considered a hybrid; generic, infrageneric, specific, infraspecific (see [GBIF](https://github.com/gbif/gbif-api/blob/master/src/main/java/org/gbif/api/vocabulary/NamePart.java#L24)
 - authorship: full string
 - originalAuthors: list of NameAuthor entities
 - originalYear: year of original name publication (in zoology only)
 - combinationAuthors: list of NameAuthor entities excluding ex- authors
 - combinationYear: year of combination publication, usually the same as publishedIn reference (in zoology only)
 - publishedIn: Reference the name was published in
 - publishedInPage: exact first page of the treatment within above reference
 - publishedInLink: URL to the first page of the treatment, e.g. in BHL, BioStor or Zenodo
 - originalName: link to the original name. In case of [replacement names](https://en.wikipedia.org/wiki/Nomen_novum) it points back to the replaced synonym.
 - isReplacement: flag to indicate that this name is a replacement name
 - isCodeCompliant: flag to indicate that this name is available/validly published
 - remark: notes for remarks on the name

TODO: handle suppressed names, name status & other nom acts. 


## NameAuthor
A person that is an author of scientific names. Not to be used for Reference authors.
The actual name string of the author can vary similar to scientific names due to spelling, transliteration, aliases, marriage or other name changes over time.
e.g., "Carlolus Linnaus", "Carl Linnaus", "Carl von Linné", etc.  

 - firstname: the preffered full first name(s) separated by whitespace
 - surname: full last name incl prefixes like "van" if existing
 - aliases: list of alternative representations (surname, firstname)
 - yearOfBirth
 - yearOfDeath
 - areaOfInterest: list of groups of higher taxa
 - biography: short description of the persons (work) life
 - collections: collection codes known to host type specimens

## Reference
Modelling and maintaining literature data can become a major burden. 
Currently the Catalogue of Life is using a very simplistic model useful when aggregating but not managing literature:

  - authors: Complete author string
  - year: Year(s) of publication
  - title: Title of the publication
  - text: Additional information pertaining to the publication
  - uri: Link other than DOI to online version
  - doi: DOI of publication

### Standards
There are various standards existing so sharing becomes simpler and with tools ready to be used. Specifically for JSON Rod Page put together a nice overview:
https://github.com/rdmpage/bibliographic-metadata-json

#### CrossRef JSON
https://github.com/CrossRef/rest-api-doc/blob/master/api_format.md
is this the same or a close flavor as citeproc json???

#### BibJSON
BibJSON used by BioStor & BioNames
http://okfnlabs.org/bibjson/

#### CSL JSON / Citeproc-js
http://citeproc-js.readthedocs.io/en/latest/csl-json/markup.html
https://aurimasv.github.io/z2csl/typeMap.xml


### Treatments
Treatments are the section of a publication that deals with a single scientific name. They are a unit of citation below articles. [Plazi](http://www.plazi.org) is based on them 
and Taxon Name Usages (TNU) in [GNUB](http://globalnames.org/docs/gnub/) correspond to treatments.

The ability to link to the treatment of a name is very useful. 
As every name has its own treatment it makes sense to attach treatment specific attributes to the [Name](#Name properties) itself.


## Type

TODO: model type designations, both type specimen and type species





# Taxonomy
Entities primarily related to taxonomic opinions, not the scientific name itself.
These entities are therefore only treated in the Catalogue of Life, but not in the nomenclator.

## Taxon
For taxon concept identity discussion see issue#6

 - taxonId
 - name: Name
 - status: accepted, provisionally accepted
 - parent: Taxon to establish taxonomic hierarchy. Not used for synonyms
 - accordingTo: Reference that describes the taxon concept
 - remarks: taxonomic notes
 
TODO: scrutiny, accordingTo vs plain list of references as sources


### Synonymy

- nameID
- nomenclatural status acording the Code
- colStatus: synonym, ambiguous synonym, misapplied name
Assuming that the homonyms Xus cus Smith 1850 and Xus cus Jones 1900 are for completely different species, 
and that we now regard Xus cus Smith 1850 as a subjective junior heterotypic synonym of Aus bus Linnaeus 1758, 
which we consider to belong to the genus “Aus”, we can collapse these down to two Accepted names (homotypic and heterotypic synonymy included indented under each):

```
 1. Xus bus (Linnaeus 1758) Smith 1850
    - Aus bus Linnaeus 1758
    - Aus buus Linnaeus 1758
    - Xus bus (Linnaeus 1758) Smith 1850
    - Xus cus Smith 1850
    - Xus bus subsp. cus Smith 1850

 2. Xus dus Pyle 2000
    - Xus cus Jones 1900
```

### Pro parte synonyms, splits & merges
TODO:


## Vernacular Name
Vernacular or common names that are used for a given taxon. Any taxon can have multiple vernacular names, even for the same language.
Properties of a common name are:

 - name: the vernacular name itself in original script
 - transliteration: transliteration in Roman alphabet
 - language: the language of the name
 - country: the country and area the name is used in 
 - source: a source reference where the name was listed

## Distribution
An species range distribution often based on experts knowledge. 
A single distribution record for a species should cover only one known area.

 - area: the named area, preferrably an area code from a given standard
 - standard: the standard the given code is based on. If missing the area is taken as free text. One out of: tdwg, iho, eez, iso
 - status: alien, alien-domesticated, domesticated, native, native-domesticated or uncertain
 - text (for not normalised data)
 - source: a source reference where the name was listed

## Environment information:
A very coarse environment/habitat classification of species is maintained using a single property from a controlled vocabulary:

 - lifezone: brackish, freshwater, marine or terrestrial


## Recent and fossil taxa
For palaeo taxa it is desirable to indicate whether an organism is known from fossils and the geological time range of these.

For recent taxa ideally we also want to know whether is is considered currently extinct, but known to have existed during the holocene.
As species go extinct rather quickly these days and true extinction of species is difficult to assess, we propose to leave this job to other initiatives like the IUCN 
and only track whether an organism is known from the holocene. The following properties are proposed THIS SHOULD BE REVISED:

 - livingPeriodStart: Earliest geological time in millions of years an organism is known to have lived on Earth
 - livingPeriodEnd: Latest geological time in millions of years an organism is known to have lived on Earth
 - preholocene: true if organism is known from before the Holocene, i.e. before ~11.700 years before now
 - holocene: true if organism is known from the Holocene, i.e. after the ice age from ~11.700 years to now



# Other

## Contributor
A contributor with a system login. Also to be used for bots modifying data.
If possible tie to Authors and allow ORCID and other external linkages


## Source metadata (GSD)
TODO: review current fields:

 - name: Full name of the source database
 - abbreviatedName: Abbreviated name of the source database
 - englishName: Name in English of the group(s) treated in the database
 - authorsOrEditors: Optional author(s) and editor(s) of the source database
 - organisation: Optional organisation which has compiled or is owning the source database 
 - version: version number or date of the source database 
 - releaseDate: date when database export has been completed for the CoL
 - abstract: free text field describing the source database, project and taxonomic group
 - taxonomicCoverage: a place of the group in CoL classification
 - isNew: values "new in a year cycle", "updated in a year cycle"
 - coverage: values "global", "regional"  
 - completeness: % 
 - checklistConfidence: values 1-5 


