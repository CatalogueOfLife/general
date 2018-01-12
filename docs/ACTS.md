# Nomenclatural Acts
The Clearinghouse models uses nomenclatural acts instances to document publications relevant to the nomenclature of a name.
These acts also track name directed relations in the data model, very similar to TCS name relations. An acts consists of:

 - taxonID: the taxonID of the core name this act is about
 - relatedTaxonID: the taxonID of the related name in case the act describes a relationship
 - actType: a classification of acts and thereby the kind of relation. One of:
    - publication: Original publication of a new name, new combination (comb.nov) or name at new rank (stat.nov) 
      with the related name being the basionym if given which implies homotypic synonymy. 
      In case of the original publication of a new name no related name exists. TCS=Basionym
    - replacement: nomen novum with the related name being the replaced synonym. Implies homotypic synonymy. TCS=ReplacementNameFor
    - emendation: spelling correction. Implies homotypic synonymy. TCS=SpellingCorrectionOf
    - correction: subsequent publication correcting a previously invalidly published name. Often indicated by an ex authorship. Implies homotypic synonymy. TCS=BasedOn
    - conservation: conserved or protected name with the rejected name given as the related name. Sanctioned fungus names are indicated on the name directly via the authorship. TCS=ConservedAgainst
    - suppression: A name ruled as rejected outright under ICN Art. 56. No related name given
    - homonym: a later/junior homonym with the related name being the earlier/senior homonym. TCS=LaterHomonymOf
    - typification: a subsequent type designation
 - note: an informal note giving more details, e.g. the reasoning and related code articles
 - publishedInID: key to the reference this act was published in. In case of original or combination acts this is the cited publication the name was published in.


# Examples
TDWG's Taxon Concept Schema guide provides great examples illustrating the different act types above.
Most examples covered here are taken from the TCS guide and show how they would look in CoL+ Darwin Core terminology.

dwc:namePublishedIn

## New genus name

__single taxon core__:
By using dwc:namePublishedInID and related terms in the taxon core one can express information about where the name was published.
namePublishedInID can refer to a structured reference in the reference file or the full citation can be given as namePublishedIn.

```
taxon.csv
taxonID;scientificName;scientificNameAuthorship;taxonRank;namePublishedInID;namePublishedIn
325655-2;Abies;Mill.;genus;8134-2;Gard. Dict. Abr., ed. 4. (1754).
```

__taxon core, acts & references__:
_taxon.csv_
```
taxonID;scientificName;scientificNameAuthorship;taxonRank
325655-2;Abies;Mill.;genus
```

_acts.csv_
```
taxonID;actType;relatedTaxonID;publishedInID
325655-2;publication;;8134-2
```

_references.csv_
```
referenceID;identifier;bhlPage;standardForm;author;date;title;bibliographicCitation
8134-2;;p44045023|p44046094|p44045559;Gard. Dict. Abr., ed. 4.;Miller, Philip;28 Jan 1754;;;
```

## New combination
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
