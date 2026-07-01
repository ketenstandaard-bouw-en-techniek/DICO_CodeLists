# DICO_CodeLists

Centrale codelijst-repository voor DICO-standaarden (zie `DECISIONS.md` D29 in
[APIBase](C:\GIT\APIBase\DECISIONS.md)). DICO OpenAPI-specs verwijzen via
externe `$ref` naar de bestanden hier in plaats van codelijsten inline te
dupliceren.

## Bestanden

- `codelist.xsd`: bron van de waarheid voor de SALES005-codelijsten (legacy
  XSD-formaat, met modificatiehistorie). **Wordt niet meer bijgewerkt vanuit
  Semantic Treehouse** (besluit 01-07-2026, zie hieronder); blijft voorlopig
  handmatig beheerd.
- `codelist.yaml`: **gegenereerd**, met twee bronnen:
  1. De SALES005-codelijsten uit `codelist.xsd`, elk als D19-schemapaar
     (`{Naam}` voor validatie + `{Naam}.Display` voor documentatie/UI met EN
     `title` + NL `x-titleNl`).
  2. Codelijsten die rechtstreeks van [Semantic Treehouse](https://ketenstandaard.semantic-treehouse.nl/codelist)
     zijn overgenomen (niet in `codelist.xsd`, en dat bestand wordt hiervoor
     ook niet bijgewerkt). Sinds 01-07-2026 is Treehouse de bron van
     waarheid voor `codelist.yaml`. Elk item vermeldt zijn Treehouse-URI in
     de schema-`description`.

  Bevat ook een `x-todoCodelists`-sectie: codelijsten die wel op Treehouse
  geregistreerd staan maar daar nog 0 waarden hebben. Vul deze **niet** in
  met de oude concept-waarden uit draft-specs (venetian blind pilot,
  GIR-drafts) die tijdens de eerste conversie zijn aangetroffen, die zijn
  niet tegen Treehouse geverifieerd.
- Het generator-script staat **niet** in dit repo maar in het aparte
  tooling-repo APIBase: `C:\GIT\APIBase\Codelists generator script\generate_codelist_yaml.py`.
  Regenereert `codelist.yaml`. **Bewerk `codelist.yaml` niet met de hand**:
  voor SALES005-codelijsten bewerk `codelist.xsd`; voor Treehouse-codelijsten
  bewerk de `TREEHOUSE_CODELISTS`/`todo_items`-datastructuren in het script.
  Draai daarna het script opnieuw.

## Herstarten in een nieuwe sessie

Als `codelist.xsd` is gewijzigd (nieuwe waarden, nieuwe codelijst,
modificatiehistorie), of als `codelist.yaml` opnieuw gegenereerd moet
worden:

```bash
python "C:/GIT/APIBase/Codelists generator script/generate_codelist_yaml.py"
```

Het script gebruikt standaard de paden in dit repo (`codelist.xsd` in,
`codelist.yaml` uit); met `--xsd`/`--out` kan het ook op een ander
domeinbestand worden losgelaten. Het is idempotent en heeft alleen de
Python-standaardbibliotheek nodig (geen `pyyaml`). Het weigert te schrijven
als er een em-dash (`—`) in de output zit (huisstijlregel, zie
`DECISIONS.md` sectie "AI-instructies voor het genereren van
DICO-content").

Om een codelijst uit `x-todoCodelists` te promoveren naar een echt schema:
controleer de bijbehorende Treehouse-URI opnieuw (de `bron`-waarde in het
todo-item), en zodra daar waarden gepubliceerd staan, verplaats het item van
`todo_items` naar `TREEHOUSE_CODELISTS` in
`C:\GIT\APIBase\Codelists generator script\generate_codelist_yaml.py` met de
daadwerkelijke code/titel/omschrijving-tuples, en draai het script opnieuw.
Voor nieuwe SALES005-codelijsten (niet op Treehouse): voeg toe aan
`codelist.xsd` (met EN/NL-documentatie per waarde, net als de bestaande
`xs:simpleType`-definities) en draai het script opnieuw.

Ontwerpbeslissingen achter dit format (dubbel D19-schema, bundeling per
domein i.p.v. per codelijst, bilinguale `title`/`x-titleNl`) staan in
`C:\GIT\APIBase\DECISIONS.md`, thema 9 (D19/D20) en D29.
