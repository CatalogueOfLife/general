	 
# Nomenclature & Names Index
The Clearinghouse separates taxonomy from names and nomenclature.
Names are not restricted to available names, but the names index should
also cover for example naked names that might appear in literature or on specimen labels somewhere. CoL+ has decided to base its model on the work done by TDWG with [Darwin Core](https://github.com/tdwg/dwc) and the Taxon Concept Schema ([TCS](https://github.com/tdwg/tcs)) which is in use by many nomenclators already, e.g. IPNI. You can also explore the Postgres [database schema](design/dbschema.png).

The driving use cases dealing with nomenclature are:

 - is a given string a name at all?
 - what is the correct spelling?
 - is a name "available" and can it be used for a taxon? 
 - what are the reasons for being unavailable?
 - where was the name originally published? Where do I find the publication on the internet?
 - what is the homotypic synonymy of a name?
 - which nomenclatural code does a name follow?
 
## Contents
 - [Name types](#name-types)
 - [Name class](#name-class)
   - [Authorship class](#authorship-class)
   - [Reconstructing scientificName & authorship](#reconstructing-scientificname--authorship)
 - [Homotypic synonymy](#homotypic-synonymy)
 - [Rank](#rank)
 - [Name relations](#name-relations)
 - [Name status](#name-status)
 - [Names Index and Name equality](#names-index-and-name-equality)
 - [Examples](#examples)


## Name types
When dealing with real data one has to work with a wide variety of name strings. These *scientific names*, excluding vernacular names, often do not follow simple latin binomials which can be represented in a parsed form. They can also contain nomenclatural (e.g. *nom.illeg.*), taxonomic (e.g. *s.str.*) or informal (e.g. *cf.*) notes. To work with a wide variety of names we coarsely classify them based on their syntactic nature:

 - 	```SCIENTIFIC```: A scientific latin name that might contain authorship but is not any of the other name types below. Notho taxa are considered scientific names.
 -  ```VIRUS```: A virus name which is never parsed into epithets. The entire name is kept in the scientificName property
 - 	```HYBRID_FORMULA```: A hybrid *formula*, not a named hybrid which falls under scientific.
 - 	```INFORMAL```: A scientific name with some informal addition like "cf." or indetermined like Abies spec.
 - 	```OTU```: Operational Taxonomic Unit such as Barcode Index Numbers
 - 	```PLACEHOLDER```: A placeholder name like "incertae sedis" or "unknown genus" which you often find in databases.
 - 	```NO_NAME```: A text which surely is not a scientific name at all.

Only scientific and informal names are represented as parsed name instances. All others are treated just as strings in the scientificName property.

## Name class
The name class holds parsed names and is free of taxonomic judgements. A name comes with the following attributes (```+``` required for all names, ```#``` only on parsed names):

 - ```id```**+** Primary identifier for the name as given by the dataset. Only guaranteed to be unique within a dataset and can follow any kind of schema.
 - ```datasetKey```**+**  Key to dataset instance. Defines context of the name id.
 - ```verbatimKey``` key to the verbatim record as it came from the source
 - ```homotypicNameId``` **+** Groups all homotypic names by referring to a single representative name of that group.
    This representative name is not guaranteed to be semantically meaningful, but if known it will often be the basionym.
 - ```indexNameId    ``` Id from the names index grouping all distinct scientific names
 - ```scientificName``` **+** Entire canonical name string with a rank marker for infragenerics and infraspecfics, but
    excluding the authorship. For uninomials, e.g. families or names at higher ranks, this is just
    the uninomial. See ScientificName section below for more details.
 - ```rank``` **+** Rank of the name from an extensive, ordered enumeration
 - ```type``` **+** The kind of name classified in broad catagories based on their syntactical structure. Enumeration.
 - ```code``` the nomenclatural code that governs the name. Enumeration.
 - ```uninomial```**#** Represents the monomial for genus, families or names at higher ranks which do not have further epithets.
   Not used for binonimals.
 - ```genus```**#** The genus part of a bi- or trinomial name. Not used for genus names which are represented by the uninomial alone.
 - ```infragenericEpithet```**#** The infrageneric epithet. Used as the terminal epithet for names at infrageneric ranks and
   optionally also for binomials
 - ```specificEpithet```**#** the specific part of a binomial
 - ```infraspecificEpithet```**#** the lowest, infraspecific part of a trinomial. 
 - ```cultivarEpithet```**#** cultivar name
 - ```appendedPhrase```**#** phrases that should be appended to the name, e.g. for bacterial strains
 - ```candidatus```**#** A boolean flag to indicate a bacterial candidate name. Candidatus is a provisional status for incompletely described procaryotes and such names are usually prefixed with the italic term *Candidatus*.
 - ```notho```**#** The part of the named hybrid which is considered a hybrid for notho taxa.
 - ```authorship``` **+** Full authorship string including basionym and combination authors, ex- sanctioning authors and years.
 - ```combinationAuthorship```**#** Authorship instance (see below for class definition) with years of the name, but excluding any basionym authorship. 
   For binomials the combination authors.
 - ```basionymAuthorship```**#** Basionym authorship of the name
 - ```sanctioningAuthor```**#** The sanctioning author for sanctioned fungal names. Fr. or Pers.
 - ```nomStatus``` nomenclatural status of the name, enumeration.
 - ```publishedInId``` Id to the reference the name was originally published in.
 - ```publishedInPage``` The page(s) or other detailed location within the publishedIn reference the name was described.
 - ```origin``` the origin indicates how the name entered the system. Either SOURCE for a record explicitly existing in the sources or other values for names created during import processing for implicitly indicated records, e.g. a flat classification.
 - ```sourceUrl``` link to a webpage showing the name in the original source if available
 - ```fossil``` true if the type specimen of the name is a fossil
 - ```remarks``` Any informal note about the nomenclature of the name, e.g. nom. illeg.
 
Please see our [API docs](http://api.catalogueoflife.org/#model-Name) for more up to date details.

### Authorship class
This class is only used as part of a Name instance and not on its own. It therefore has no keys and is not shared between names.

 - ```year```**#**: The year the combination or basionym was first published, usually the same as the publishedIn reference.
 - ```authors```**#**: List of authors as strings, excluding ex- authors.
 - ```exAuthors``` List of ex- authors.


### Reconstructing scientificName & authorship
The scientificName and authorship properties are the main representation of a name instance and are reconstructed based on the other parsed fields.
For unparsable names such as virus names or hybrid formulas the entire verbatim name string is used. In all other cases the scientificName is assembled and includes just the genus, specific- and infraspecificEpithet, it's standardized rank marker, cultivar and strain names. Authorship, infragenerics, taxon concept references and nomenclatural notes are excluded. Ligatures such as œ are decomposed into their standard multi letter equivalent, in this case oe.

The full authorship string is reconstructed from the parsed authors. 
Author teams are included in their full length with the last author always concatenated by an ampersand.
Years are added at the end of each authorship preceeded by a comma.
Basionym authors are kept in brackets and preceed the combination authorship.

For more details see the [NameFormatter class](https://github.com/gbif/name-parser/blob/master/name-parser-api/src/main/java/org/gbif/nameparser/util/NameFormatter.java#L13) of the GBIF Name Parser.


## Homotypic synonymy
Names based on the same type can be clustered together as a homotypic group of names.
A homotypic group not only includes all subsequent recombinations, but also any [replacement name](https://en.wikipedia.org/wiki/Nomen_novum) or emendation if existing. These names are homotypic synonyms, also called nomenclatural or objective synonyms.

In theory one can establish such a group by linking all names back to the first published name known as the protonym in zoology and basionym in botany (strictly a basionym only exists once a name was newly combined while the protonym definition covers a lone newly described name already. We prefer to use the older and well known term basionym in CoL). But in reality the basionym is often not known while it is known that a collection of names are homotypic. The Clearinghouse datastore therefore picks a random name from the set of homotypic names and uses its key on all such names in the property `homotypicNameId`.

The following names can be clustered into three sets of homotypic groups, shown with canonical authorship:

```
 1. Aus bus Linnaeus 1758
    - Aus ba Linnaeus 1758 [orthographic variant]
    - Xus bus (Linnaeus 1758) Smith 1850 [alternate combination] 
    
 2. Xus cus Smith 1850
    - Xus bus subsp. cus Smith 1850 [alternate rank]
    
 3. Xus cus Jones 1900 (later, heterotypic homonym of Xus cus Smith 1850)
    - Xus dus Pyle 2000 [replacement name for Xus cus Jones]
```

See the homotypic group example below for the JSON representation.


## Rank
There is the need to deal with ranks in a consistent manner. Old ranks not accepted anymore also need to be covered as they appear in synonyms at least.
The [list of known ranks](https://github.com/gbif/name-parser/blob/master/name-parser-api/src/main/java/org/gbif/nameparser/api/Rank.java) is intended to be interoperable between name providers for bacteria, viruses, fungi, plants, and animals. 
It is not assumed that in each taxonomic group all ranks have to be used. 
The enumeration attempts to strike a balance between listing all possible rank terms, and remaining comprehensible. 
Not included in the list are the botanical "notho-" ranks, which are used to designate hybrids (nothospecies, nothogenus) as this information is handled by the Name.notho property already. If needed the list can obviously be extended to cover missing ranks.

Sources consulted:

 - [GBIF](https://github.com/gbif/gbif-api/blob/master/src/main/java/org/gbif/api/vocabulary/Rank.java)
 - [TCS](https://github.com/tdwg/tcs/blob/master/TCS101/v101.xsd#L860) 
 - [TaxCat2](http://mansfeld.ipk-gatersleben.de/apex/f?p=185:78:::NO::P77_TAXCAT,P77_DB_CHECKBOX1,77_TAXCAT2RAD,77_SHOWRAD:%25,,0,s_All) 
 - [EDIT CDM](https://dev.e-taxonomy.eu/gitweb/cdmlib.git/blob/HEAD:/cdmlib-model/src/main/java/eu/etaxonomy/cdm/model/name/Rank.java)



## Name relations
CoL follows the TCS name relations and distinguishes between the following type of directed relations. 
A relation optionally comes with a reference if the underlying nomenclatural act is different from the publishedIn reference for the name.

We provide a [Darwin Core Archive extension definition](https://github.com/CatalogueOfLife/general/blob/master/dwca/name_relation.xml) supporting publishing name relations as part of a DwC Checklist Archive with tools like the [GBIF IPT](https://www.gbif.org/ipt).


 - ```SPELLING_CORRECTION```: The current name is a spelling correction, called emendation in zoology, of the related name. Intentional changes in the original spelling of an available name, whether justified or unjustified. The binomial authority remains unchanged. Valid emendations include changes made to correct:
     - a) typographical errors in the original work describing the species,
     - b) errors in transliteration from non-Latin languages,
     - c) names that included diacritics, hyphens
     - d) endings of species to match the gender of the generic name, particularly when the combination has been changed

- ```BASIONYM```: The current name has a basionym and therefore is either a recombination (combinatio nova, comb. nov.) of the name pointed to (and the name pointed to is not, itself, a recombination), or a change in rank (status novus, stat. nov.).

- ```BASED_ON ```: The current name is the validation of a name that was not fully published before. Covers the use of ex in botanical author strings.
   
   ICBN Art. 46.4: e.g. if this name object represents G. tomentosum Nutt. ex Seem.
   then the related name should be G. tomentosum Nutt.

- ```REPLACEMENT_NAME ```: Current name is replacement for the related name.
   Also called 'Nomen Novum' or 'avowed substitute'
   ICBN: Article 7.3
   ICZN: Article 60.3.

- ```CONSERVED ```: The current name or spelling is conserved / protected against the related name or the related name is suppressed / rejected in favor of the current name. A spelling which has been conserved relates two homotypic names, otherwise
   the related names should be based on different types. Based on an individual publication but more often due to actions of the ICZN or ICBN exercising its Plenary Powers.
   
   ICN: Conservation is covered under Article 14 and Appendix II and Appendix III (this name is nomina conservanda).
   
   ICZN: Reversal of precedence under Article 23.9 (this name is nomen protectum and the target name is nomen oblitum) or suppression via plenary power Article 81.


- ```LATER_HOMONYM ```: Current name has same spelling as related name but was published later and has priority over it (unless conserved or sanctioned) and is based on a different type. Called a junior homonym in zoology. This includes botanical parahomonyms which differ slightly in spelling but are similar enough that they are likely to be confused (Art 53.3). The zoological code has a set of spelling variations (article 58) that are considered to be identical.
 
   When acts of conservation or suppression have occurred then the terms “Conserved Later Homonym”   and “Rejected Earlier Homonym” should be used.
   
   ICBN: Article 53
 
   ICZN: Chapter 12, Article 52.

- ```SUPERFLUOUS```: Current name was superfluous at its time of publication,
   i. e. it was based on the same type as the related, previously published name (ICN article 52). The current, superfluous name is available but illegitimate. Includes the special case of isonyms which are identical, homotypic names.

- ```HOMOTYPIC```: A relation indicating two homotypic names, i.e. objective or nomenclatural synonymy, but not further specifying why.

- ```TYPE```: Current name is the type name (species or genus) of the related higher ranked name.
   

## Name status
The name status can in many cases be derived from a names relation if existing.
The related name is sometimes not known and a name that has not been properly published (e.g. a naked name) need to be flagged directly as such. We also use the status to indicate known chresonyms or provisional manuscript names. 

The vocablary is kept minimal to basically answer two fundamental questions:

1) Has the name nomenclatural standing and should be considered in nomenclatural questions?
2) Can the name be used for a taxon?

The name class also offers an unrestricted remarks field which should be used to keep further nomenclatural notes to inform about the exact status of a name and which rules have been considered. It is meant to be understood by humans.

We use [BioCode terminology](https://archive.bgbm.org/IAPT/biocode/biocode.html#Table) as much as possible
and avoid the term valid entirely as it is very overloaded and used for different things in both codes.


 - ```ESTABLISHED```: *nomen validum*. A properly established name according to the rules of nomenclature.
    - Botany: validly published name
    - Zoology: available name

 - ```NOT_ESTABLISHED```: *nomen invalidum*. A name that was not validly published according to the rules of the code, or a name that was not accepted by the author in the original publication, for example,
   if the name was suggested as a synonym of an accepted name.
   In zoology referred to as an unavailable name.
   There are many reasons for a name to be unavailable.
   The exact reason should be indicated in the Names remarks field, e.g.:
   
    - published as synonym (pro syn.) ICN Art 36
    - nomen nudum (nom. nud.) published without an adequate description
    - not latin (ICN Art 32)
    - provisional/manuscript names
    - suppressed publication
    - tautonym (ICN) e.g. Opuntia opuntia H.Karst.
   

 - ```ACCEPTABLE```: *nomen legitimum*. Botany: Names that are validly published and legitimate
   Zoology: Available name and *potentially* valid, i.e. not otherwise invalid
   for any other objective reason, such as being a junior homonym.

 - ```UNACCEPTABLE```: *nomen illegitimum*. An available name with nomenclatural standing, but one that objectively contravenes some of the rules laid down by nomenclatural codes and thus cannot be used as a name for an accepted taxon. 
   
   There can be varioous reasons why a name is illegitimate, e.g.:
     - Botany: superfluous at its time of publication (article 52), i.e., the taxon (as represented by the type) already has a name
     - Botany: the name has already been applied to another plant (a homonym) articles 53 and 54
     - Zoology: junior homonym or objective synonyms
     - Zoology: nomen oblitum
     - Zoology: suppressed name

 - ```CONSERVED```: *nomen conservandum*. A scientific name that enjoys special nomenclatural protection, i.e. a name conserved, protected or sanctioned in respective code.
   Names classified as available and valid by action of the ICZN or ICBN exercising its Plenary Powers .
   Includes rulings to conserve junior/later synonyms in place of rejected forgotten names (nomen oblitum)
   via "Reversal of Precedence" in accordance with ICZN Article 23.9.1.
   Such names are entered on the Official Lists.

   Conservation of botanical names is only possible at the rank of family, genus or species.
   
   Conserved names are a more generalized definition than the one for nomen protectum, which is specifically a conserved name that is either a junior synonym or homonym that is in use
   because the senior synonym or homonym has been made an available, but invalid nomen oblitum ("forgotten name").

 - ```REJECTED```: *nomen rejiciendum* Rejected / suppressed name. Inverse of conserved. Outright rejection is possible for a name at any rank.

 - ```DOUBTFUL```: *nomen dubium* or *nomen ambiguum*. Doubtful or dubious names, names which are not certainly applicable to any known taxon or for which the evidence is insufficient to permit recognition of the taxon to which they belong. The confusion being derived from an incomplete or confusing description. Includes nomen ambiguum and nomen inquirendum, a species of doubtful identity requiring further investigation.

 - ```MANUSCRIPT```: An unpublished name that was given a temporary placeholder name to work with. Sometimes these names do not get properly published for decades and can be cited in other works. Often abbreviated as ined. (ineditus) or ms. (manuscript) and sometimes called chironym/cheironym

 - ```CHRESONYM```: A name usage erroneously cited without a sec/sensu indication so it appears to be a published homonym with a different authority.


## Names Index and Name equality
A scientific name should have been published in literature according to the codes. At least it should have been attempted (failed to comply with the codes) or it is currently being prepared for publishing (manuscript name). Without knowing the exact list of all published names it is near impossible to asses whether a given name string is an actually published name. Many *bad* names can already be filtered out based on the name type they are classified under (see above). But many others appear to be correct according to their syntactic structure, e.g. subsequently introduced misspellings or chresonyms.

The names index is meant to keep track of all distinct names that syntactically appear to be published scientific names. It offers a matching API that can be used to lookup up names and an editorial API to manage its content.

Every dataset (e.g. GSD) imported into the Clearinghouse is isolated from others and does not share any name instances with other datasets. Thus the *same* name, potentially in a different exact string, may exist various times in the Clearinghouse, but they should all be linked to the same name in the Names Index.

A name is considered the same when it's canonical form (rank, genus, specific & infraspecific epithet), the authorship and the place of original publication are the *same*. Authorship and publication equality are rather difficult to determine, as the exact spelling for the same authorteam or publication often varies considerably. 

Lexical variations exist for various reasons. Author spelling, transliterations, epithet gender, additional infrageneric or infraspecific indications, cited species authors in infraspecific names are common reasons. Listed here are 7 distinct names with some of their string representations:

```
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
```

New names (**sp./gen. nov.**), new recombinations of the same epithet (**comb. nov.**), a name at a new rank (**stat. nov.**) 
or replacement names (**nom. nov.**) are all treated as distinct names. 

To avoid all spelling mistakes found on labels and in databases to enter the names index a very limited *fuzzy* matching is also allowed on the canonical name itself. This is largely restricted to gender stemming of the terminal epithet and very frequently swapped characters such as `i/y` or the addition/removal of an `h` after `t` or `g`.



# Examples
TDWG's [Taxon Concept Schema guide](https://github.com/tdwg/tcs/blob/master/TCS101/UserGuidev_1.3.pdf) provides great examples which we exploited here as CoL+ JSON to illustrate various use cases. The guide is well worth reading on its own.

After the first few examples we will start omitting parsed fields of the name instance to concentrate on the important parts. If it says parsed=true at the bottom of an object please imagine the missing fields would be present.


## Species Pinus abies var. leioclada Steven ex Endl.
```json5
{
  "id": 1234,
  "datasetKey": 1045,
  "verbatimKey": 2190,
  "homotypicNameId": 1234,
  "scientificName": "Pinus abies var. leioclada",
  "authorship": "Steven ex Endl.",
  "rank": "variety",
  "genus": "Pinus",
  "specificEpithet": "abies",
  "infraspecificEpithet": "leioclada",
  "combinationAuthorship": {
    "authors": [
      "Endl."
    ],
    "exAuthors": [
      "Steven"
    ]
  },
  "code": "botanical",
  "origin": "source",
  "type": "scientific",
  "parsed": true
}
```

## Genus Abies
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Abies",
  "authorship": "Mill.",
  "rank": "genus",
  "uninomial": "Abies",
  "combinationAuthorship": {
    "authors": [
      "Mill."
    ]
  },
  "code": "botanical",
  "origin": "source",
  "type": "scientific",
  "publishedInId": 6,
  "publishedInPage": "347",
  "parsed": true
}
```

## Abies Section Grandes
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Abies sect. Grandes",
  "authorship": "A.E.Murray",
  "rank": "section",
  "genus": "Abies",
  "infragenericEpithet": "Grandes",
  "combinationAuthorship": {
    "authors": [
      "A.E.Murray"
    ]
  },
  "code": "botanical",
  "origin": "source",
  "type": "scientific",
  "publishedInId": 6,
  "publishedInPage": "4",
  "parsed": true
}
```

## Subgenus Afrostrandius
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Onthophagus (Afrostrandius)",
  "authorship": "Moretto, 2009",
  "rank": "subgenus",
  "genus": "Onthophagus",
  "infragenericEpithet": "Afrostrandius",
  "combinationAuthorship": {
    "year": "2009",
    "authors": [
      "Moretto"
    ]
  },
  "code": "zoological",
  "origin": "source",
  "type": "scientific",
  "publishedInId": 6,
  "parsed": true
}
```

## Virus species Garlic virus B
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Garlic virus B",
  "rank": "species",
  "code": "virus",
  "type": "virus",
  "parsed": false
}
```

## Named hybrid 
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Carex ×subviridula",
  "authorship": "(Kükenthal) Fernald",
  "rank": "species",
  "notho": "specific",
  "genus": "Carex",
  "specificEpithet": "subviridula",
  "basionymAuthorship": {
    "authors": [
      "Kükenthal"
    ]
  },
  "combinationAuthorship": {
    "authors": [
      "Fernald"
    ]
  },
  "code": "botanical",
  "type": "scientific",
  "parsed": true
}
```


## Hybrid Forumula
```json5
{
  "id": 1234,
  "homotypicNameId": 1234,
  "scientificName": "Asplenium germanicum x trichomanes C.Chr.",
  "rank": "species",
  "code": "botanical",
  "type": "hybrid formula",
  "parsed": false
}
```

## Cultivar Rhododendron x obtusum 'Amoenum'
```json5
{
  "id": 1234,
  "scientificName": "Rhododendron x obtusum 'Amoenum'",
  "rank": "species",
  "notho": "specific",
  "genus": "Rhododendron",
  "specificEpithet": "obtusum",
  "cultivarEpithet": "Amoenum",
  "code": "cultivars",
  "type": "scientific",
  "parsed": true
}
```
## Placeholder Asteraceae Incertae sedis
```json5
{
  "id": 1234,
  "scientificName": "Asteraceae Incertae sedis",
  "rank": "unranked",
  "type": "placeholder",
  "parsed": false
}
```

## New combination Fallopia dumetorum
Arethusa divaricata gets placed into a new genus. 
The JSON shows both names and their relation object.

```json5
{
  "id": 325,
  "homotypicNameId": 325,
  "scientificName": "Polygonum dumetorum",
  "authorship": "L.",
  "rank": "species",
  "genus": "Polygonum",
  "specificEpithet": "dumetorum",
  "combinationAuthorship": {
    "authors": [
      "L."
    ]
  },
  "code": "botanical",
  "type": "scientific",
  "parsed": true
},
{
  "id": 326,
  "homotypicNameId": 325,
  "scientificName": "Fallopia dumetorum",
  "authorship": "(L.) J.Holub",
  "rank": "species",
  "genus": "Fallopia",
  "specificEpithet": "dumetorum",
  "basionymAuthorship": {
    "authors": [
      "L."
    ]
  },
  "combinationAuthorship": {
    "authors": [
      "J.Holub"
    ]
  },
  "code": "botanical",
  "type": "scientific",
  "parsed": true
},
{
  "type": "basionym",
  "nameKey": 326,
  "relatedNameKey": 325
}
```


## Correction Gossypium tomentosum
Correction of an invalid/unavailable name in a subsequent publication.

An example from the ICBN Art. 15 - Seemann (1865) published Gossypium tomentosum "Nutt. mss.", followed by a validating description not ascribed to Nuttall; the name may be cited as G. tomentosum Nutt. ex Seem. or G. tomentosum Seem.

Correction homonyms are represented as two separate names. The validating name should have a `basedOn` relation to the incorrectly published name. The validating name should not have a `laterHomonym` relation to the incorrectly published name.

```json5
{
  "id": 124,
  "homotypicNameId": 124,
  "scientificName": "Gossypium tomentosum",
  "authorship": "Nutt.",
  "rank": "species",
  "genus": "Gossypium",
  "specificEpithet": "tomentosum",
  "combinationAuthorship": {
    "authors": [
      "Nutt."
    ]
  },
  "code": "botanical",
  "type": "scientific",
  "status": "manuscript",
  "parsed": true
},
{
  "id": 123,
  "homotypicNameId": 124,
  "scientificName": "Gossypium tomentosum",
  "authorship": "Nutt. ex Seem.",
  "rank": "species",
  "genus": "Gossypium",
  "specificEpithet": "tomentosum",
  "combinationAuthorship": {
    "authors": [
      "Seem."
    ],
    "exAuthors": [
      "Nutt."
    ]
  },
  "code": "botanical",
  "type": "scientific",
  "publishedInId": 166,
  "parsed": true
},

{
  "type": "basedOn",
  "nameKey": 123,
  "relatedNameKey": 124
}
```


## Emendation Persicaria segeta
Sometimes names are misspelled. By misspelling here we mean orthographic and typographic variants of all forms including having the wrong gender for an epithet. This can happen for a number of reasons. The original author of a name can spell it incorrectly and the name can then be corrected under the code (e.g. see ICN Article 60 for examples.), authors of revision concepts can make mistakes in interpreting the code or typographical errors can be made. A correctly spelled Name can be related to multiple incorrectly spelled Names.

```json5
{
  "id": 225,
  "scientificName": "Persicaria segetum",
  "authorship": "(Kunth) Small (1903)",
  "parsed": true
},
{
  "id": 226,
  "scientificName": "Persicaria segeta",
  "authorship": "(Kunth) Small (1903)",
  "parsed": true
},
{
  "type": "correction",
  "nameKey": 226,
  "relatedNameKey": 225,
  "note": "Correction of epithet gender, rule 23.5"
}
```


## Homonym Pedicularis inconspicua
An example of 3 identical names from botany, one superfluous *publication homonym* as TCS calls it and a real homonym in chronological order and all published in the same year:

 - Pedicularis inconspicua P.C.Tsoong, ActaPhytotax. Sin. 3: 292 & 323, Jan. 1955 [IPNI](http://beta.ipni.org/n/807180-1)
 - Pedicularis inconspicua Vved., Fl. URSS 22: 811, 18 Jun. 1955. [IPNI](http://beta.ipni.org/n/807178-1)
 - Pedicularis inconspicua P.C. Tsoong, Bull. Brit. Mus. (Nat. Hist.) 2:17, Nov. 1955. [IPNI](http://beta.ipni.org/n/807179-1)

Names 1 and 3 relate to the same taxon from Bhutan and represent double publication of the same name in Chinese and Western journals. This is one of many such isonyms published in the same two papers by Tsoong; all the names in Acta Phytotax. Sin. have priority over their publication in the British Museum Bulletin. Between the two papers published by Tsoong, the Russian botanist Vvedensky published a real homonym P. inconspicua Vved. for a totally different species from Uzbekistan.

```json5
{
  "id": 123,
  "homotypicNameId": 123,
  "scientificName": "Pedicularis inconspicua",
  "authorship": "P.C. Tsoong",
  "publishedInId": 26,
  "parsed": true
},
{
  "id": 124,
  "homotypicNameId": 124,
  "scientificName": "Pedicularis inconspicua",
  "authorship": "Vved.",
  "publishedInId": 27,
  "parsed": true
},
{
  "id": 125,
  "homotypicNameId": 123,
  "scientificName": "Pedicularis inconspicua",
  "authorship": "P.C. Tsoong",
  "status": "unacceptable",
  "publishedInId": 28,
  "parsed": true
},


{
  "type": "later homonym",
  "nameKey": 124,
  "relatedNameKey": 123
},
{
  "type": "superfluous",
  "nameKey": 125,
  "relatedNameKey": 123
}
```


## Isonym Trillium pusillum var. texanum
Sometimes a new combination is made more than once thus creating an isonym, i.e. the same name published several times based on the same type (as opposed to a homonym which is based on different types). An example is Trillium texanum Buckley which was recombined as a variety of Trillium pusillum twice:

 - Trillium pusillum var. texanum (Buckley) J.L. Reveal & C.R. Broome in Castanea,46(1): 56 (1981)
 - Trillium pusillum var. texanum (Buckley) C.F.Reed in Phytologia, 50(4): 279, 283 (1982)

The combination made by C.F. Reed is a later isonym and so invalid.

```json5
{
  "id": 223,
  "homotypicNameId": 223,
  "scientificName": "Trillium texanum",
  "authorship": "Buckley",
  "publishedInId": 100,
  "parsed": true
},
{
  "id": 224,
  "homotypicNameId": 223,
  "scientificName": "Trillium pusillum var. texanum",
  "authorship": "(Buckley) J.L.Reveal & C.R.Broome",
  "publishedInId": 101,
  "parsed": true
},
{
  "id": 225,
  "homotypicNameId": 223,
  "scientificName": "Trillium pusillum var. texanum",
  "authorship": "(Buckley) C.F.Reed",
  "status": "unacceptable",
  "publishedInId": 102,
  "parsed": true
},


{
  "type": "superfluous",
  "nameKey": 225,
  "relatedNameKey": 224
},
{
  "type": "basionym",
  "nameKey": 225,
  "relatedNameKey": 223
},
{
  "type": "basionym",
  "nameKey": 224,
  "relatedNameKey": 223
}
```



## Nomen Novum Myrcia lucida
Sometimes authors wish to recognise taxa whose names are homonyms. In such cases both the ICN (Art. 7.3) and the ICZN allow for *nomen novum* or replacement name to be created. The ICZN glossary defines nomen novum as:

> A name established expressly to replace an already established name. A nominal taxon denoted by a new replacement name (nomen novum) has the same name-bearing type as the nominal taxon denoted by the replaced name [Arts. 67.8, 72.7].

A nomen novum should have a `ReplacementName` relation to the illegitimate homonym name it replaces. The illegitimate homonym should have a `LaterHomonym` relation to the legitimate homonym of the name.

```json5
{
  "id": 123,
  "homotypicNameId": 123,
  "scientificName": "Myrcia lucida",
  "authorship": "McVaugh",
  "publishedInId": 1969,
  "parsed": true
},
{
  "id": 124,
  "homotypicNameId": 123,
  "scientificName": "Myrcia laevis",
  "authorship": "O.Berg",
  "publishedInId": 1862,
  "status": "unacceptable",
  "parsed": true
},
{
  "id": 125,
  "homotypicNameId": 125,
  "scientificName": "Myrcia laevis",
  "authorship": "G.Don",
  "status": "unacceptable",
  "publishedInId": 1832,
  "parsed": true
},


{
  "type": "later homonym",
  "nameKey": 124,
  "relatedNameKey": 125
},
{
  "type": "replacement",
  "nameKey": 123,
  "relatedNameKey": 124
}
```
 


## Sanctioned Agaricus personatus
The names of fungi presented in certain publications have been sanctioned by ICN (Art. 15). This means that these names are treated as if conserved against earlier homonyms and competing synonyms. The earlier names are not invalid as would be the case if these works had been set as the starting date for fungal nomenclature but are available for use in different combinations. They are also not unacceptable and can be the basionym for a combination

A sanctioned name may be conserved against more than one other names and so may contain more than one `conservered` relation to the other names.


```json5
{
  "id": 123,
  "homotypicNameId": 123,
  "scientificName": "Agaricus personatus",
  "authorship": "Bolton : Fr",
  "status": "conserved",
  "parsed": true
},
{
  "id": 124,
  "homotypicNameId": 124,
  "scientificName": "Agaricus personatus",
  "authorship": "Valenti",
  "status": "rejected",
  "parsed": true
},
{
  "id": 125,
  "homotypicNameId": 125,
  "scientificName": "Agaricus personatus",
  "authorship": "Lasch",
  "status": "rejected",
  "parsed": true
},
{
  "id": 126,
  "homotypicNameId": 126,
  "scientificName": "Agaricus personatus",
  "authorship": "With.",
  "status": "rejected",
  "parsed": true
},

{
  "type": "conserved",
  "nameKey": 123,
  "relatedNameKey": 124
},
{
  "type": "conserved",
  "nameKey": 123,
  "relatedNameKey": 125
},
{
  "type": "conserved",
  "nameKey": 123,
  "relatedNameKey": 126
}
```


## Homotypic group Aus bus

```json5
{
  "id": 1000,
  "homotypicNameId": 1000,
  "scientificName": "Aus bus",
  "authorship": "Linnaeus 1758",
  "rank": "species"
},{
  "id": 1001,
  "homotypicNameId": 1000,
  "scientificName": "Aus ba",
  "authorship": "Linnaeus 1758",
  "rank": "species"
},{
  "id": 1002,
  "homotypicNameId": 1000,
  "scientificName": "Xus bus",
  "authorship": "(Linnaeus 1758) Smith 1850",
  "rank": "species"
},{
  "id": 1003,
  "homotypicNameId": 1003,
  "scientificName": "Xus cus",
  "authorship": "Smith 1850",
  "rank": "species"
},{
  "id": 1004,
  "homotypicNameId": 1003,
  "scientificName": "Xus bus subsp. cus",
  "authorship": "Smith 1850",
  "rank": "subspecies"
},{
  "id": 1005,
  "homotypicNameId": 1005,
  "scientificName": "Xus bus",
  "authorship": "Jones 1900",
  "rank": "species"
},{
  "id": 1006,
  "homotypicNameId": 1005,
  "scientificName": "Xus dus",
  "authorship": "Pyle 1900",
  "rank": "species"
}
```
