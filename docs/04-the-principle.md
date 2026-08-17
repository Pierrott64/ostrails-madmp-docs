# The principle

> A rule added to a JSON file becomes a DSW question **and** a quality check,
> with no code and no room for drift.

Everything else follows from that sentence. This section says what it means
precisely, what it buys, and what it costs.

## Rules are data, and the only source of truth

A standard is a JSON file describing a tree of fields. Each field declares two
orthogonal facts and, optionally, a vocabulary and some prose.

This is a real one, from the RDA DMP Common Standard file:

```json
"ethical_issues_exist": {
  "_cardinality": "1",
  "_type": "string",
  "_allowed_values": ["yes", "no", "unknown"],
  "_description": "Whether the project raises ethical issues."
}
```

- **cardinality** says *how many*: `1`, `0..1`, `1..n`, `0..n`
- **type** says *what each value is*: an object, or a scalar format such as
  `date`, `email`, `url`, `country_code`, `language`
- **a closed vocabulary** (`_allowed_values`) or **a recommended one**
  (`_suggested_values`), never both

There is deliberately no `list` type. Being a list is carried entirely by
cardinality. A `list` type would create two ways to express the same thing, and
therefore two ways for one file to contradict another.

That is the whole vocabulary an author needs. No code, no template syntax, no
identifier to invent.

## Declared once, derived everywhere

| what a reader or a program meets | where it comes from |
|---|---|
| a chapter in the questionnaire | a top-level object field |
| a question, and its type | a field, through one decision function |
| the answers of a controlled vocabulary | the vocabulary's values |
| the order questions are read in | the order fields are declared in |
| the identifier of every entity | the path of the field it was generated for |
| the display name of a standard | the standard's own name, upper-cased where it is shown |
| the expression that reads an answer in the document template | the same field path |
| the checks a submitted document faces | the same field, its cardinality, its type, its vocabulary |

Almost nothing is declared twice. The one deliberate exception is a project's
human-readable name, because deriving `SOCIB HF Radar` from `socib-hf-radar`
mechanically is a function that fails on acronyms, accents and internal
capitals, and a derivation you must be able to override makes the override
mandatory. It is allowed to be declared precisely because it names nothing: no
identifier, no path, no package depends on it.

## Structural, not disciplinary

The promise is not "we are careful". Two mechanisms make drift impossible rather
than unlikely.

**Deterministic identity.** Every generated entity takes its UUID from a `uuid5`
over the path of the field it was generated for. The questionnaire generator and
the document template generator share no lookup table and never run in the same
process, yet both arrive at the same identifier for the same field. A template
cannot reference a question its knowledge model does not contain, and the way we
test that agreement is not a fixture: we generate both and confront every path
the template reads with the entities the knowledge model emits.

**A single decision point.** One function answers the question "what is this
field, logically?", with one of eleven answers. Every generator asks it and no
generator decides for itself. A field the questionnaire asks as a list and the
template renders as a single value would be a pair of packages nobody can fill
in, and no test of either one alone would catch it.

The general rule behind both: what several programs must answer identically, and
where disagreeing would be a fault, lives in exactly one place.

## Mechanism, not content

No code names a standard. A standard is a directory, the loader is handed one
file at a time, a project's configuration pins versions in an ordered list, and
the merge folds however many documents it is given. Removing a standard removes
data, not a capability. Adding a national or thematic profile is a file.

The same line runs through the whole repository. The rules packages interpret
nothing, the resolution step decides nothing about what gets built, and the
generators know nothing about where anything is published.

## The unit of deployment is one configuration file

One YAML file per project carries everything that defines it: its own facts, the
rule versions it pins, and what the generated packages announce. From it alone
come a knowledge model, a document template, a submission route and a registry
folder.

The consequence is intended. A project moves only when its own file changes and
its version is incremented. Nothing is shared at runtime, so no project drifts
because another one was edited.

## What it costs

Three prices, all knowingly paid.

**The identifier convention is frozen for life.** Once a knowledge model is
published, changing how UUIDs are derived would change every identifier and
break every existing reference inside the platform. This is not a guideline, it
is a test holding nine derived values against what has already been published.

**Everything must be expressible in the rules language.** A constraint the
language cannot state, such as "this field is required only when that other one
has a given value", cannot be expressed at all until the language gains it. We
consider that a feature: the alternative is an escape hatch into code, and an
escape hatch used once is used everywhere within a year.

**Validation is strict at the door, which makes authoring stricter.** A rules
file is checked three ways when it loads, and every problem in all three is
reported at once. A file that is misplaced, misnamed, or carries a vocabulary
with a duplicate value is refused rather than half accepted. Authors pay for
this in refused edits. The alternative is a typo reaching a researcher's
questionnaire.
