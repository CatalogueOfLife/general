# The Catalogue of Life plus project

The CoL+ project addresses two primary concerns:

 1) The broadening of covered names so the catalogue can act as a taxonomic backbone for GBIF and similar global activities. This includes both filling true taxonomic gaps with provisional data and adding homotypic synonymies for existing groups.

 1) The migration of essential features of the existing CoL to a new infrastructure to address current sustainability and hosting issues. This includes automation of the assembly process as much as possible to free editorial resources and to reduce latency for updated GSD data to show up in the catalogue.

## System Overview
![](system-overview.png)

## Consolidate GSD publishing
Ideally all (GSD) sources expose their data in the CoL standard exchange format. 
With GSDs managing their data in all kind of custom ways including Word documents, we do not expect to succeed with this ultimate goal. 
Nevertheless CoL+ tries to show some path forward by

 - Defining an updated DwC-A format for exchange
 - running a shared CoL IPT that GSDs can use to upload spreadsheets and text files
 - Use the GBIF helpdesk to install IPTs accessing SQL databases on GSD servers
 - Pilot migration to existing taxonomic editing environments (e.g. Species Files)
 - Pilot migration to a new CoL GSD editor

## Unified Nomenclator
CoL+ will create a unified and broadened nomenclator of scientific names across all life that provides an authoritative view and single place of editorial efforts. 
It is intended to track objective information that can be verified from literature and thus lends itself well to collaborative editing and review. 
This allows much better scaling to complete the coverage of all original and subsequently recombined names and tie them to their publication.

#### Names covered
There are different kind of scientific names with different syntactical structures. 
In the first iteration the nomenclator tries to be comprehensive for all:

 - Linnaean names of all ranks
   - Available names
   - Unavailable names
   - Chresonyms	
 - Named hybrids and nothotaxa
 - Virus names

For the time being this will explicitly exclude:

 - Cultivar names
 - Hybrid formulas
 - Well known OTUs (e.g. BOLD Barcode Index Numbers)

#### Organism groups
In order to track new or changed names of certain organism groups the nomenclator must “classify” names to some degree. 
For homonym disambiguation it is also desirable to have some notion of a coarse classification to not just rely on the often missing or variously spelled authorship. 
With the CoL management classification covering all families this seems like a logical option. 
Other options to be considered include:

 - CoL family
 - Original classification as in publication
 - A few higher groups at various ranks to break down large groups and discriminate homonyms
 - Nomenclatural code alone
 - Nothing at all


#### Open editorial web-interface

 - Registration model (authenticating the user - not limiting rights)
 - Track changes (with attribution)
 - Name verification, review
 - Supports annotation (github issues)
 - Incentivisation strategies

 
#### Webservice API
 See [Nomenclator API draft](https://sp2000.github.io/colplus/api/nomenclator.html)
 - Lexical namestring matching service taking a name string and an optional classification hint to return matching names from the nomenclator. ACTION: Integrate GNI lexical services into the nomenclator (MD, DM)
 - Name search
 - Homotypic Name reconciliation service: Retrieve all (homotypic) names with the same original name
 - Monitor new/changed names, e.g. for GSDs

#### Exchange with existing nomenclators
Various code specific nomenclators are existing already with which information should be shared bidirectionally if possible. 
A simple way to expose changes since a given date is a possible solution to be explored with the existing nomenclature projects including:

 - ZooBank
 - The International Plant Names Index (IPNI)
 - Index Fungorum (IF)
 - The International Fossil Plant Names Index (IFPNI)
 - Algae (under development)
 - ICTV
 - Prokariotic Nomenclature Up-to-date (DSMZ)

Consider editorial blocking of virus & bacteria names as these are well managed externally already.

#### Suggest content for review
Many interesting facts about a name are building up relations with existing data. 
These can often be discovered by software and proposed to the system for later, manual review.

 - Suggest original name publication in BHL
   - BHL Name Indexing 
   - Query additional non GSD sources: GNUB, BioNames
   - Action:  Scope possible sources and mechanisms for linking literature to a name record.	
 - Suggest links to type specimens in GBIF or JSTOR
 - Suggest original name by looking at shared terminal epithets from the same original author within a family. Implemented already in the GBIF Backbone building.


## Taxonomic web editor
Some GSDs have expressed the wish and need to work in a convenient, collaborative online environment. 
There are some existing tools like [TaxonWorks](http://www.taxonworks.org) (successor of Species Files) and the Aphia database that should be considered to be integrated into the CoL workflow. 
On the other hand a new taxonomic webeditor specifically build for CoL will require considerably less resources 
as there is already a name editing environment in the Nomenclator and a management classification down to families provided. 
A minimum taxonomic collaboration environment would therefore provide an API that covers:

 - Managing heterotypic synonymies
 - Integrate with the Nomenclator
 - Manage genera, species and infraspecific taxa
 - Assign genera to families
 - Link to literature for taxonomic assertions
 - Manage fossil flag & geological times
 - Allow discussions

An extended editor out of scope for CoL+ should also be managing additional species information published in the CoL:

 - Vernacular names
 - Distribution ranges
 - Environment information


## New CoL assembly process
Integrating source data both from a GSD and a non complete, e.g. regional source should be an automated and repeatable process as much as possible. 
Editorial decisions should be possible to the degree needed and persisted to allow repeated processing. 
The process incorporating data from the standard format into the live CoL can be divided in two main parts. 
First we will get the data into a staging environment for editorial review where data is linked to the existing catalogue and nomenclator. 
Then the reviewed data gets incorporated into the current CoL, applying the configured editorial decisions.

#### Source staging environment
In order to evaluate source data, provide initial integrity checks and a unified API to work against all sources a staging environment is planned. 
This is the outer tier where all external sources get imported and interpreted before they can be incorporated into the CoL or the Nomenclator. 
The staging environment will be responsible for:

 - Keeping track of (GSD) sources and their metadata, providing a UI for managing them
 - A minimal GSD cache API to return exact original content
 - Assign nameID by name matching to nomenclator
 - Interpretation of controlled vocabularies (e.g. countries, language, rank, taxonomic status)
 - Assign existing stable taxon identifiers to accepted taxa by comparing the synonymy with the existing catalogue
 - Integrity checks and verification, flagging issues for editorial review
 - Slice into families/groups for assembly process

#### Automated assembly
After a source has been reviewed and considered suitable it can be incorporated into the latest version of the Catalogue of Life. 
This process can be influenced by manual editorial decisions, but overall is fully automated. 
A source can be added as either provisional or authoritative. 
Provisional data will only be visible in the extended Catalogue of Life plus. 
Key features of the assembly process are:

 - Persistent editorial decisions
 - Replace entire families from GSD sources
 - Allow overlay of provisional sources for groups not covered by any GSD
 - Integrate nomenclator (homotypic synonyms, literature) as provisional data
 - Create new stable taxon identifiers for missing concepts
 - Final integrity checks and validation
 - Point reviewers to flagged issues.

![](taxon-concept-changes.jpg)

## Migration of current Portal, API & DVD
The core features and URLs of the current portal and webservices have to persist in the updated infrastructure. 
There are several maintenance issues with the current setup. 
The most pressing issues currently are caused by hosting each annual CoL edition in its own virtual machine with the entire software stack 
and the use of outdated software and operating systems for which no security patches are available anymore.

#### Dynamic system
With the introduction of stable taxon identifiers that will resolve forever even if logically deleted the CoL will be a dynamic system exposing the current database as it tracks changes. 
Annual or quarterly snapshots will be preserved as backups and as a source for generating future metrics not yet perceived, 
but they will not be exposed anymore through the webservices or the portal.

#### CoL Portal
With a changed database model the portal as it is will be dysfunctional. Consider the least intrusive and resource efficient way to revive the essential features within the new infrastructure, for example:

 1. Update the current PHP portal code to 
    - Use the new database model for SQL queries
    - or instead use the new API
    - Deal with deleted taxa in visualisation
    - Resolve stable taxon ids
    - Show and mark provisional data
 2. Rewrite the portal in the same framework used for the Nomenclator
    - Reuse the existing portal url layout, html & css
    - Consider if internationalisation is needed

#### CoL Webservices
Similar to the existing portal the webservices should be continued to not break existing users. 
In case the new webservices provide all essential features in a different way consider to deprecate the current API 
at some stage and help users to migrate to the new version.

If the old webservices have to stay as they are it would be best to adapt the current codebase to the new infrastructure as for the portal above.

#### Annual Checklist DVD
Annual checklist DVD will still be produced. 
