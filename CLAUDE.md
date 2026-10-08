# DICO_CodeLists (instructies voor Claude)

Dit bestand bevat de werkinstructies voor het beheer van dit repo. Het
externe gebruiksoverzicht staat in `README.md`.

Centrale codelijst-repository voor DICO-standaarden (zie `DECISIONS.md` D29 in
[APIBase](C:\GIT\APIBase\DECISIONS.md)). DICO OpenAPI-specs verwijzen via
externe `$ref` naar de bestanden hier in plaats van codelijsten inline te
dupliceren.

## Bestanden

Dit repo is de plek waar de codelijsten beheerd worden. `codelist.xsd` en
`codelist.yaml` worden los van elkaar en met de hand onderhouden; er is geen
generator-script en geen van beide is afgeleid van de ander.

- `README.md`: gebruiksinstructies voor externe partijen.
- `codelist.xsd`: de SALES005-codelijsten in het legacy XSD-formaat, met
  modificatiehistorie. Wordt niet meer bijgewerkt vanuit Semantic Treehouse
  (besluit 01-07-2026).
- `codelist.yaml`: OpenAPI-componenten voor de DICO OpenAPI-specs, met twee
  soorten codelijsten:
  1. SALES005-codelijsten, elk als D19-schemapaar (`{Naam}` voor validatie +
     `{Naam}.Display` voor documentatie/UI met EN `title` + NL `x-titleNl`).
  2. Codelijsten die rechtstreeks van [Semantic Treehouse](https://ketenstandaard.semantic-treehouse.nl/codelist)
     zijn overgenomen. Sinds 01-07-2026 is Treehouse de bron van waarheid
     voor deze lijsten. Elk item vermeldt zijn Treehouse-URI in de
     schema-`description`.

  Bevat ook een `x-todoCodelists`-sectie: codelijsten die wel op Treehouse
  geregistreerd staan maar daar nog 0 waarden hebben. Vul deze **niet** in
  met de oude concept-waarden uit draft-specs (venetian blind pilot,
  GIR-drafts) die tijdens de eerste conversie zijn aangetroffen, die zijn
  niet tegen Treehouse geverifieerd.

## Wijzigen

- Wijzigingen in een SALES005-codelijst moeten in zowel `codelist.xsd` als
  `codelist.yaml` worden doorgevoerd.
- **Elke waarde van elke codelijst heeft een Engelse en een Nederlandse
  omschrijving**, in beide bestanden. In de xsd: `xs:documentation
  xml:lang="EN"` en `"NL"`. In de yaml (`{Naam}.Display`): `title` (EN) en
  `x-titleNl` (NL), en bij een omschrijving `description` (EN) en
  `x-descriptionNl` (NL). Een nieuwe waarde zonder beide talen is onvolledig.
- Leg elke wijziging vast in de modificatiehistorie van beide bestanden
  (`ModificationHistory` in de xsd, `x-modificationHistory` in de yaml) en
  werk de revisiedatum bij (`RevisionDate` / `x-revisionDate`).
- Een Treehouse-codelijst wordt alleen in `codelist.yaml` aangepast, naar het
  voorbeeld van Treehouse. Om een codelijst uit `x-todoCodelists` te
  promoveren: controleer de Treehouse-URI (de `bron`-waarde in het
  todo-item) en verplaats het item naar een echt schemapaar zodra daar
  waarden gepubliceerd staan.
- Gebruik geen em-dash (`—`) in de content (huisstijlregel, zie
  `DECISIONS.md` sectie "AI-instructies voor het genereren van
  DICO-content" in APIBase).

## Importbestanden (OASIS genericode)

Voor elke codelijst die wordt aangemaakt of gewijzigd hoort een importbestand
in het formaat **OASIS genericode 1.0** (XML, `.gc`).

**Het formaat volgt altijd de OASIS-specificatie**:
<https://docs.oasis-open.org/codelist/genericode/v1.0/genericode-v1.0.html>
(schema: <https://docs.oasis-open.org/codelist/genericode/xsd/genericode.xsd>).
De eisen hieronder zijn een samenvatting; bij verschil of twijfel geldt de
specificatie. Raadpleeg de specificatie bij elke nieuwe of gewijzigde
structuur (extra kolommen, andere datatypes) en verzin geen eigen
elementen of attributen.

**Waar:** alleen op de branch `genericode`, in de map `genericode/`, een
bestand per codelijst: `genericode/{Naam}.gc`. `main` bevat uitsluitend
`codelist.yaml`, `codelist.xsd`, `README.md` en `CLAUDE.md`; zet geen
importbestanden op `main`. Na een wijziging op `main` wordt `main` eerst in
`genericode` gemerged, daarna worden de importbestanden bijgewerkt.

**Eisen aan het bestand:**

- UTF-8, wel-gevormd XML, tab-inspringing, geen em-dash.
- Root-element `gc:CodeList` met namespace
  `http://docs.oasis-open.org/codelist/ns/genericode/1.0/`. De kindelementen
  zijn niet namespace-gekwalificeerd.
- Volgorde van de onderdelen: `Identification`, `ColumnSet`, `SimpleCodeList`.
- `Identification`:
  - `ShortName`: de naam van de codelijst, zoals in de yaml (bijvoorbeeld
    `StatusCode`).
  - `LongName` met `xml:lang="en"` en met `xml:lang="nl"`.
  - `Version`: de versie van de codelijst; bij een Treehouse-lijst de versie
    uit de Treehouse-export, anders de revisiedatum uit `codelist.xsd`.
  - `CanonicalUri`: `https://www.ketenstandaard.nl/codelist/dico/{Naam}`.
  - `CanonicalVersionUri`: `{CanonicalUri}/{Version}`.
  - `Agency` met `LongName` `Ketenstandaard Bouw en Techniek`.
- `ColumnSet`, alle kolommen `Use="required"` en `Data Type="string"`; bij
  een taalspecifieke kolom (`...En`, `...Nl`) staat op `Data` ook
  `Lang="en"` respectievelijk `Lang="nl"`:
  - `code` (sleutel), `titleEn`, `titleNl`; en `descriptionEn`,
    `descriptionNl` als de lijst omschrijvingen heeft.
  - Heeft de lijst extra attributen, dan komt er per attribuut en per taal
    een kolom bij (bijvoorbeeld `initiatorEn` en `initiatorNl` bij
    `EventCode`).
  - Eén `Key` met `Id="codeKey"` en `ColumnRef Ref="code"`.
- `SimpleCodeList`: een `Row` per waarde, in dezelfde volgorde als in de
  yaml, met voor elke kolom een `Value ColumnRef="..."` met `SimpleValue`.
  Speciale tekens worden ge-escaped. Elke rij heeft in alle kolommen een
  waarde, Engels en Nederlands.
- De inhoud moet gelijk zijn aan `codelist.yaml`. Bij een verschil wint de
  yaml; pas het importbestand aan.
- Controleer na het schrijven dat het bestand wel-gevormd XML is.

**Nog geen yaml/xsd-definitie:** `EventCode` (ONH, REX, VIN, AIN, VER, AFH,
THD) bestaat voorlopig alleen als importbestand. Kolommen: `code`, `titleEn`,
`titleNl`, `mainStatusEffectEn`/`Nl` (effect op de hoofdstatus),
`initiatorEn`/`Nl` (opdrachtnemer of opdrachtgever), `descriptionEn`/`Nl`.

Ontwerpbeslissingen achter dit format (dubbel D19-schema, bundeling per
domein i.p.v. per codelijst, bilinguale `title`/`x-titleNl`) staan in
`C:\GIT\APIBase\DECISIONS.md`, thema 9 (D19/D20) en D29.
