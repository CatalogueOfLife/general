# Development Roadmap


## System Maintainance

### Bug Fixing
The primary task is to keep the systems running and fix bugs blocking the CoL editor to work on monthly releases.
Issues labeled with `bug` should be addressed as soon as possible for both the API & UI.

### System monitoring, backup
Ongoing but low maintanence work doing 
 - system monitoring
 - restarting of broken services
 - analyze logs for errors and file bug issues

### Continuous improvements to parsing
Name parsing is an essential feature of the system that lays the foundation for many features.
Bad name parsing regularly needs to be fixed in the name parser.
With the new name parser configs it is also possible to manage overrides now directly in the system for the rare, specific cases.



## Features

An overview of to be developed features for the CoL infrastructure, both on frontend and backend.
These features are not arranged in a particular order. The following milestones section will try to group them instead.


### User Management
 - UI: admin & project section to manage user roles and editor permissions per dataset
 - UI: show user info and personal settings

### Name Parser Configs
 - UI: admin section to manage name parser configs

### Project Refactoring
 - API/UI: use consistent parameters for datasetKey, catalogueKey, projectKey, sourceDatasetKey, etc
 - UI: change all wording to use "project"

### Taxon/Name Details
 - UI: show taxon children
 - UI: merge name information into taxon pages
 - UI/API: show related names on via NamesIndex

### NamesIndex Management
 - UI/API: merge/split/modify names in the names index

### Stable Name Ids
 - API: keep name & taxon ids stable based on name index ids during project releases

### Stable Taxon Ids
Prerequisite: Stable Name Ids

- Keep stable taxon ids independent from the name, comparing synonymies and included taxa instead


### Continuous Indexing for Plazi
  - API: improve db partitioning suitable for hundreds of thousands of small dataset

### Sync Merge Mode
 - API:

### ColDP/DwC-A Export
 - API/UI: export datasets and parts of it as ColDP or DwC-A archives

### DOI for Exports
 - API/UI: generate a DOI for exports and store exports and DOI metadata

### Extended Catalogue Assembly
 Prerequisite: Stable Name Ids, Continuous Indexing for Plazi, Sync Merge Mode

 - API/UI: provenance matrix for a name usage

### Extended Catalogue Release
 Prerequisite: Extended Catalogue Assembly

 - DATA: content management
 - DATA: data review/audit

### Taxon Matching 
 - API: port gbif nub lookup code

### Public Portal
 Prerequisite: Stable Name Ids, Taxon/Name Details

 - [All UI & content issues]( https://github.com/CatalogueOfLife/portal/milestone/1)
 - [All API issues](https://github.com/CatalogueOfLife/backend/milestone/16) inlcuding also all prerequisites

### Extended Portal
 Prerequisite: Extended Catalogue Assembly, Public Portal

 - [All UI & content issues]( https://github.com/CatalogueOfLife/portal/milestone/2)

### Legacy Docker Services
 - Geoff: run historical annual editions via their historical AC interface in docker

### CoL Directory Service
 - API/UI: manage CoL team members and other roles
 - API: [create mailinglists for team](https://github.com/CatalogueOfLife/general/issues/73)
 - UI: Show CoL team members in portal

### Hosting Migration
 Prerequisite: Legacy Docker Services

 - GBIF: [migrate DNS](https://github.com/CatalogueOfLife/general/issues/70)

### User Discussion
 - API/UI: manage discussion threads
 - UI: integrate discussion to taxon pages

### Reference Details
 - UI: show a reference details page

### Treatments
 - UI: show Plazi html treatments on taxon pages

### Multimedia
 - UI: show multimedia on taxon pages

### Review Queues
 - API/UI: manage GSD role 
 - API/UI: offer GSD specific review queue pages

### Improved import issues





## Milestones 2020

### Milestone: Extended Catalogue

### Milestone: CoL Portal



## Milestones 2021

### Milestone: User comments

### Milestone: Review Queues

### Milestone: Taxon Ids

