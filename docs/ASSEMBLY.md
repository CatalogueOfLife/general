# Catalogue Assembly
The Catalogue of Life Plus project intends to produce two aggregated catalogues that are assembled from various underlying sources. A *scrutinized catalogue* (sCat, The Catalogue of Life) which is stitched together from many curated, non overlapping sources, each representing a single, authoritative view on a certain taxonomic *sector*. And a *provisional catalogue* (pCat) which extends the scrutinized catalogue by adding many more names and taxa by mostly automated merging of information from a wider and overlapping range of taxonomic and nomenclatural sources.

Both of these assembly processes are semi-automated and need editorial work and reviewing in order to build a high quality catalogue. This is especially true for the scrutinized catalogue.

# Source Datasets
The first step towards the assembly of both catalogues is to get the source datasets into a common structure and to make sure there are no obvious syntactical or semantic errors. The CoL Clearinghouse is used to register URL accessible datasets in various standard formats and import them into a common database model. The Clearinghouse importer interprets literal string data into well known enumerations, objects and relations, thereby flagging rectified or detected potential problems in the entire range of basic string reading to very specific taxonomic integrity checks.

The content of each dataset in the [Clearinghouse can be browsed and searched](https://sp2000.github.io/colplus/api/api.html) on its own with the primary purpose to allow a quality assesment and detailed data review. For that reason every record also keeps track of the exact verbatim data as it came in and all fallged importing issues can be used for filters to navigate data.


## Dataset publication
Datasets that should be added to the CoL need to be accessible in a standard format from a public URL. The URL should remain the same if new versions of the dataset are published. DwC Archives and the CoL ACEF format are handled currently. It is expected to also cover TCS or newly defined formats in the future. Converting data from highly custom formats into DwC-A or ACEF is expected to be done either by the publishing source directly or with the help of the CoL data manager & the GBIF helpdesk. For sources in relational databases, Excel spreadsheets or CSVs the GBIF IPT can be used to assist in this task. 

All existing GSDs inside the CoL have been exported from the CoL global assembly database into [ACEF compliant files](https://github.com/Sp2000/colplus-repo) so that the current version of the data is immediately available even if the original sources are not yet published in a standard format. This can be repeated at any time and should at least be done once more when leaving behind the old workbench.


## Dataset registration
In order to add new datasets the URL to their data needs to be registered manually by editorial or publisher intervention. The Clearinghouse has an internal registry of datasets, but is also able to sync dataset metadata from the [GBIF registry](https://www.gbif.org/dataset/search?type=CHECKLIST). This allows to automatically keep track of new checklist datasets from IPTs or the very active [Plazi initiative](https://www.gbif.org/dataset/search?publishing_org=7ce8aef0-9e92-11dc-8738-b8a03c50a862). The ACEF data format is currently not supported in GBIF though.

Dataset registration will also include a few more settings that determine how a dataset is imported into the Clearinghouse and which can be changed at any time. Currently this includes:

 - catalogue: indicates a dataset is to be considered as a source for the scrutinized or provisional catalogue
 - type: indicates whether the dataset is primarily a nomenclatural list, taxonomic checklist or something other (need to be specified, e.g. a list of type specimens, paper on ecology of North Sea, Red List, European Environment Agency list, etc.); global, regional or thematic
 - code: the nomenclatural code context which should be applied to all data in case the dataset is restricted to a single code [rules should be discussed. Example: rule for authorstrings in fungi names in the CoL is a hybrid between ICZN and botanical Code; CoL allows infraspecific markers as var. and f. in zoological synonyms/combinations]. This allows for better parsing and interpretation of names data in ambiguous situations.
 - importFrequency: an indication how frequently new dataset versions should be imported into the Clearinghouse

Dataset metadata will be extracted from the data if available for both ACEF and DwC-A, but can also be changed by editorial decision.

## Dataset Import
Once registered a dataset can be imported into the Clearinghouse. Imports can be triggered manually or automatically scheduled by the system, preferring datasets that have not been imported for a long time. An import queue is managed internally which can be queried and modified via the admin API.

A dataset import does many things. Most notably:
 - convert the data into the [native CoL data model](dbschema.png) with a separation of [names](NAMES.md), taxa, synonyms, name relations, distributions and references. Scientific names are parsed into their individual components.
 - normalize flat classifications into a parent child relation with a single record for higher taxa with the same name & classification
 - normalize citation strings creating a single reference for the same citation
 - generic data cleaning: resolve character encodings, replace xml, unicode or html entities, remove html tags
 - parse names into individual parts
 - flag data issues of different severity (info/warning/error). This is a large set of checks that will be continously extended. Example checks are:
    - parsed name inconsistencies
    - referential integrity problems (id terms)
    - potential chresonyms
    - duplicate names or references
    - classification loops, synonyms of synonyms, etc.
    - potential data truncation
 - match names against the names index, adding or removing names for trusted datasets
 - generate dataset import statistics: number of names by status, rank, issues etc. enabling time series for historic imports


# Assembly of the Scrutinized Catalogue
All data passes a number of steps before it ends up in the public Catalogue of Life (sCat):

 1. Structural conversion of sources to standard formats
 2. Data import into the Clearinghouse
 3. Define relevant sectors from dataset and attach them to the CoL management cssification (MC)
 4. Review of sector, online report back to editors and dataproviders, reasonable editorial modifications in the sector, fixes in the source dataset and reimport revised version until accepted or with a scheduled new update
 5. Integrity checks between the sectors of source database and remaining parts of the Catalogue of Life; reasonable editorial modifications in the CoL, fixes in the source datasets and reimport revised versions with a scheduled new update  
 6. Assemble a preliminary CoL (sCat) from accepted sectors for review [yr: such approach was rejected in EC 4D4Life project]
 7. Review and rebuild or release CoL (sCat) [yr: see above]

## Managing the CoL Management Classification
At the heart of the CoL (sCat) lies the [management classification of the CoL](http://www.catalogueoflife.org/col/info/hierarchy). It is a special dataset in the Clearinghouse that contains a taxonomic tree down to genus level as a snapshot of latest version of integrated CoL; it may include synonyms and should offers species estimates for higher groups together with account of acctual species ans children taxa in the sector which show up in the public portal and can be used for gap analysis. The MC is maintained by the CoL editorial board using a basic tree editor with species estimates forms provided by the Clearinghouse.

The MC can be seen as the backbone of the CoL (sCat) where all other sources are attached to.

![mc](management-classification.png)

## Taxonomic Sectors & Col Sources
A single taxonomic group attached to the MC is called a taxonomic *sector*. A single source dataset may provide multiple sectors, e.g. Fishbase, WoRMS or ITIS. In the case of [Fishbase](http://www.catalogueoflife.org/annual-checklist/2017/details/database/id/10) the CoL (sCat) groups all 6 sectors under the same umbrella, listing them all as Fishbase with the same contact and credits. This is different for larger "cluster" source like WoRMS or ITIS that provide many sectors, but like to be cited differently for most sectors, e.g. [WoRMS Bryozoa](http://www.catalogueoflife.org/annual-checklist/2017/details/database/id/81) and [WoRMS Hydrrozoa](http://www.catalogueoflife.org/annual-checklist/2017/details/database/id/112).

Sectors can therefore be grouped into CoL sources that provide common citation metadata which can be maintained by the editorial board and in absence of inputs defaults to the metadata of their parent dataset. [yr: this proposal should be agreed with claster data providers: WoRMS, ITIS, Species Files, Species Fungorum plus, Paris cluster, FADA]

Selecting appropriate taxonomic sectors and mapping them to the CoL Management Classification is an important editorial decision. Taxonomic groups that should end up in the CoL need to be mapped at least once from the source datasets to the CoL management classification. In the simplest case a single higher taxon from a source dataset can be placed directly onto the management classification. More control is provided to filter names & taxa in sectors by rank and allow exclusion of included groups, for example a specific genus or family because they are treated in a different source already. Nested sectors that attach and thereby replace a group in another sector is another option. Once defined, sectors will remain when the underlying dataset is updated. [yr: some sectors of the CoL are built as proto-GSD: a checklist is combined on species level, e.g. 3 species in the genus are taken from NZIB, 2 from CoL-China and 20 from ITIS]


## Data Review
Entire datasets or specific sectors can be reviewed to find problems and report them back to the data provider. Once revised and updated dataset can simply be reimported into the Clearinghouse. Dataset also might be modified directly in Clearinghouse to avoid delay of publication, and replaced with revised and updated dataset during scheduled update.

[yr: in a present CoL procedures we are replacinf old version of dataset with a new one. What is a provision for the procedure in CoL+? How to keep a history of "species record" modifications? How it will work in the case of replacement of sector previously received from one dataprovider and then replaced with a sector from a new dataprovider?]

A dataset and sector summary will help identifying problems. For certain issues like duplicates or potential chresonyms a comparison view of several records is needed that allows the editor to block names from entering the Catalogue or modify their status. Editor also may need to modify spelling (e.g. misspelled genus name in a single binomial; misspelled species epithet ("crista galli" ->  "crista-galli"); misspelled species author; etc.).

Trusted sources that already have a review process implemented themselves can be marked to be included in the CoL automatically with every new dataset version imported. However, those authomated datasets should pass data integrity checks with each update and detected cases should be reported to the CoL editor and data provider for correction in the Clearinghouse and/or in the source database. In some cases, manual acceptance of updated sector for inclusion into the CoL needs to take place everytime through editorial decision. (A new sector (i.e. "sister taxon") from existing dataprovider is alawys require manual acceptance; whereas a newly added child in the accepted sector does not need manual acceptance. For example, if WoRMS is an authorised provider for phylum Mollusca, there is no need for manual acceptance of growing number of children clases, orders, etc. However, at particular stage of WoRMS Mollusca growth, the Clearing house should detect and report overlap with exinct taxa taken from PaleBioDB. Editorial decision will be needed on whether to block order †Ammonoidea from WoRMS Mollusca and continue taking it from PaleBioDB, or switch to WoRMS Mollusca as an authorised dataprovider for ammonits and delete PaleoBioDB data). 

Selected sectors from Provisional Catalogue of Life (pCat), those which passed a revirew and recommended by Taxonomy Group and CoL Editorial Board as trusted sources, can be accepted as a dataset in gap areas, or as a replacement for existing proto-GSDs, or as a replacement for existing dataprovider with low quality or incomplete data.

## Discussion Threads
The Clearinghouse provides generic [discussion threads](https://github.com/Sp2000/colplus/blob/master/docs/EDITORIAL-TOOLS.md#discussion-page) that consist of a description which can link out to any number of name, taxon or reference entities, status and a flat comments timeline. A discussion can be used for numerous things, e.g. to capture feedback and to communicate data issues betweem data reviewers [yr: do you mean "CoL editors" or "GBIF commentators"?] and  data publishers [yr: do you mean "CoL assembly team" or "dataproviders"]. 
[A number of names from a dataset sharing the same problem can thus be grouped as a discussion which keeps track of its state (e.g. under review, accepted, done). By using a discussion linked to records with issues we can track matching editorial decisions and recognise issues that still need editorial attention from those which have been addressed already. yr: I would suggest to keep a wiki space for broad discussions in CoL+ infrastructure, and restrict editorial friont end in Clearinghouse for practical editorial decisions made by CoL dataproviders and CoL editors]

## Assembling a preliminary CoL (sCat)
Automatically or manually accepted sectors are copied to a preliminary CoL (sCat) so they are immutable and available for subsequent Catalogues [yr: ?]. The preliminary CoL (sCat) can only by modified by replacing entire sectors or changing the management classification (MC) itself [yr: ?]. When a sector is attached to a higher part of the management classification it becomes the authority for that part of the tree and defines the included classification which can be different from the hidden MC. 

[In order to avoid tedious marking of duplicate names we propose to automatically exclude exact duplicates, i.e. the name, status & classification is identical. Hmm, it will not work like that. I can give example of Distoleon tripunctatus (Navás, 1914) at http://www.catalogueoflife.org/col/search/all/key/Distoleon+tripunctatus/fossil/1/match/1 and Genysa decorsei (Simon, 1902) at http://www.catalogueoflife.org/col/search/all/key/Genysa+decorsei/fossil/1/match/1 - in both cases dataproviders insist to keep both species in the CoL with Provisionally Accepted names. Unfortunately, they failed to add explanation to the cases in Additional Information field] [When the same accepted name appears with different information multiple times within the same sector or across sectors we can either block such names manually (and persist these edits for subsequent updates) or default to a priority list of sectors to be managed through editorial decision. This case and rules should be discussed additionally: there is a class of duplicated names which should be excluded automatically, and there are classes which should be assessed "manually"

Similarily chresonyms, manuscript and placeholder names known by the [Names Index](#names-index) are ignored and not copied to the CoL (sCat). [yr: ?]

When a sector is copied to the preliminary Catalogue missing transliterations for vernacular names are generated automatically.

Optionally a dataset can be configured in the CoL to prefer the exact name spelling from a nomenclator and to apply the objective synonyms from the nomenlcator after written prermission from the dataprovider.
    
The preliminary CoL can be browsed and searched in a format of final product [? just as any other dataset in the Clearinghouse for review before it gets released]. When released it will be copied into the immutable CoL archive that drives the public portal. Each edition of the CoL should have an option of republishing in the public portal. (For example, a single but severe bug in the content or metadata may require to republish monthly edition withing few days after first release: title of the publication remains the same, but timestamp of release is different - reasons for such practice might be discussed further).  


# Assembling the Provisional Catalogue
Assembling a Provisional Catalogue of Life (pCat) with a much broader names base is done by merging many overlapping datasets onto the scrutinized catalogue. 


## Names Index
The names index is a set of unique names that powers name matching and can be used to identify the same name across different or within the same dataset. Name matching handles gender stemming and simple but common misspellings in binomials. It also does a rather fuzzy author comparison for equal binomials.

It is currently planned that the names index will be identical to the names stored in the provisional catalogue. [yr: ?] All occurrences of a name in any matched dataset will be tracked and if none is left, e.g. because a dataset has removed or modified an erroneous name, it will also be logically deleted in the names index (note that identifiers will remain forever). As a consequence importing new datasets will potentially modify the provisional catalogue on the fly. Name relations will not be considered for the names index, just bare names. Records from nomenclators will take precedence when deciding on a canonical form for a name. Name strings which are clearly not names or classified as placeholders by the name parser will be ignored.

The status of a name can be used to indicate chresonyms or manuscript names. An editorial interface for basic name properties, initially at least the status, is provided which allows the exclusion of names from entering the final Catalogue through editorial decisions.


**TODO: Work in progress !!!**
Draft ideas to cover here: 
- microtaxonomy patches stored in db for sCat and source overrides
- copy scrutinized cat? What about the scrut cat lagging behind because of manual review required?
- names are added dynamically as trusted datasets are imported (see names index)
- merge data about the same name based on names index match
- logically delete all names that no source contains anymore (should be done post dataset import already)
- process relevant dataset by priority, then by reverse date published (eg Plazi)
    - insert only ranks configured, default to family and below
- detect basionyms by the same epithet and authorship within families
- resolve taxonomic status, allow just one accepted name within a homotypic group [yr: it will be difficult to judge without detailed citation of the type. Misatkes are highly possible. Better to leave judgement for the expert in the taxon - task for professional networks around appropriate nomenclators]

