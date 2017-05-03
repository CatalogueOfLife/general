# Model entities for CoL plus 

The main entities represented in the Catalogue of Life and a clarification of their semantics.
The entities are used to establish an API primarily and the actual storage model might differ.

## Exchange format
This model will be mapped to a modified exchange format based on the previous i4Life "Checklist exhcange format", see [exchange format specs](EXCHANGE-FORMAT.md).


# Nomenclature
## Name
A scientific name is a surprisingly ambigous term. 
CoL deals and categorizes the following types of names:

 - Linnaean names, i.e. mono-, bi- or trinomials
 - Named hybrids and nothotaxa
 - Virus names

A name includes it's authorship. Two homonyms with different authors therefore represent two different name entities.

The same name can usually be represented by many different strings which we refer to as lexical variations. 
For each name a standard representation, the canonical form, exists.

Lexical variations exist for various reasons. 
Author spelling, transliterations, epither gender, additional infrageneric or infraspecific indications, cited species authors in infraspecific names are common reasons.
Listed here are 7 distinct names with some of their string representations:

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

Recombinations of the same epithet are treated as distinct names. 

### Homotypic groups
Names based on the same type can be clustered together as a homotypic group of names.
The first, originally published name, the protonym or basionym in botany, is used to represent such a group 
as it is a clean proxy to the type specimen other than for replacement names.
Protonyms are not necessarily Code-compliant original descriptions, but usually they are. 

A homotypic group not only includes all subsequent recombinations, but also any replacement names if existing.

The above names can be clustered into four sets of homotypic synonym groups, shown with canonical authorship:

 1. Aus bus Linnaeus 1758
    - Aus ba Linnaeus 1758 [orthographic variant]
    - Xus bus (Linnaeus 1758) Smith 1850 [alternate combination]
 
 2. Xus cus Smith 1850
    - Xus bus subsp. cus Smith 1850 [alternate rank]
 
 3. Xus cus Jones 1900 (heterotypic homonym of Xus cus Smith 1850)
    - Xus dus Pyle 2000 [replacement name for Xus cus Jones]

 4. Foo bar var. lion Smith 1850

## Author
A person that is an author of scientific names. 
The actual name string of the author can vary similar to scientific names due to spelling, transliteration, aliases, marriage or other name changes over time.
e.g., "Carlolus Linnaus", "Carl Linnaus", "Carl von Linné", etc.  

 - firstName: the preffered full first name(s) separated by whitespace
 - lastName: full last name incl prefixes like "van" if existing
 - aliases: list of alternative representations (LastName, FirstNames)
 - yearOfBirth
 - yearOfDeath
 - areaOfInterest: list of groups of higher taxa
 - biography: short description of the persons (work) life
 - collections: collection codes known to host type specimens

## Literature
Modelling and maintaining literature data can get pretty complex if fully atomized. 
Use cases are needed to justify a fully atomized data model over a very simplistic one like the Catalogue of Life is using right now:

  - authors: Complete author string
  - year: Year(s) of publication
  - title: Title of the publication
  - text: Additional information pertaining to the publication
  - uri: Link other than DOI to online version
  - doi: DOI of publication

### Normalized alternative
A more normalized structure would separate out journals and book series which reduces the work of actively managing these entities in a nomeclator a lot.
It would also allow better linking to BHL for articles, as matching by journal, volume and page is least ambiguous.

 **TBD**
  
> RICH:
> 2) Reference
> Conceptual entity representing any form of information assertion made by one or more Agent instances, asserted at a particular time.  
> The "information assertion" is wide open, and can include any form of static documentation (e.g., publication).  
> Reference instances are linked to AgentName instances (rather than directly to Agent instances), 
> because it's useful to track the orthography of an author name as it actually appears in the documented reference (e.g., byline of a publication).  
> Agent "authors" associated with a single Reference have a specific "role" in the association (e.g., "author", "editor", "artist", etc.), 
> and is represented as an ordered list. Business Rules ensure that there can only be one Agent-Reference link as an author (with Agent inherited through 
> the link between the Reference instance and the AgentName instance).  In other words, the same Reference cannot have both "Carolus Linnaeus" and "Carl von Linné" 
> as separate linked AgentName instances, if both of those AgentNames refer to the same Agent instance).  An useful analogy is that "Agent is to Reference" as "Location is to Event".  
> Whereas an Event is a "date-stamped location" (with various metadata), a Reference is a "date-stamped Agent" (with various metadata).

## Type



# Taxonomy
Entities primarily related to taxonomic opinions, not the scientific name itself.
These entities are therefore only treated in the Catalogue of Life, but not in the nomenclator.

## Taxon
mint identifiers and detail scope

CREATE TABLE `taxon_detail` (
  `taxon_id` int(10) unsigned NOT NULL,
  `author_string_id` int(10) unsigned DEFAULT NULL COMMENT 'Link to author citation of the taxon',
  `scientific_name_status_id` tinyint(2) unsigned NOT NULL,
  `scrutiny_id` int(10) unsigned DEFAULT NULL,
  `additional_data` text COMMENT 'Optional free text field describing the taxon',
  `taxon_guid` varchar(255) DEFAULT NULL,
  `name_guid` varchar(255) DEFAULT NULL,
  `has_preholocene` smallint(1) DEFAULT NULL DEFAULT '0',
  `has_modern` smallint(1) DEFAULT NULL DEFAULT '1',
  `is_extinct` smallint(1) DEFAULT NULL DEFAULT '0',
  PRIMARY KEY (`taxon_id`),
  KEY `author_string_id` (`author_string_id`),
  KEY `taxononomic_status_id` (`scientific_name_status_id`),
  KEY `scrutiny_id` (`scrutiny_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COMMENT='Details pertaining to species and infraspecies';


### Taxon Name Usage (TNU)
> RICH:
> Unique combination of ReferenceID + ProtonymID (recursive to TNU representing Protonym; see below) + TaxonRank.  
> The addition of TaxonRank for uniqueness is important for Autonyms/Nominotypical names, such as Aus (Aus) bus subs. bus, 
> where the same Protonyms for the genus-group name "Aus" and species-group name "bus" are each used twice within the same TNU, 
> one for each of two different ranks (Genus/Subgenus; Species/subspecies).  
> Core properties of each TNU include NameString (terminal epithet only), rank (controlled vocabulary), 
> recursive link to TNU representing the "valid" treatment of the name within the usage (self-referential for names treated as valid, 
> FK to the senior synonym as treated within the same Reference for heterortypic synonyms), recursive link to TNU representing the direct "parent" treatment 
> (e.g., a TNU for a species links to a TNU for a genus treatment within the same Reference to yield the binomen Genus + Species). 
> TNUs are extremely powerful anchor-points to taxon names (via Protonyms and arrays of protonyms) and taxon concepts (specific taxon definitions, 
> which allow disambiguation of different concepts that have the same name).  
> It's simultaneously a convenient way to capture all spelling variants and build an index to literature treatments of names e.g., in BHL. 
> They also serve as the perfect backbone for cross-linking nomenclatural acts as governed by the various Codes of Nomenclature (all of which happen within the context of TNUs).	


### Synonymy
Assuming that the homonyms Xus cus Smith 1850 and Xus cus Jones 1900 are for completely different species, 
and that we now regard Xus cus Smith 1850 as a subjective junior heterotypic synonym of Aus bus Linnaeus 1758, 
which we consider to belong to the genus “Aus”, we can collapse these down to two Accepted names (homotypic and heterotypic synonymy included indented under each):

 1. Xus bus (Linnaeus 1758) Smith 1850
    - Aus bus Linnaeus 1758
    - Aus buus Linnaeus 1758
    - Xus bus (Linnaeus 1758) Smith 1850
    - Xus cus Smith 1850
    - Xus bus subsp. cus Smith 1850
 2. Xus dus Pyle 2000
    - Xus cus Jones 1900

## Vernacular Name
Vernacular or common names that are used for a given taxon. Any taxon can have multiple vernacular names, even for the same language.
Properties of a common name are:

 - name: the vernacular name itself
 - language: the language of the name
 - country: the country the name is used in
 - source: a source reference where the name was listed

## Distribution
An species range distribution often based on experts knowledge. 
A single distribution record for a species should cover only one known area.

 - area: the named area, preferrably an area code from a given standard
 - standard: the standard the given code is based on. If missing the area is taken as free text. One out of: tdwg, iho, eez, iso
 - status: alien, alien-domesticated, domesticated, native, native-domesticated or uncertain
 - source: a source reference where the name was listed

## Environment information:
A very coarse environment/habitat classification of species is maintained using a single property from a controlled vocabulary:

 - lifezone: brackish, freshwater, marine or terrestrial


## Recent and fossil taxa
For palaeo taxa it is desirable to indicate whether an organism is known from fossils and the geological time range of these.

For recent taxa ideally we also want to know whether is is considered currently extinct, but known to have existed during the holocene.
As species go extinct rather quickly these days and true extinction of species is difficult to assess, we propose to leave this job to other initiatives like the IUCN 
and only track whether an organism is known from the holocene. The following properties are proposed:

 - livingPeriodStart: Earliest geological time in millions of years an organism is known to have lived on Earth
 - livingPeriodEnd: Latest geological time in millions of years an organism is known to have lived on Earth
 - preholocene: true if organism is known from before the Holocene, i.e. before ~11.700 years before now
 - holocene: true if organism is known from the Holocene, i.e. after the ice age from ~11.700 years to now

# Other

## Contributor
if possible tie to Authors

## Source metadata (GSD)
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL COMMENT 'Full name of the source database',
  `abbreviated_name` varchar(50) DEFAULT NULL COMMENT 'Abbreviated name of the source database',
  `group_name_in_english` varchar(255) DEFAULT NULL COMMENT 'Name in English of the group(s) treated in the database',
  `authors_and_editors` varchar(255) DEFAULT NULL COMMENT 'Optional author(s) and editor(s) of the source database',
  `organisation` varchar(500) DEFAULT NULL COMMENT 'Optional organisation which has compiled or is owning the source database',
  `contact_person` varchar(255) DEFAULT NULL COMMENT 'Optional contact person of the source database',
  `version` varchar(25) DEFAULT NULL COMMENT 'Optional version number of the source database',
  `release_date` date DEFAULT NULL COMMENT 'Optional most recent release date of the source database',
  `abstract` text COMMENT 'Optional free text field describing the source database',
  `taxonomic_coverage` text,
  `is_new` tinyint(1) NOT NULL,
  `coverage` varchar(255) DEFAULT NULL,
  `completeness` varchar(10) DEFAULT NULL,
  `confidence` tinyint(1) DEFAULT NULL,

CREATE TABLE `taxonomic_coverage` (
  `source_database_id` int(10) NOT NULL,
  `taxon_id` int(10) NOT NULL,
  `sector` tinyint(2) NOT NULL,
  `point_of_attachment` tinyint(1) NOT NULL DEFAULT '0',
  KEY `source_database_id` (`source_database_id`),
  KEY `sector` (`sector`),
  KEY `taxon_id` (`taxon_id`)
) ENGINE=MyISAM DEFAULT CHARSET=utf8;


