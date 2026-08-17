# Rules-driven maDMP

Technical documentation for the SOCIB maDMP implementation, contributed to
OSTrails deliverable D4.3, *Case Studies and Proof of Concept Instances*.

**Read it as a site: <https://pstcricq.github.io/ostrails-madmp-documentations/>**

One declarative rule set, the RDA DMP Common Standard with the OSTrails
Application Profile on top of it, generates a Data Stewardship Wizard
questionnaire, the document template that turns answers into a
machine-actionable DMP, and the quality control that DMP is checked against.
The result is committed into a versioned registry.

This repository holds the documentation only, and it is written so that it can be
read without the code:

- the rules, the generators, the publisher: <https://github.com/pstcricq/ostrails-madmp-core>
- the platform deployment and the submission webhook: <https://github.com/pstcricq/ostrails-madmp-dsw>
- the registry the rendered plans are committed to: <https://github.com/pstcricq/ostrails-madmp-registry>

## Building it locally

```bash
pip install -r requirements.txt
mkdocs serve
```

## Contact

Pierre St-Cricq dit Lompre, Miguel Charcos Llorens and Oana Dragomir, SOCIB.
