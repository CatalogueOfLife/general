# Catalogue of Life Assembly

The Catalogue of Life is build by assembling non overlapping taxonomic groups taken from source datasets. We call these groups taxonomic *sectors* and a single source dataset may contain multiple sectors, e.g. ITIS or WoRMS. Selecting appropriate sectors for the CoL is an important editorial decision. The editorial board also reviews the quality and overlap of the original sources as well as the final resulting Catalogue before it can be released.

All data passes a number of steps before it ends up in the public CoL:

 1) Structural conversion to standard formats
 2) Data import into the Clearinghouse
 3) Define relevant sectors from dataset once and attach them to the CoL management classification (MC)
 4) Review of sector, online report back to source, reimport revised version until accepted
 5) Assemble a preliminary CoL from accepted sectors for review
 6) Review and rebuild or release CoL

# Data Prerequisites
Datasets that should be added to the CoL need to be accessible in a standard format from a public URL. DwC Archives and the CoL ACEF format are handled currently. This can be extended to TCS or newly defined formats in the future. Converting data from custom formats into DwC-A or ACEF is expected to be done either by the publishing source or with the help of the CoL data manager & the GBIF helpdesk. The URL should remain the same if new versions of the dataset are published.

All existing GSDs inside the CoL have been exported into ACEF compliant files so that the current version of the data is immediately available even if the original sources are not yet published in a standard format.

# Dataset registry
Within the Clearinghouse all datasets have a URL. These dataset URLs need to be registered manually by editorial or publisher intervention. The Clearinghouse has an internal registry of datasets, but is also able to sync datasets from the GBIF registry. The ACEF data format is currently not supported in GBIF though.

Dataset registration will also include a few more settings that determine how a dataset is processed in the Clearinghouse and which are to be maintained by the CoL editor. Currently this includes:

 - trustedNames: a flag that indicates trusted datasets which will be allowed to insert new names into the names index
 - nomenclator: a flag to indicate a dataset is a trusted nomenclatural source
 - code: the nomenclatural code context which should be applied to all data in case the dataset is restricted to a single code. This allows for better parsing and interpretation of names data in ambiguous situations.

Dataset metadata will be extracted from the data if available for both ACEF and DwC-A, but can also be changed by editorial decision.

# Dataset Import
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

## Names Index
The names index is a set of unique names that powers name matching and can be used to identify the same name across different or within the same dataset. Name matching handles gender stemming and simple but common misspellings in binomials. It also does a rather fuzzy author comparison for equal binomials.

It is planned that the names index will be identical to the names stored in the provisional catalogue. All occurrences of a name in any matched dataset will be tracked and if none is left, e.g. because a dataset has removed or modified an erroneous name, it will also be logically deleted in the names index (note that identifiers will remain forever). As a consequence importing new datasets will potentially modify the provisional catalogue on the fly. Name relations will not be considered for the names index, just bare names. Records from nomenclators will take precedence when deciding on a canonical form for a name. Name strings which are clearly not names or classified as placeholders by the name parser will be ignored.

The status of a name can be used to indicate chresonyms or manuscript names. An editorial interface for basic name properties, initially at least the status, is provided which allows the exclusion of names from entering the final Catalogue through editorial decisions.

# Taxonomic Sectors
Taxonomic groups that should end up in the CoL need to be mapped at least once from the source datasets to the CoL management hierarchy (see below). In the simplest case a single higher taxon from a source dataset can be placed diretly onto the management classification. More control is provided to define sectors by allowing multiple root groups and also exclusion of included groups, for example a specific genus or family because they are treated in a different source already. Nested sectors that attach and thereby replace a group in another sector is another option. Once defined, sectors will remain when the underlying dataset is updated.

Sectors in the CoL correspond to the source databases currently listed in the annual checklist. They have distinct metadata (title, contacts, logo, etc.) that can be managed by the CoL editor, but defaults to its parent dataset metadata.

# Data Review
Entire datasets or specific sector subsets can be reviewed to find problems and report them back to the sources. Once revised an updated dataset can simply be reimported into the Clearinghouse.

A dataset and sector subset summary will help identifying problems. For certain issues like duplicates or potential chresonyms a comparison view of several records is needed that allows the editor to block names from entering the Catalogue or modify their status.

Trusted sources that already have a review process implemented themselves can be marked to be included in the CoL automatically with every new dataset version imported. Otherwise manually acceptance of a new or updated sector for inclusion into the CoL needs to take place everytime through editorial decision. 

# Assembling a preliminary CoL
Automatically or manually accepted sectors are copied to a preliminary CoL so they are immutable and available for subsequent Catalogues. The preliminary CoL can only by modified by replacing entire sectors or changing the management classification (MC) itself. When a sector is attached to a higher part of the management classification it becomes the authority for that part of the tree and defines the included classification which can be different from the hidden MC. 

In order to avoid tedious marking of duplicate names we propose to automatically exclude exact duplicates, i.e. the name, status & classification is identical. When the same accepted name appears with different information multiple times within the same sector or across sectors we can either block such names manually (and persist these edits for subsequent updates) or default to a priority list of sectors to be managed through editorial decision.

When a sector is copied to the preliminary Catalogue missing transliterations for vernacular names are generated automatically.

The preliminary CoL can be browsed and searched just as any other dataset in the Clearinghouse for review before it gets released. When released it will be copied into the immutable CoL archive that drives the public portal.

# CoL Management Hierarchy
The [management classification of the CoL](http://www.catalogueoflife.org/col/info/hierarchy) is a special dataset in the Clearinghouse. It contains a taxonomic tree down to order or family level, may include synonyms and offers species estimates for higher groups that can be used for gap analysis and which also show up in the public portal.

To guarantee consistency a basic tree editor with species estimates forms will be provided that allow managing a single tree through editorial decisions.
