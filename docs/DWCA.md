# Clearinghouse DwC-A
A quick documentation of the recommended Darwin Core Archive format to exchange nomenclatural and taxonomic information.
It is based on a regular DwC checklist archive with a "Taxon" core which we also use to exchange plain names.

Terms are marked by [N] or [T] if recommended for nomenclatural or taxonomic datasets.
Exclamation marks ! indicate required terms.

# Taxon Core
The taxon core contains taxa and names. 
In taxonomic lists synonyms and accepted names are both included and require to have an identifier on their own.

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
