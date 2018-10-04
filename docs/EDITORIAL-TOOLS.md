# Editorial Tooling
An overview of the main tools needed to assembly a scrutinized and provisional Catalogue of Life. Most tools should correspond to some interactive UI pages and the ideas here can be used to define UI & backend requirements.

## Terminology
- **MC**: CoL management classification
- **Dataset**: An entire dataset as provided by some source archive
- **ColSource**: A subset of a dataset with its own metadata, often to represent a GSD within a *cluster* such as WoRMS
- **Sector**: A single higher taxon from a dataset or ColSource that acts as the root for a taxonomic sector in the CoL, with rules for name exclusion, rank exclusion etc


# Main Editorial Dashboard
The main dashboard to give a quick overview about all relevant states, running and failed imports and highlighting work to be done by the editors.

- dataset table (show all/provcat/scrutcat only) [mockup](https://github.com/Sp2000/colplus/blob/master/mockups/Datasetlist.png) [beta](http://test.col.plus/dataset) 
    - sorted by name, size, last success import, date registered
    - columns: last import state with error message "popup", last successful import & counts, number verbatim, nomCode, issues, relative to last import (num up/down)
    - search & filter by import status?
- register new dataset URL, format & type
- import overview
    - toggle continuous importing 
    - running imports, highlight long running or stuck imports
    - import queued: show next dataset, waiting time in queue and total queue size
        - expandable to show all queued imports. Cancel option
- GBIF registry sync state
    - toggle sync
    - last sync time & changes
- system health overview ???


# Data(sub)set Editorial Page
Shows a single dataset or datasubset with editorial focus.

- modify metadata (title, abstract, contacts, homepage, etc)[mockup](https://github.com/Sp2000/colplus/blob/master/mockups/Datasetmeta.png) [beta](http://test.col.plus/dataset/1007/meta)
- dataset specific
    - list datasubsets
- datasubset specific
    - show parent dataset
- metadata lock toggle (dont overwrite by parent or imported metadata)
- provCat contributor?, num of new names
- assembly configs
    - useNomenclatorSpelling
    - useNomenclatorSynonyms
    - ignoreVernaculars
- list mapped sectors
    - size, last accepted, names changed since last accepted
- unread discussions
- import default values (nomCode, vernacular lang, isNomenclator)
- names diff to last import (deleted, added, modified spelling), counts only linking to search or own tab???


## Editorial Decisions Tab
sourceId, name, authorship, action, issue reason
- list editorial decisions
    - blocked names
    - status changes

## Metrics Tab 
- [mockup](https://github.com/Sp2000/colplus/blob/master/mockups/Metrics.png)
- issue overview 
    
## Tree Browser Tab
Editorial portal for detailed data review and decisions on record level.
- tree browser [mockup](https://github.com/Sp2000/colplus/blob/master/mockups/Exploretax.png) [beta](http://test.col.plus/dataset/1028/classification)
- link to taxon page

## Data Search Tab
- faceted search by rank, issue, taxon & name status (incl bare names), name type, nomCode, higher taxon?
- map taxon onto MC by selecting its MC parent
    - optionally allow suppression of other MC taxa
    - optional excludes of subtrees
- remember records for comparison or later review (multiple lists needed?)
- remember records with comment for review queue

## Discussions Tab
List all discussion threads existing for a dataset. Order by latest activity?

## Record Comparison View
Shows several names/taxa next to each other to resolve issues, e.g. duplicates
- tabular view
- block name from CoL sector
- change taxon status?
- change name status? Link to dedicated names index editor?


# Name page
Single name shown with all its information and relations.
- show nomenclatural info
- objective synonymy, if known in chronological order
- name relations
- link to currently accepted (=taxon) name
- issues
- potential homonyms, i.e. other names with the same canonical name ignoring authorship
- link to name index
- verbatim data


# Single Taxon page
- [mockup](https://github.com/Sp2000/colplus/blob/master/mockups/Taxonpage.png) [beta](http://test.col.plus/dataset/1010/taxon/Fis-22711)
- classification
- children
- full synonymy
- taxon info
- vernacular names
- distributions
- verbatim data


# Discussion page
A "talk page" discussing some topic or issue.
Can be used for public (authenticated) comments, but also for conversations between CoL editors and data publishers. Github issues as the brainchild.
- markdown comment thread
- subscriptions for users (allow to change subscription status)
- links to names in comments, markup


# User page
User page that links to the GBIF user account but also shows Clearinghouse user activity.
- link to GBIF user account page to change settings (email, password, etc)
- list of subscribed discussions
- list of supplied patches


# Assembly Console
- show running builds
- trigger builds
- release catalogues
- prov cat
    - list of sources in prio order with maxRank, nomCode, nomenclator status, number of all & new names
    - allow to reorder sources
- scrut cat
    - list all sectors, last accepted, newer dataset available
    - browse management tree with sector roots


# Classification editor
Manage the CoL tree with name, rank, species estimate & estimate reference
- browse tree
- add, remove, move taxon
- add synonym
- species estimates form with reference


# Patch Editor
Create new patches for name & taxa or deactivate (but not delete) existing ones. Stored in database ideally in the native format as a single dataset, with subsets being a single patch. Alternatively a single NameUsage table ala dwc with denormed classification?


# Review Queue
The review queue page is targeting the dataset publisher to improve the data.
- list issues like in dataset review
- show records highlighted by editorial decision
