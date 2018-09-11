# Editorial Tooling
An overview of the main tools needed to assembly a scrutinized and provisional Catalogue of Life. Most tools should correspond to some interactive UI pages and the ideas here can be used to define UI & backend requirements.

## Terminology
**MC**: CoL management classification
**Dataset**: An entire dataset as provided by some source archive
**Datasubset**: A subset of a dataset with its own metadata
**Sector**: A single higher taxon from a data(sub)set that acts as the root for a taxonomic sector in the CoL


# Editorial Dashboard
A quick overview about all relevant states, highlighting work to be done by the editorial board.

- dataset table (all/provcat/scrutcat)
    - sorted by name, size, last success import, date registered
    - offline, last import state, last successful import & counts, avg rec/second
    - show issue overview, relative to last import (num up/down)
    - show unhandled issues for scrutcat
    - search & filter?
- register new dataset URL, format & type
- import overview
    - toggle continuous importing 
    - running imports, highlight long running or stuck imports
    - import queued: show next dataset, waiting time in queue and total queue size
        - expandable to show all queued imports. Cancel option
- GBIF registry sync state
    - toggle sync
    - last sync time & changes
- system health overview ?


# Data(sub)set Editorial Page
Shows a single dataset or datasubset with editorial focus.

- modify metadata (title, abstract, contacts, homepage, etc)
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
- metrics
    - issue overview
- unread discussions
- import default values (nomCode, vernacular lang, isNomenclator)
- tab with table of editorial decisions (sourceId, name, authorship, action, issue reason)
    - blocked names
    - status changes
    

# Editorial Review Portal
Editorial portal for detailed data review and decisions on record level.
- restrict to dataset or sector
- faceted search by rank, issue, taxon & name status (incl bare names), name type, nomCode, higher taxon?
- tree browser
- map taxon onto MC by selecting its MC parent
    - optionally allow suppression of other MC taxa
- remember records for comparison or later review (multiple lists needed?)
- remember records with comment for review queue


### Record Comparison
Shows several names/taxa next to each other to resolve issues, e.g. duplicates
- tabular view
- block name from CoL sector
- change taxon status?
- change name status? Link to dedicated names index editor?


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
Manage the CoL tree with monomials, rank, species estimate & estimate reference

# Patch Editor
Create new patches for name & taxa, deactivate existing one

# Review Queue
The review queue page is targeting the dataset publisher to improve the data.
- list issues like in dataset review
- show records highlighted by editorial decision
