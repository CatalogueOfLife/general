# Nomenclature / Names Index
The Clearinghouse separates taxonomy from names and nomenclature.
Names are not restricted to available names, but the names index should
also cover for example naked names that might appear in literature or on specimen labels somewhere. CoL+ has decided to base its model on the work done by TDWG with Darwin Core and the Taxon Concept Schema (TCS) which is in use by many nomenclators already, e.g. IPNI. You can also explore the Postgres [database schema](dbschema.png).

The driving use cases dealing with nomenclature are:

 - is a given string a name at all?
 - what is the correct spelling?
 - is a name "available" and can be used for a taxon? 
 - what are the reasons for being unavailable?
 - where was the name originally published? Ideally with a direct link to the publication
 - what is the homotypic synonymy of a name?
 - which nomenclatural code does a name follow?
 
## Name types
When dealing with real data one has to work with a wide variety of name strings. These *scientific names*, excluding vernacular names, often do not follow simple latin binomials which can be represented in a parsed form. They can also contain nomenclatural (e.g. *nom.illeg.*), taxonomic (e.g. *s.str.*) or informal (e.g. *cf.*) notes. To work with a wide variety of names we coarsely classify them based on their syntactic nature:

 - 	```SCIENTIFIC```: A scientific latin name that might contain authorship but is not any of the other name types below. Notho taxa are considered scientific names.
 - 	```HYBRID_FORMULA```: A hybrid *formula*, not a named hybrid which falls under scientific.
 - 	```INFORMAL```: A scientific name with some informal addition like "cf." or indetermined like Abies spec.
 - 	```VIRUS```: A virus name which is never parsed into epithets. The entire name is kept in the scientificName property
 - 	```OTU```: Operational Taxonomic Unit such as Barcode Index Numbers
 - 	```PLACEHOLDER```: A placeholder name like "incertae sedis" or "unknown genus" which you often find in databases.
 - 	```NO_NAME```: A text which surely is not a scientific name at all.

Only scientific and informal names are represented as parsed name instances. All others are treated just as strings in the scientificName property.

## Name class
The name class holds parsed names and is free of taxonomic judgements. A name comes with the following attributes (```+``` required for all names, ```#``` only on parsed names):

 - **key** ```+```: Primary, internal surrogate key of the name as provided by the dataset store.       
 - **id**: identifier for the name as given by the dataset  Only guaranteed to be unique within a dataset and can follow any kind of schema.
 - **datasetKey** ```+```: Key to dataset instance. Defines context of the name id.
 - **verbatimKey**: key to the verbatim record as it came from the source
 - **homotypicNameKey** ```+```: Groups all homotypic names by referring to a single representative name of that group.
    This representative name is not guaranteed to be semantically meaningful, but if known it will often be the basionym.
 - **scientificName** ```+```: Entire canonical name string with a rank marker for infragenerics and infraspecfics, but
    excluding the authorship. For uninomials, e.g. families or names at higher ranks, this is just
    the uninomial.
 - **rank** ```+```: Rank of the name from an extensive, ordered enumeration
 - **type** ```+```: The kind of name classified in broad catagories based on their syntactical structure
 - **code**: the nomenclatural code that governs the name
 - **uninomial** ```#```: Represents the monomial for genus, families or names at higher ranks which do not have further epithets.
   Not used for binonimals.
 - **genus** ```#```: The genus part of a bi- or trinomial name. Not used for genus names which are represented by the uninomial alone.
 - **infragenericEpithet** ```#```: The infrageneric epithet. Used as the terminal epithet for names at infrageneric ranks and
   optionally also for binomials
 - **specificEpithet** ```#```: the specific part of a binomial
 - **infraspecificEpithet** ```#```: the lowest, infraspecific part of a trinomial. 
 - **cultivarEpithet** ```#```: cultivar name
 - **candidatus** ```#```: A boolean flag to indicate a bacterial candidate name. Candidatus is a provisional status for incompletely described procaryotes and such names are usually prefixed with the italic term *Candidatus*.
 - **notho** ```#```: The part of the named hybrid which is considered a hybrid for notho taxa.
 - **combinationAuthorship** ```#```: Authorship instance (see below for class definition) with years of the name, but excluding any basionym authorship. 
   For binomials the combination authors.
 - **basionymAuthorship** ```#```: Basionym authorship of the name
 - **sanctioningAuthor** ```#```: The sanctioning author for sanctioned fungal names. Fr. or Pers.
 - **status**: nomenclatural status of the name
 - **publishedInKey**: key to the reference the name was originally published in.
 - **publishedInPage**: The page(s) or other detailed location within the publishedIn reference the name was described.
 - **sourceUrl**: link to a webpage showing the name in the original source if available
 - **fossil**: true if the type specimen of the name is a fossil
 - **remarks**: Any informal note about the nomenclature of the name
 
Please see our [API docs](https://sp2000.github.io/colplus/api/api.html#model-Name) for more details.

## Name relations
CoL follows the TCS name relations and distinguishes between the following. 
A relation does 'not' come with a reference as the relevant publication is the same as  the publishedIn reference for the name with the exception of the conservation relations. Conserving a botanical or zoological name can only be done by using the official lists of the code, so it is straight forward to look them up.


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

- ```CONSERVED ```: The current name is conserved against the related name or the related name is suppressed in favor of the current name.
   Acts taken by the official committee.
   
   ICBN: Conservation is covered under Article 14 and Appendix II and Appendix III (this name is nomina conservanda).
   
   ICZN: Conservation is covered under Article 23.9 (this name is nomen protectum and the target name is nomen oblitum)

- ```LATER_HOMONYM ```: Current name has same spelling as related name but was published later and has priority over it (unless conserved or sanctioned). Called a junior homonym in zoology.
 
   When acts of conservation or suppression have occurred then the terms “Conserved Later Homonym”   and “Rejected Earlier Homonym” should be used.
   
   ICBN: Article 53
 
   ICZN: Chapter 12, Article 52.




## Name status
The name status can in many cases be derived from a names relation if existing.
The related name is sometimes not known and a name that has not been properly published (e.g. a naked name) need to be flagged directly as such. We also use the status to indicate known chresonyms. Status values which correspond to relation types are indicated with ```*```:

 - ```LEGITIMATE```: *nomen legitimum*. Botany: Names that are validly published and legitimate
   Zoology: Available name and *potentially* valid, i.e. not otherwise invalid
   for any other objective reason, such as being a junior homonym.

 - ```REPLACEMENT*```: *nomen novum*. A replacement name or new substitute name indicates a scientific name
   that is created specifically to replace another preoccupied name,
   but only when this other name can not be used for technical, nomenclatural reasons.
   It automatically inherits the same type and type locality and is commonly applied
   to names proposed to replace junior homonyms.

 - ```VARIANT*```: *nomen orthographia*. An alternative or corrected spelling for the name.
   In botanical nomenclature (Art 61 ICN), an orthographical variant (abbreviated orth. var.) is a spelling variant
   of the same name. For example, Hieronima and Hyeronima are orthographical variants of Hieronyma.
   One of the spellings must be treated as the correct one. In this case, the spelling Hieronyma has been conserved
   and is to be used as the correct spelling.
   An inadvertent use of one of the other spellings has no consequences:
   the name is to be treated as if it were correctly spelled.
   Any subsequent use is to be corrected.
   In zoology, orthographical variants in the formal sense do not exist;
   a misspelling or orthographic error is treated as a lapsus, a form of inadvertent error.
   The first reviser is allowed to choose one variant for mandatory further use, but in other ways,
   these errors generally have no further formal standing.
   Inadvertent misspellings are treated in Art. 32-33 of the ICZN.

 - ```CONSERVED*```: *nomen conservandum* or *nomen protectum*. A scientific name that enjoys special nomenclatural protection, i.e. a name conserved in respective code.
   Names classed as available and valid by action of the ICZN or ICBN exercising its Plenary Powers.
   Includes rulings to conserve junior/later synonyms (nomen protectum) in place of rejected forgotten names (nomen oblitum).
   Such names are entered on the respective Official Lists.

 - ```REJECTED*```: *nomen rejiciendum*. Rejected / suppressed name. Inverse of conserved. Outright rejection is possible for a name at any rank.

 - ```UNAVAILABLE```: *nomen invalidum*. A name that was not validly published according to the rules of the code, or a name that was not accepted by the author in the original publication, for example,
   if the name was suggested as a synonym of an accepted name.
   In zoology referred to as an unavailable name.

 - ```NAKED```: *nomen nudum*. The term is used to indicate a designation which looks exactly like a scientific name of an organism,
   and may have originally been intended to be a scientific name, but fails to be one
   because it has not (or has not yet) been published with an adequate description (or a reference to such a description),
   and thus is a "bare" or "naked" name, one which cannot be accepted as it currently stands.
   Because a nomen nudum fails to qualify as a formal scientific name,
   a later author can publish a real scientific name that is identical in spelling.
   If one and the same author puts a name in print, first as a nomen nudum and later publishes the name
   accompanied by a description that meets the formal requirements,
   then the date of publication of the latter, formally correct, publication becomes the name's date of establishment.

 - ```FORGOTTEN```: *nomen oblitum*. A name which has been overlooked (literally, forgotten), has not been in use after 1899 and is no longer valid.
   The name is turned invalid in favor of the nomen protectum which should be marked as conserved.
   ICZN Article 23.9.1

 - ```ILLEGITIMATE```: *nomen illegitimum*. A nomen illegitimum is a validly published name, but one that contravenes some of the articles laid down by
   the International Botanical Congress. The name could be illegitimate because:
   - article 52: it was superfluous at its time of publication, i.e., the taxon (as represented by the type) already has a name
   - articles 53 and 54: the name has already been applied to another plant (a homonym)
   
   Zoology: Available, but objectively invalid names, e.g. a junior homonym

 - ```SUPERFLUOUS```: *nomen superfluum*. A name that was superfluous at its time of publication, i. e. it was based on the same type as another, previously published name (ICN article 52).

 - ```DOUBTFUL```: *nomen dubium* or *nomen ambiguum*. Doubtful or dubious names, names which are not certainly applicable to any known taxon or for which the evidence is insufficient to permit recognition of the taxon to which they belong. The confusion being derived from an incomplete or confusing description.

 - ```MANUSCRIPT```: An unpublished name that was given a temporary placeholder name to work with. Sometimes these names do not get properly published for decades and can be cited in other works. Often abbreviated as ined. (ineditus) or ms. (manuscript) and sometimes called chironym/cheironym

 - ```CHRESONYM```: A name usage erroneously cited without a sec/sensu indication so it appears to be a published homonym with a different authority.

 - ```UNEVALUATED```: *nomen inquirendum*. A species of doubtful identity requiring further investigation

The unrestricted remarks field is used to keep further nomenclatural notes to inform about the status of a name but which are only understood by humans.



## Names Index and Name equality
A scientific name should have been published in literature according to the codes. At least it should have been attempted (failed to comply with the codes) or it is currently being prepared for publishing (manuscript name). Without knowing the exact list of all published names it is near impossible to asses whether a given name string is an actually published name. Many *bad* names can already be filtered out based on the name type they are classified under (see above). But many others appear to be correct according to their syntactic structure, e.g. subsequently introduced misspellings or chresonyms.

The names index is meant to keep track of all distinct names that syntactically appear to be published scientific names. It offers a matching API that can be used to lookup up names and an editorial API to manage its content.

Every dataset (e.g. GSD) imported into the Clearinghouse is isolated from others and does not share any name instances with other datasets. Thus the *same* name may exist various times in the Clearinghouse, but they should all be linked to the same name in the Names Index.

A name is considered the same when it's canonical form (rank, genus, specific & infraspecific epithet), the authorship and the place of original publication are the *same*. Authorship and publication equality are rather difficult to determine, as the exact spelling for the same authorteam or publication often varies considerably. 

To avoid all spelling mistakes found on labels and in databases to enter the names index a very limited *fuzzy* matching is also allowed on the canonical name itself. This is largely restricted to gender stemming of the terminal epithet and very frequently swapped characters such as `i/y` or the addition/removal of an `h` after `t` or `g`.



# Examples
TDWG's [Taxon Concept Schema guide](http://www.tdwg.org/fileadmin/_migrated/content_uploads/UserGuidev_1.3.pdf) provides great examples illustrating the different relations above. Many examples covered here are taken from the TCS guide and show how they would look in the CoL+ API.

 - Recombinations are considered different names:
	 - 	```Arethusa divaricata L.```
	 - 	```Cleistes divaricata (L.) Ames```
 - True Homonyms:
	 - 	``` ```
	 - 	``` ```
 - Same genus name published under different codes:
	 - 	``` ```
	 - 	``` ```
 - Correction of an invalid/unavailable name in a subsequent publication. 'Correction' homonyms in TCS:
	 - 	``` ```
	 - 	``` ```
 - Correction of an invalid/unavailable name in a subsequent publication by the same author in the same year:
	 - 	``` ```
	 - 	``` ```
 - Published spelling corrections:
	 - 	``` ```
	 - 	``` ```



## genus Abies

__single taxon core__:
By using dwc:namePublishedInID and related terms in the taxon core one can express information about where the name was published.
namePublishedInID can refer to a structured reference in the reference file or the full citation can be given as namePublishedIn.

```
taxon.csv
taxonID;scientificName;scientificNameAuthorship;taxonRank;namePublishedInID;namePublishedIn
325655-2;Abies;Mill.;genus;8134-2;Gard. Dict. Abr., ed. 4. (1754).
```

## New combination

	 - 	```Arethusa divaricata L.```
	 - 	```Cleistes divaricata (L.) Ames```

```
<TaxonNames>
  <TaxonName id="325" nomenclaturalCode="Botanical">
    <Simple>Fallopia dumetorum (L.) J. Holub</Simple>
    <Basionym>
      <RelatedName ref="326"/>
    </Basionym>
  </TaxonName>
  <TaxonName id="326" nomenclaturalCode="Botanical">
    <Simple>Polygonum dumetorum L.</Simple>
  </TaxonName>
</TaxonNames>
```

__single taxon core__:
By using dwc:originalNameUsageID in the taxon core one can express the basionym relation of a name.
```
taxon.csv
taxonID;scientificName;scientificNameAuthorship;originalNameUsageID
325;Fallopia dumetorum;(L.) J. Holub;326
326;Polygonum dumetorum;L.;
```

__taxon core & acts__:
_taxon.csv_
```
taxonID;scientificName;scientificNameAuthorship
325;Fallopia dumetorum;(L.) J. Holub
326;Polygonum dumetorum;L.
```

_acts.csv_
```
taxonID;actType;relatedTaxonID
325;publication;326
```



## Correction
Correction of an invalid/unavailable name in a subsequent publication.

```
<TaxonNames>
  <TaxonName id="123" nomenclaturalCode="Botanical">
    <Simple>Gossypium tomentosum Nutt. ex Seem.</Simple>
    <CanonicalAuthorship>
      <Simple>Nutt. ex Seem.</Simple>
      <Authorship>
        <Simple>Nutt. ex Seem.</Simple>
        <Authors>
          <AgentName role="ex">Nuttall</AgentName>
          <AgentName>Seem</AgentName>
        </Authors>
      </Authorship>
    </CanonicalAuthorship>
    <BasedOn>
      <RelatedName ref="124"/>
    </BasedOn>
  </TaxonName>
  <TaxonName id="124" nomenclaturalCode="Botanical">
    <Simple>Gossypium tomentosum Nutt.</Simple>
  </TaxonName>
</TaxonNames>
```

In its simplest form this can be expressed by citing an ex author in the scientific name authorship.

__single taxon core__:
```
taxon.csv
taxonID;scientificName;scientificNameAuthorship
123;Gossypium tomentosum;Nutt. ex Seem.
```

__taxon core & acts__:
Explicitly listing the names and relation is preferred as it allows to cite all publications:

_taxon.csv_
```
taxonID;scientificName;scientificNameAuthorship;nomenclaturalStatus
123;Gossypium tomentosum;Nutt. ex Seem.;
124;Gossypium tomentosum;Nutt. mss.;nom.inval.
```

_acts.csv_
```
taxonID;actType;relatedTaxonID;publishedInID
123;correction;124;ref1
```
_references.csv_
```
referenceID;standardForm;author;date;title;volume;collation
ref1;Fl. Vit. 22.;Seemann, Berthold Carl;1865;Flora Vitiensis;1;22
```



## homonym
### Pedicularis inconspicua

```
<TaxonNames>
  <TaxonName id="123" nomenclaturalCode="Botanical">
    <Simple>Pedicularis inconspicua P.C. Tsoong</Simple>
    <PublishedIn>Acta Phytotax. Sin. 3: 292 &amp; 323, Jan. 1955</PublishedIn>
  </TaxonName>
  <TaxonName id="124" nomenclaturalCode="Botanical">
    <Simple>Pedicularis inconspicua Vved.</Simple>
    <PublishedIn>Fl. URSS 22: 811, 18 Jun. 1955.</PublishedIn>
    <LaterHomonymOf>
      <RelatedName ref="123"/>
    </LaterHomonymOf>
  </TaxonName>
  <TaxonName id="125" nomenclaturalCode="Botanical">
    <Simple>Pedicularis inconspicua P.C. Tsoong</Simple>
    <PublishedIn>Bull. Brit. Mus. (Nat. Hist.) 2:17, Nov. 1955</PublishedIn>
    <LaterHomonymOf>
      <RelatedName ref="123"/>
    </LaterHomonymOf>
  </TaxonName>
</TaxonNames>
```



### Trillium texanum

```
<TaxonNames>
  <TaxonName id="123" nomenclaturalCode="Botanical">
    <Simple>Trillium texanum Buckley</Simple>
    <PublishedIn>Proc. Acad. Nat. Sci. Philadelphia 1860: 443. 1861</PublishedIn>
  </TaxonName>
  <TaxonName id="124" nomenclaturalCode="Botanical">
    <Simple>Trillium pusillum Michx. var. texanum J.L.Reveal &amp; C.R.Broome</Simple>
    <PublishedIn>Castanea, 46(1): 56 (1981)</PublishedIn>
    <Basionym>
      <RelatedName ref="123"/>
    </Basionym>
  </TaxonName>
  <TaxonName id="125" nomenclaturalCode="Botanical">
    <Simple>Trillium pusillum Michx. var. texanum (Buckley) C.F.Reed</Simple>
    <PublishedIn>Phytologia, 50(4): 279, 283 (1982)</PublishedIn>
    <Basionym>
      <RelatedName ref="123"/>
    </Basionym>
    <LaterHomonymOf>
      <RelatedName ref="124"/>
    </LaterHomonymOf>
  </TaxonName>
</TaxonNames>
```




## conservation



## rejection


## replacement

```
<TaxonNames>
  <TaxonName id="123" nomenclaturalCode="Botanical">
    <Simple>Myrcia lucida McVaugh (1969)</Simple>
    <Typification>
      <TypeVoucher typeOfType="holo">
        <VoucherReference>Spruce 3502</VoucherReference>
      </TypeVoucher>
    </Typification>
    <ReplacementNameFor>
      <RelatedName ref="124"/>
    </ReplacementNameFor>
  </TaxonName>
  <TaxonName id="124" nomenclaturalCode="Botanical">
    <Simple>Myrcia laevis O. Berg (1862)</Simple>
    <Typification>
      <TypeVoucher typeOfType="holo">
        <VoucherReference>Spruce 3502</VoucherReference>
      </TypeVoucher>
    </Typification>
    <LaterHomonymOf>
      <RelatedName ref="125"/>
    </LaterHomonymOf>
  </TaxonName>
  <TaxonName id="125" nomenclaturalCode="Botanical">
    <Simple>Myrcia laevis G. Don (1832)</Simple>
    <Typification>
      <TypeVoucher typeOfType="holo">
        <VoucherReference>Some other type</VoucherReference>
      </TypeVoucher>
    </Typification>
  </TaxonName>
</TaxonNames>
```


# emendation


```
<TaxonNames>
  <TaxonName id="225" nomenclaturalCode="Botanical">
    <Simple>Persicaria segetum (Kunth) Small (1903)</Simple>
    <SpellingCorrectionOf>
      <RuleConsidered>23.5</RuleConsidered>
      <Note>Correction of epithet gender</Note>
      <RelatedName ref="226">Persicaria segeta (Kunth) Small (1903)</RelatedName>
    </SpellingCorrectionOf>
  </TaxonName>
  <TaxonName id="226" nomenclaturalCode="Botanical">
    <Simple>Persicaria segeta (Kunth) Small (1903)</Simple>
  </TaxonName>
</TaxonNames>
```




# Other examples
## sanctioned names

```
<TaxonNames>
  <TaxonName id="123" nomenclaturalCode="Botanical">
    <Simple>Agaricus personatus Bolton : Fr</Simple>
    <PublishedIn>Hist. fung. Halifax 2: 58 (1788)</PublishedIn>
    <ConservedAgainst>
      <RelatedName ref="124"/>
    </ConservedAgainst>
    <ConservedAgainst>
      <RelatedName ref="125"/>
    </ConservedAgainst>
    <ConservedAgainst>
      <RelatedName ref="126"/>
    </ConservedAgainst>
    <Sanctioned>
      <PublishedIn>Syst. mycol. 1: 126 (1821)</PublishedIn>
    </Sanctioned>
  </TaxonName>
  <TaxonName id="124" nomenclaturalCode="Botanical">
    <Simple>Agaricus peronatus Valenti</Simple>
  </TaxonName>
  <TaxonName id="125" nomenclaturalCode="Botanical">
    <Simple>Agaricus peronatus Lasch</Simple>
  </TaxonName>
  <TaxonName id="126" nomenclaturalCode="Botanical">
    <Simple>Agaricus peronatus With.</Simple>
  </TaxonName>
</TaxonNames>
```
