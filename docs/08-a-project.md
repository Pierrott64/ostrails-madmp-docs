# A project

Standards say what a DMP must contain. A project says which standards apply,
who it belongs to, and what the packages generated for it announce. It is one
file.

## The unit of deployment is the configuration file

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
  OSTrails Application Profile, in DS Wizard.
auto_timestamps: true
```

One file carries **everything** that defines a project: its own facts, the rule
versions it pins, and what the generated packages announce. From it alone come a
knowledge model, a document template, a submission route and a registry folder.

The consequence is intended. A project moves **only** when its own file changes
and its version is incremented. Nothing is shared at runtime, so nothing drifts
behind a project's back because another one was edited.

It reads top to bottom in three blocks, the project, its rules, then the
platform, and the order is not decorative. It goes from the most stable to the
most editorial.

## One machine name, one human name, nothing in between

`id` is the **only** identifier. It is the name of the file, the name of the
destination folder in the registry and therefore the routing key a submission
carries, and what the knowledge model, the template and the platform project are
all named after. A pattern enforces it, so those names never depend on how it
was typed.

This replaced three identifiers naming the same project, two of which named it
with two different strings. Merging them removes the question "which of the two
does this generator use?". One redundancy is unavoidable, since a file must have
a name, so the loader checks it. Otherwise renaming a configuration would
publish the project somewhere else, without a word.

`name` is the one thing a reader sees that is declared rather than derived, and
the exception is reasoned. For a standard, the displayable form is a **total**
function of the identifier: upper-casing always works. For a project it is not.
`socib-hf-radar` would give `Socib Hf Radar`, and the list of cases a mechanical
rule would get wrong is not bounded.

What protects against drift here is therefore not derivation, it is **scope**.
`name` names nothing. No identifier, no path and no package depends on it, and
it can change at every version without a single reference moving.

## What a project resolves to

A configuration declares. A project is that declaration plus everything it
names, loaded. Three steps, each answering a question the others do not.

| step | crosses | yields |
|---|---|---|
| resolve pins | a declaration and a directory tree | file paths |
| merge rules | N documents with each other | one model |
| assemble | a configuration and its model | a project |

**Resolving pins** turns `{ostrails: "1.0.0"}` into the file it names, and
reports every pin that resolves to nothing, naming what does exist, so a typo is
corrected without going to look.

**Merging** folds the validated documents into one model, base first. It is the
only place that sees the *set*. Two extensions contradicting each other on a
shared field is a property of the combination, invisible file by file.

**Assembling** puts a configuration and its merged model together, in the one
order that works, so that no two consumers assemble a project differently.

## They compose, they do not call each other

This is worth one paragraph because it is not tidiness for its own sake.

The merge step is handed **paths**. It never learns that a pin, or a
configuration, exists. That is precisely what lets quality control merge the
pins recorded in a submitted DMP's registry metadata **without building a
project at all**. If merging called pin resolution, that path would demand the
configuration package for a file that is not a configuration.

Assembling, symmetrically, only fixes the order. That is little, and that is the
point. If every generator chained the two steps itself, two of them would
eventually disagree about what "this project" means.

## One project today

`glider` is the only configuration in the repository, and the SOCIB Application
Profile it would eventually pin does not exist yet. What the case demonstrates
is the mechanism: a second project is a second file, and an institutional
profile is one more rules file pinned by it, adding only what does not already
fit into the standards above it.

There is a check for exactly this. Every configuration goes through both
generators in continuous integration, because the rules, vocabularies and pins
of a second project are **data no unit test has ever seen**. What it verifies is
what the data decides rather than what the code decides: that no entity is
emitted twice, and that the generated template is parseable at all.
