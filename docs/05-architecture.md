# Architecture

## Four components, and what each one knows

| component | what it is | what it knows |
|---|---|---|
| **ostrails-madmp-core** | the rules, the project configurations, the generators, the publisher | the standards and how to turn them into DSW packages. It knows an instance only as an address it was given |
| **ostrails-madmp-dsw** | a Data Stewardship Wizard 4.31 deployment, plus the submission webhook | how to run DSW, and how to commit one document into the registry. It knows nothing about rules or standards |
| **the DSW instance** | where a researcher answers | only what was published into it |
| **ostrails-madmp-registry** | a git repository, one folder per project | nothing. It is storage with a layout, and the layout is a written contract both sides honour |

The separation is deliberate and it is what makes the case transferable. A pilot
that wants the rules approach takes `ostrails-madmp-core`. A pilot that only wants a
reproducible DSW deployment with a submission hook takes `ostrails-madmp-dsw`. Neither
imports the other.

## The whole chain

```mermaid
flowchart TB
    subgraph source["Declarative source (ostrails-madmp-core)"]
        R["rules/standards/<br/>rda_dcs/1.0.0.json<br/>ostrails/1.0.0.json"]
        C["configs/projects/<br/>glider.yaml"]
    end

    subgraph derive["Resolution and generation"]
        M["resolve pins<br/>merge, tighten-only<br/>assemble project"]
        KM["Knowledge Model<br/>the questionnaire"]
        DT["Document Template<br/>Jinja that emits JSON"]
    end

    subgraph inst["DSW instance (ostrails-madmp-dsw)"]
        Q["questionnaire<br/>answered by a researcher"]
        DOC["rendered maDMP"]
        SUB["Submit"]
    end

    subgraph reg["ostrails-madmp-registry"]
        META["projects/glider/meta.yaml<br/>identity + pinned rule versions"]
        TPL["projects/glider/template/<br/>the committed maDMP"]
    end

    QC["Quality control<br/>presence, shape, type,<br/>vocabularies, provenance"]

    R --> M
    C --> M
    M --> KM
    M --> DT
    KM -->|publish km| Q
    DT -->|publish template| DOC
    C -->|register| META
    META -->|publish submission<br/>refuses an unregistered folder| SUB
    Q --> DOC
    DOC --> SUB
    SUB -->|webhook commits,<br/>rewrites dmp_id| TPL
    META -->|the pins to judge by| QC
    TPL --> QC
```

Two things in that picture are worth pausing on.

**The registry is written before the submission route exists.** The webhook
refuses a folder that carries no `meta.yaml`, so a submission service pointing at
an unregistered folder would turn every Submit into a failure the researcher gets
blamed for. Publishing the route therefore checks the folder first, and refuses.
The order is enforced by the code rather than by the order of the commands, since
a convention is exactly what gets skipped when someone runs one step by hand.

**Quality control reads the registry, not the configuration.** A submitted maDMP
is judged against the rule versions recorded in `meta.yaml` next to it, not
against whatever the configuration says today. That is what makes a verdict
reproducible a year later, and it is why the merge step can be handed a set of
file paths without ever learning that a project configuration exists.

## The life of one DMP

```mermaid
sequenceDiagram
    autonumber
    participant CI as ostrails-madmp-core CI
    participant DSW as DSW instance
    participant R as Researcher
    participant W as Submission webhook
    participant G as ostrails-madmp-registry

    CI->>G: register the project folder (meta.yaml, pinned versions)
    CI->>DSW: publish the Knowledge Model
    CI->>DSW: publish the Document Template
    CI->>DSW: declare the submission service, scoped to this project
    R->>DSW: answer the questionnaire
    R->>DSW: render the document
    R->>DSW: Submit
    DSW->>W: POST the rendered maDMP, project in the query string
    W->>G: commit it, rewriting dmp_id to the file's stable URL
    W-->>DSW: 200, with a link the researcher can follow
```

Steps 1 to 4 are a continuous integration run and happen without anyone present.
Steps 5 to 7 are the only ones a human performs. Steps 8 to 10 take under a
second.

## What crosses a boundary, and what does not

Three interfaces carry everything, and each is small enough to write down.

- **ostrails-madmp-core to a DSW instance**: the wizard API, four endpoints. A login, the
  package listings, the bundle uploads, and one read and one write of the tenant
  configuration. No shared library, no plugin, no database access.
- **DSW to the webhook**: one HTTP POST carrying the rendered document, with the
  project folder in the query string and a bearer token in a header. The
  document itself is never parsed to decide where it goes. Routing comes from
  the URL alone, so a malformed document cannot land in another project's folder.
- **the webhook to the registry**: two calls of the GitHub contents API, read a
  file and write a file. Nothing else.

The registry layout is the one thing two codebases both depend on, and neither
of them can derive it from the other. It is therefore written down, in the
registry's own README, as a contract they both honour.
