# Model entities for CoL plus 

The main entities represented in the Catalogue of Life and a clarification of their semantics.
The entities are used to establish an API primarily and the actual storage model might differ.

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

#### Properties

 - firstName: the preffered full first name(s) separated by whitespace
 - lastName: full last name incl prefixes like "van" if existing
 - aliases: list of alternative representations (LastName, FirstNames)
 - yearOfBirth
 - yearOfDeath
 - areaOfInterest: list of groups of higher taxa
 - biography: short description of the persons (work) life
 - collections: collection codes known to host type specimens

## Literature
*TBD*

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
## Taxon
mint identifiers and detail scope

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
Included in the system. The issue is relation with nomenclator

## Distribution

## Species information: environment, fossil/extinct, geological age


# Other

## Contributor
if possible tie to Authors

## Source metadata (GSD)



