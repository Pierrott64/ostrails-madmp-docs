# Annexes

Real excerpts, taken from the repositories rather than written for this document.
Account names and addresses are replaced by placeholders in angle brackets.

## A. A rules field

From the RDA DMP Common Standard file, `rules/standards/rda_dcs/1.0.0.json`. A
required scalar with a closed vocabulary.

```json
"ethical_issues_exist": {
  "_cardinality": "1",
  "_type": "string",
  "_allowed_values": ["yes", "no", "unknown"],
  "_description": "Whether the project raises ethical issues."
}
```

From the OSTrails Application Profile, `rules/standards/ostrails/1.0.0.json`. The
one field of that profile carrying a vocabulary.

```json
"data_access": {
  "_cardinality": "0..1",
  "_type": "string",
  "_allowed_values": ["open", "closed", "shared"],
  "_description": "The overall access level for this DMP's data (open, closed, or shared)."
}
```

Everything else in the profile is a new field with a cardinality, a type and a
description, or a structural parent redeclared exactly as the base declares it so
that the tree can be walked down to those new fields.

## B. A project configuration

`configs/projects/glider.yaml`, complete except for comments.

```yaml
id: glider
name: "SOCIB Glider"
version: "1.0.0" # bump to publish a change
author: "Pierre St-Cricq dit Lompre (SOCIB)"
license: "CC BY 4.0"

rules:
  - rda_dcs: "1.0.0"
  - ostrails: "1.0.0"

organizationId: socib
description: |-
  Implements the RDA DMP Common Standard v1.2 (maDMP), extended with the
  OSTrails Application Profile, in DS Wizard. Every field of the standards is
  asked of the researcher.
references:
  - label: "RDA DMP Common Standard specification"
    url: "https://github.com/RDA-DMP-Common/RDA-DMP-Common-Standard"
  - label: "OSTrails project"
    url: "https://ostrails.eu"
auto_timestamps: true
```

The DMP's own identifier needs no configuration. The template derives it from the
render context, and the webhook rewrites it on commit.

## C. A registry folder

```
projects/glider/
  meta.yaml
  template/dmp_glider_template.json
  productions/
```

`meta.yaml`, written by the generator side and read by quality control:

```yaml
id: glider
rules:
- rda_dcs: 1.0.0
- ostrails: 1.0.0
```

Two keys, and they are the two that matter: which project this folder is, and
which exact rule versions its DMPs must be judged against. Anything else the file
comes to carry is written by the registry's own automation and carried across
untouched.

## D. A submitted maDMP

This is a real committed document from an end-to-end test, shown in full. It was
submitted from an unanswered questionnaire on purpose, which makes it the
clearest possible illustration of two decisions.

```json
{
  "dmp": {
    "created": "2026-08-11T15:05:51.983614Z",
    "ethical_issues_exist": "",
    "language": "",
    "modified": "2026-08-13T08:27:45.740789Z",
    "title": "",
    "contact": {
      "mbox": "",
      "name": "",
      "contact_id": [
        {
          "identifier": "",
          "type": ""
        }
      ]
    },
    "dmp_id": {
      "identifier": "https://raw.githubusercontent.com/pstcricq/ostrails-madmp-registry/main/projects/glider/template/dmp_glider_template.json",
      "type": "url"
    }
  }
}
```

**Required fields are present and empty.** A DMP missing a mandatory field says
so. An absent key would not.

**The identifier points at the committed file.** Inside the platform it was that
instance's own URL for the document. The webhook rewrote it on commit, which is
the moment the document acquired an address that does not depend on a running
service.

Optional fields that were not answered are simply absent, which is why a document
from an empty questionnaire is short rather than a wall of empty keys.

## E. Reproducing it

Validation and generation need no platform, no registry and no credentials.

```bash
uv sync
uv run pytest
uv run python scripts/validate_rules.py
uv run python scripts/validate_configs.py
uv run python scripts/validate_projects.py
uv run python scripts/validate_generation.py
```

The last one writes the questionnaire and the document template for every project
under a build directory that is never committed.

One project at a time:

```bash
uv run python -m dsw.generate_km configs/projects/glider.yaml
uv run python -m dsw.generate_template configs/projects/glider.yaml
```

Bringing up a full platform stack with the submission webhook beside it, from the
deployment repository:

```bash
cp .env.example .env   # then fill in the eight empty values
bash scripts/setup.sh
```

Publishing into an instance needs its address and an account, and the submission
route additionally needs the webhook's address, the shared secret, and the
registry coordinates. None of them has a default value.

## F. References

| | |
|---|---|
| RDA DMP Common Standard | <https://github.com/RDA-DMP-Common/RDA-DMP-Common-Standard> |
| RDA DCS DOI | <http://doi.org/10.15497/rda00039> |
| OSTrails project | <https://ostrails.eu> |
| Data Stewardship Wizard | <https://ds-wizard.org> |
| DSW document template specification | <https://guide.ds-wizard.org/en/4.31/more/development/document-templates/specification.html> |
| DSW metamodel schemas | <https://github.com/ds-wizard/dsw-schemas/tree/main/schemas> |
| DSW deployment example, the upstream of our deployment | <https://github.com/ds-wizard/dsw-deployment-example> |
| SOCIB | <https://www.socib.es> |
| the SOCIB platform instance | <https://dsw.priv.socib.es/wizard/> |
| the rules, the generators, the publisher | <https://github.com/pstcricq/ostrails-madmp-core> |
| the platform deployment and the submission webhook | <https://github.com/pstcricq/ostrails-madmp-dsw> |
| the registry the rendered plans are committed to | <https://github.com/pstcricq/ostrails-madmp-registry> |
| this documentation | <https://github.com/pstcricq/ostrails-madmp-documentations> |

## G. Contacts

| | | |
|---|---|---|
| Pierre St-Cricq dit Lompre | SOCIB | pstcricq@socib.es |
| Miguel Charcos Llorens | SOCIB | mcharcos@socib.es |
| Oana Dragomir | SOCIB | |
