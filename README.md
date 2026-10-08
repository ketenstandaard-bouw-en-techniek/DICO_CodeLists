# DICO codelijsten

Gedeelde codelijsten voor de DICO-standaarden van
[Ketenstandaard Bouw en Techniek](https://www.ketenstandaard.nl). Alle
waarden hebben een Engelse en een Nederlandse omschrijving.

## Inhoud

| Bestand | Beschrijving |
|---|---|
| `codelist.yaml` | Codelijsten als OpenAPI-schema's, voor gebruik in DICO OpenAPI-specificaties. |
| `codelist.xsd` | De SALES005-codelijsten in XSD-formaat, inclusief modificatiehistorie. |
| `genericode/*.gc` | Importbestanden in het formaat OASIS genericode 1.0. Alleen op de branch `genericode`. |

## Gebruik in een OpenAPI-specificatie

`codelist.yaml` bevat per codelijst twee schema's onder `components/schemas`:

- `{Naam}`: de lijst met toegestane waarden (`enum`), voor validatie.
- `{Naam}.Display`: dezelfde waarden met Engelse `title` en Nederlandse
  `x-titleNl`, en waar beschikbaar `description` en `x-descriptionNl`. Alleen
  bedoeld voor documentatie en weergave, niet voor validatie.

Verwijs met een externe `$ref` naar het schema, in plaats van de waarden te
kopieren:

```yaml
components:
  schemas:
    Status:
      $ref: 'codelist.yaml#/components/schemas/StatusCode'
```

Gebruik het pad of de URL waaronder je `codelist.yaml` beschikbaar hebt.

Bovenin `codelist.yaml` staat de wijzigingshistorie (`x-modificationHistory`)
en de revisiedatum (`x-revisionDate`). Bij Treehouse-codelijsten staat in de
`description` van het `.Display`-schema de bron
([Semantic Treehouse](https://ketenstandaard.semantic-treehouse.nl/codelist)).

## Gebruik van de importbestanden

De bestanden in `genericode/` volgen
[OASIS genericode 1.0](https://www.oasis-open.org/committees/codelist/)
en zijn bedoeld om een codelijst in andere systemen in te lezen. Haal de map op
met:

```bash
git fetch origin genericode
git checkout genericode
```

Elk bestand bevat:

- `Identification`: naam, versie en de canonieke URI van de codelijst.
- `ColumnSet`: de kolommen `code`, `titleEn`, `titleNl` en, waar van
  toepassing, `descriptionEn` en `descriptionNl` (plus eventuele extra
  attributen per taal).
- `SimpleCodeList`: een rij per waarde.

## Wijzigingen voorstellen

Wijzigingen verlopen via Ketenstandaard Bouw en Techniek. Neem contact op met
de DICO Helpdesk van Ketenstandaard.
