# Clearinghouse DwC-A
The recommended format for providing nomenclatural or taxonomic information data to the Clearinghouse is in the form of
Darwin Core Archives (dwca). These archives package up column separated files, e.g. CSV or TAB delimited files.
It is based on a regular DwC checklist archive with a "Taxon" core which is also used to exchange plain names.

The Clearinghouse needs more relations than the limiting star schema of Darwin Core Archives offer.
We therefore extend the dwca specifications to allow any number of generic <entity> sections to exist, defined just like the other extensions but without the coreid element.
Most notably we make use of that feature in order to be able to refer to structured bibliographic reference from anywhere.

In order to refer unambigously to these entities it is recommended to include dc:identifier as a term.




**!!! THE FOLLOWING NEEDS A LOT OF CLEANING UP STILL AND IS NOT READY FOR PUBLIC EYES !!!**


### meta.xml
TODO:
 - updated xml schema
 - <entity> example

### Metadata
EML is the most widely used format for dataset metadata used in DwC-As.
The Clearinghouse accepts this, but also offers support for a much simpler YAML based format that leans towards Dublin Core and the CoL submission format.

#### metadata.yaml
A complete example of a full metadata YAML file for Fishbase:

```
title:	FishBase
shortTitle:	FishBase
authors:
  - Froese R.
  - Pauly D. (eds)
organization:
  - FIN
  - WorldFish
  - FAO
  - IFM-GEOMAR
  - MNHN
  - RMCA
  - NRM
  - FC-UBC
  - AUTH
  - CAFS
contact:
  name: Rainer Froese
  email: rainer@dont-email-me.com
group: Fishes
abstract: >
  FishBase is a global information system with extensive information on all species and subspecies of fish.
  In addition to the data contributed to the Catalogue of Life,
  the original FishBase database also includes descriptive, biological, ecological, physiological and conservation data
  and more, and onward links to information in many other databases.
  Data entry and maintenance is done mainly at the FishBase Information and Research Group, Inc. (FIN) in the Philippines
  since the 1st January 2011 in collaboration with many colleagues and institutions around the world.
  The team was previously hosted in WorldFish since the start in 1990
  (formerly ICLARM then called WorldFish Center between 2001-2013, and now WorldFish since 2013).
  FishBase is supported by a Consortium of nine institutions around the world that acts as the Scientific Committee for FIN.
  FishBase is funded mainly by the European Commission, and by several other donors.
  FishBase uses data and information from the Catalog of Fishes (CofF) developed by W.N. Eschmeyer at the California Academy of Science.
  In particular, FishBase uses CofF as the most complete and up-to-date fish nomenclator,
  and as a taxonomic authority list in a collaborative work of synchronization of the two databases.
website: http://www.fishbase.org
logo: http://www.fishbase.org/images/gifs/fblogo_new.gif
release: 2017-02-24
citation: Froese R. & Pauly D. (eds) (2018). FishBase (version Feb 2017). In: Species 2000 & ITIS Catalogue of Life, 2017 Annual Checklist (Roskov Y., Abucay L., Orrell T., Nicolson D., Bailly N., Kirk P.M., Bourgoin T., DeWalt R.E., Decock W., De Wever A., Nieukerken E. van, Zarucchi J., Penev L., eds.). Digital resource at www.catalogueoflife.org/annual-checklist/2017. Species 2000: Naturalis, Leiden, the Netherlands. ISSN 2405-884X.
```

#### Logo image
Can be provided inside the archive as a file called ```logo.[jpg|gif|png]``` 
or as an external URL inside the metadata (see above).

### Data files
It is recommended to generate **TAB delimited** files encoded in **UTF-8**.

### Taxon
The taxon core file for checklist archives.
Each accepted name and synonym should have its own, dedicated record i.e. row.
A denormalized, flat classification can optionally be given, but is recommended to define the taxonomic hierarchy and any other relations by referring to other taxa 
via ids in this file. The following terms are recommended:

 - taxonID
 - parentNameUsageID
 - acceptedNameUsageID
 - originalNameUsageID
 - scientificNameID
 - scientificName
 - taxonRank
 - references: record URL
 - publishedInID: -> refID

The scientificName can also be given in an atomized form instead:

 - genus
 - specificEpithet
 - infraspecificEpithet
 - scientificNameAuthorship


### References
Structured bibliographic references can be referred to from many other places and we therefore recommend to use a normalized, structured reference file.
The file can be supplied in 3 formats:

#### CSL_JSON
CSL-JSON should be indicated in the meta.xml by declaring a rowType of '''CSL-JSON'''

#### BibTex
BibTex should be indicated in the meta.xml by declaring a rowType of '''BibTex'''

#### Dublin Core TSV
Alternatively a regular column delimited file (TAB delimited preferred) can be used with Dublin Core terms as columns and a rowType of http://col.plus/terms/Reference
DC terms to be used are based on http://dublincore.org/documents/dc-citation-guidelines/ and http://rs.gbif.org/extension/gbif/1.0/references.xml:

 - dc:identifier (source internal id referred to elsewhere)
 - dcterms:bibliographicCitation: full rendered citation as a single string
 - dc:title
 - dc:creator: authors, ideally separated by comma or a delimiter indicated in the meta.xml
 - dc:publisher
 - dcterms:issued
 - dcterms:isPartOf journal ISSN, e.g. ISSN:0302-9743
 - dc:source: the container title, e.g. the journal
 - dc:references: weblink or DOI
A **TAB delimited** file encoded in **UTF-8** with Dublin Core columns.

### Vernacular names
TBD

### Distribution
TBD

### Bibliography
TBD











# Taxon Core
The taxon core contains taxa and names. 
In taxonomic lists synonyms and accepted names are both included and require to have an identifier on their own.

Terms are marked by [N] or [T] if recommended for nomenclatural or taxonomic datasets.
Exclamation marks ! indicate required terms.

 - __taxonID__ [NT]!: required primary key of the name or taxon that the other ID terms below refer to
 - __references__ [NT]: URL to same record in publisher website

 - __scientificName__ [N]!: scientific name without authorship
 - __scientificNameAuthorship__ [N]: the complete authorship incl. year and basionym authors of the terminal epithet
 - __taxonRank__ [N]!: the taxonomic rank of the name, given in latin or english
 - __nomenclaturalCode__ [N]: the nomenclatural code
 - __originalNameUsageID__ [N]: taxonID of the basionym
 - __namePublishedInID__ [N]: ID to a publication in the reference extension this name was first published in
 - __nomenclaturalStatus__ [N]: nomenclatural status remarks, e.g. _nom. illeg._
 - __kingdom__ [N]: the loosely classified kingdom
 - __family__ [N]: the loosely classified family

 - __scientificNameID__ [T]: Optional id for the name only in _addition_ to the taxonID. Only useful in taxonomic lists to assign a known name identifier.
 - __acceptedNameUsageID__ [T]: taxonID of the accepted name (synonyms only)
 - __parentNameUsageID__ [T]: taxonID of the next higher taxon of the classification (accepted only)
 - __nameAccordingTo__ [T]: taxonomic scrutinizer
 - __nameAccordingToDate__ [T]: date last scrutinized in the form YYYY-MM-DD
 - __taxonRemarks__ [T]: any taxonomic remark
 - __fossil__ [T]: boolean (true/false) indicating if an organism is known from fossil evidence.
 - __recent__ [T]: boolean (true/false) indicating if an organism is known to have lived recently, i.e. in the Holocene.
 - __lifezone__ [T]: one or multiple of: BRACKISH, FRESHWATER, MARINE or TERRESTRIAL delimited by a comma.
 - __livingPeriod__ [T]: The (geological) time an organism with fossil remains is known to have lived. For geological times of fossils ideally based on a vocabulary like http://en.wikipedia.org/wiki/Geologic_column

# Type Names
Typifcation of genus or family names by a lower ranked scientific name.

 - __typeNameID__ [N]: taxonID of the type species or genus that typifies this name
 - __typeDesignationID__ [N]: ID to a publication in the reference extension the type for this name was first published in
 - __typeFixation__ [N]: One of monotypy, tautonomy, original or subsequent designation.

# Nomenclatural acts
 - __TBD__: 

# Vernacular Names
http://rs.gbif.org/extension/gbif/1.0/vernacularname.xml

Vernacular names shared in taxonomic datasets.

 - __vernacularName__ !: the vernacular name itself, given in the original script if possible.
 - __language__ !: 3 letter ISO language code
 - __countryCode__: optional 2 or 3 letter ISO country code
 - __sourceID__: The source for this vernacular names as an ID of a reference from the reference extension. Preferred over verbatim, unstructured source

# Distribution
http://rs.gbif.org/extension/gbif/1.0/distribution.xml

Species expert range distributions shared in taxonomic datasets.
The distribution area can be specified either as a generic identifier from a well known gazetteer or some country code - one of which must be given.

 - __locationID__ : the vernacular name itself, given in the original script if possible.
 - __countryCode__: optional ISO 3166 country code including 2 or 3 letter codes and 3166-2 codes for country subdivisions.
 - __country__: optional country name in english or local language
 - __occurrenceStatus__: optional status of the species presence in this area. Defaults to "natively present".
 - __sourceID__: The source reference for this single distribution record as an ID to the reference from the reference extension. Preferred over verbatim, unstructured source

# References


# FAQ
 - How can we 







# Checklist Darwin Core Archives
Darwin Core archive can be used to publish taxonomic or nomenclatoral datasets which GBIF generically calls ___checklists___. GBIF has published various documents that guide publishers in crafting archives:

 - [Publishing Species Checklists, Best Practices](http://www.gbif.org/resources/2548)
 - [GBIF GNA Profile Reference Guide for Darwin Core Archive, Core Terms and Extensions](http://www.gbif.org/resources/2562)
 - [Publishing Species Checklists, Step-by-Step Guide](http://www.gbif.org/resources/2514)
 - [Best practice guide for compiling, maintaining, disseminating national species checklists](http://www.gbif.org/resources/2306)
 - [Best practice guidelines in the development and maintenance of regional marine species checklists](http://www.gbif.org/resources/2357)

Sadly they contradict each other in some areas and there is not a single, complete and up to date document available. This is maintained with the code and if in doubt should contain the most up to date answer.

In addition to GBIF the Catalog of Life has invested a lot of energy into developing a stricter format for publishing taxonomies as Darwin Core archives which is very useful and supported by GBIF indexing:

 - [i4Life Darwin Core Archive Profile](http://www.i4life.eu/i4lifewebsite/wp-content/uploads/2012/12/i4Life-DarwinCoreArchiveProfile.pdf)

 
# dwc:Taxon = NameUsage
The core entity of a checklist archive is a record with a rowType of dwc:Taxon. Don't be mislead by the term "taxon". A record of type "Taxon" is a _name usage_, a generalization of a taxon or a name record. It can be anything between a purely nomenclatoral name record or a fully fledged taxon concept.

## taxonID
dwc:taxonID is the primary key of a name usage record. It must be unique, but can be a local identifier only. It is the value that other foreign key terms refer to in order to establish relations between name usages. The following "foreign key" terms must reference an existing taxonID value in some record:

 - dwc:parentNameUsageID
 - dwc:acceptedNameUsageID
 - dwc:originalNameUsageID

## scientificNameID
The scientificNameID is used to declare a nomeclators identifier for a given name. It is _not_ used to establish relationships inside the dataset. Ideally dwc:scientificNameID should hold a resolvable, globally unique id, e.g. a DOI, LSID or URL.

## taxonConceptID & taxonAccordingTo
The taxonConceptID can be used to identifiy the exact concept of a name usage. This is mostly useful to assert that different name usages refer to the same concept albeit using a different name. It is best used with a globally unique identifier that can be referred to across various datasets. The taxonConceptID should _not_ be used to establish additional relations inside the dataset.

In order to label different taxon concepts based on the same name an additional reference is usually given (sec. / sensu). dwc:taxonAccordingTo should be used for this:

    taxonConceptID: doi:10.8912:BA6504FA-5EE6-4464-B96B-765891017D3D
    taxonAccordingTo: Frey 1989


## scientific names
scientific names are accepted in 3 different formats:

__1 - entire name with authorship__ 

given as dwc:scientificName

    scientificName: Abies alba var. alpina Mill.

__2 - canonical & author__

    scientificName: Abies alba var. alpina
    scientificNameAuthorship: Mill.

__3 - atomized__

    genus: Abies
    specificEpithet: alba
    infraspecificEpithet: alpina
    scientificNameAuthorship: Mill.
    verbatimTaxonRank: var.
    taxonRank: variety

## rank
Try to use a controlled rank vocabulary or at least english rank names for dwc:taxonRank. For example the [GBIF rank enumeration](http://gbif.github.io/gbif-api/apidocs/org/gbif/api/vocabulary/Rank.html)

## Verbatim vs ID terms
Terms used to express relations in a dataset often exist in two forms in Darwin Core. One that takes a verbatim scientific name and one that takes an identifier (taxonID) of another record inside the dataset. If possible prefer to use the id based term which is less doubtful. The following term twins are specified in Darwin Core:

 - parentNameUsage & parentNameUsageID
 - acceptedNameUsage & acceptedNameUsageID
 - originalNameUsage & originalNameUsageID

## Classification
The classification can be published in 2 main ways. Prefer the normalized form over the denormalized if possible as it is more precise and offers a flexible hierarchy with any number of ranks.

### Normalized classification
Normalized checklists use a parent child relation to express the taxonomic classification.
Use dwc:parentNameUsageID to refer to the taxonID value of the next higher parent name usage.

    taxonID: 100
    parentNameUsageID:  
    scientificName: Chordata
    taxonRank: phylum

    taxonID: 102
    parentNameUsageID:  100
    scientificName: Vertebrata
    taxonRank: subphylum

### Denormalized classification
A denormalized name usage record specifies the major Linnean ranks as verbatim canonical names. The complete list of all supported ranks are:

 - dwc:kingdom
 - dwc:phylum
 - dwc:class
 - dwc:order
 - dwc:family
 - dwc:genus
 - dwc:subgenus

Example:

    scientificName: Sciurus vulgaris Linnaeus, 1758
    kingdom: Animalia
    phylum: Chordata
    class: Mammalia
    order: Rodentia
    family: Sciuridae
    genus: Sciurus
    subgenus: Sciurus
    

## Synonyms
Synonyms and accepted names are both expected to be found as core records in a dwc checklist archive. Synonyms can have the following properties:

 - dwc:acceptedNameUsageID to refer to the accepted name usage record. It must be an existing taxonID. It is legal for accepted taxa to refer to themselves
 - synonyms should not be chained, acceptedNameUsageID should point to an accepted taxon not another synonym
 - [taxonomicStatus](http://gbif.github.io/gbif-api/apidocs/org/gbif/api/vocabulary/TaxonomicStatus.html) can indicate kind of synonym, e.g. homotypic
 - acceptedNameUsageID takes precedence over parentNameUsageID

Example:

    taxonID: 100
    scientificName: Cygnus columbianus (Ord, 1815)
    taxonomicStatus: accepted

    taxonID: 101
    scientificName: Anas columbianus Ord, 1815
    taxonomicStatus: homotypic synonym
    
## Basionym
The basionym (or protonym) of a name can be indicated either as a verbatim name or as a reference to another name usage record in the dataset. If you can prefer the reference approach using dwc:originalNameUsageID
 
Example:

    taxonID: 100
    scientificName: Cygnus columbianus (Ord, 1815)
    originalNameUsageID: 101
    
    taxonID: 101
    scientificName: Anas columbianus Ord, 1815

## namePublishedIn
 - full reference citation of the publication the name was originally first published in
 - use namePublishedInID to express a DOI or URL of the publication

## Dataset constituents
An full dataset can be made up of subdatasets, known as constituents to GBIF. For example the Catalog of Life contains several GSD (Global Species Dataset) subdatasets.

Each constituent can have its own EML metadata file which is referred to be the core record using the datasetNameID term. A corresponding EML file with the same name as the datasetNameID and a suffix ".xml" should live under a datasets subfolder inside the archive.

# Extensions supported by GBIF
See the [GBIF extension enumeration](http://gbif.github.io/gbif-api/apidocs/org/gbif/api/vocabulary/Extension.html) with their corresponding rowType or the list of [GBIF checklist extension definitions](http://rs.gbif.org/extension/gbif/1.0/).

A detailed description of the usage for each extension coming soon.

## Description
    rowType = http://rs.gbif.org/terms/1.0/Description

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/description.xml](http://rs.gbif.org/extension/gbif/1.0/description.xml)


## Distribution
    rowType = http://rs.gbif.org/terms/1.0/Distribution

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/distribution.xml](http://rs.gbif.org/extension/gbif/1.0/distribution.xml)


## Identifer
    rowType = http://rs.gbif.org/terms/1.0/Identifier

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/identifier.xml](http://rs.gbif.org/extension/gbif/1.0/identifier.xml)


## Multimedia
    rowType = http://rs.gbif.org/terms/1.0/Multimedia

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/multimedia.xml](http://rs.gbif.org/extension/gbif/1.0/multimedia.xml)


## References
    rowType = http://rs.gbif.org/terms/1.0/Reference

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/references.xml](http://rs.gbif.org/extension/gbif/1.0/references.xml)


## SpeciesProfile
    rowType = http://rs.gbif.org/terms/1.0/SpeciesProfile

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/speciesprofile.xml](http://rs.gbif.org/extension/gbif/1.0/speciesprofile.xml)


## Typification
    rowType = http://rs.gbif.org/terms/1.0/TypesAndSpecimen

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/typesandspecimen.xml](http://rs.gbif.org/extension/gbif/1.0/typesandspecimen.xml)


## VernacularName
    rowType = http://rs.gbif.org/terms/1.0/VernacularName

For supported terms see [http://rs.gbif.org/extension/gbif/1.0/vernacularname.xml](http://rs.gbif.org/extension/gbif/1.0/vernacularname.xml)
