# Catalogue of Life (COL)


[Catalogue of Life (COL)](http://www.catalogueoflife.org/) is a collaboration bringing together the effort and contributions of taxonomists and informaticians from around the world. COL aims to address the needs of researchers, policy-makers, environmental managers and the wider public for a consistent and up-to-date listing of all the world’s known species. COL also supports those who need to manage their own taxonomic information and species lists.

You can read more about COL here:

 - Overview: https://www.catalogueoflife.org/about/catalogueoflife
 - The COL Data Pipeline: https://www.catalogueoflife.org/about/colpipeline
 - COL ChecklistBank: https://www.catalogueoflife.org/about/colcommunity#col-checklistbank


## COL ChecklistBank

COL, in partnership with the [Global Biodiversity Information Facility (GBIF)](https://www.gbif.org), maintains [COL ChecklistBank](https://data.catalogueoflife.org), a public checklist repository that brings together the latest versions of the checklists for each taxonomic sector, along with thousands of other species checklists shared by researchers and other contributors. These include summaries from new taxonomic publications, national or local checklists, lists of threatened or invasive species, etc. Checklists published in [supported formats](docs/DATA-FORMATS.md) to this repository are readily discoverable and citable and can be downloaded both in their original form and in a standard interpreted format. All published checklists can be searched, browsed, downloaded or accessed via a standard Application Programming Interface (API). In this way, COL ChecklistBank serves as a rich resource for discovering how any scientific name has been used in different contexts. Some of the names found in these checklists do not yet appear in any community-managed sector checklist, so COL ChecklistBank is also a tool for addressing gaps in COL.


##  Github repositories

CoL+ manages several github repositories within the [Species 2000 organisation](https://github.com/Sp2000) which are responsible for specific tasks.
Please check the individual repositories and their issue management for more details:

 - [general](https://github.com/CatalogueOfLife/general): the overarching project repository that contains:
    - issues reaching out to the [CoL Global Team](https://github.com/CatalogueOfLife/general/issues?q=is%3Aissue+is%3Aopen+label%3A%22Global+Team%22)
 - [coldp](https://github.com/CatalogueOfLife/coldp): Catalogue of Life Data Package specification of a richer & recommended exchange format for the Clearinghouse and Catalogue of Life, replacing DwC-A and the CoL submission format (ACEF).
 - [backend](https://github.com/CatalogueOfLife/backend): the Java backend with various Maven modules that primarily provide standalone JSON webservices as shaded jars using the Dropwizard framework
    - [API documentation](https://sp2000.github.io/colplus/api/api.html) using RAML
 - [checklistbank](https://github.com/CatalogueOfLife/checklistbank): repository containing all frontend code written in [React](https://reactjs.org/) on top of the API services.
 - [portal](https://github.com/CatalogueOfLife/portal): the public facing website for Catalogue of Life built using [Jekyll](https://jekyllrb.com/)
 - [deploy](https://github.com/CatalogueOfLife/deploy): private repository with credentials and deploy scripts for GBIF
 - [data](https://github.com/CatalogueOfLife/data): a data repository that is used for tracking issues and working with data in the COL Checklist.
