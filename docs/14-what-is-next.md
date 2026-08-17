# What is next

Three lists, and the difference between them is the point. What is committed and
in progress, what is designed and waiting on something specific, and what is
genuinely still being decided. We would rather name the third than present it as
a plan.

## In progress

### Quality control, ported and completed

The engine exists and has been exercised. It walks the merged model and a
submitted document together and returns one flat list of results, with four
statuses:

- **fail**, a real violation: a required field missing, a shape that does not
  match the cardinality, a wrong type or format, a value outside a closed
  vocabulary
- **missing**, an optional field absent, which follows from cardinality alone and
  is never an error
- **warning**, a present value outside a recommended vocabulary, and also a key
  the *document* carries that no pinned rule declares, because a DMP may
  legitimately carry an extension this project does not pin
- **pass**

Where a constraint was imposed by an extension rather than by the base standard,
the message says so, from the provenance the merge recorded.

Assessment in this pilot has **three layers**, and they answer three different
questions on three different kinds of evidence. Blurring them is the easiest
mistake to make here, so they are named apart.

**Does the plan say valid things?** That is the layer described above, derived
from the rules, and it is **done**. Everything decidable from the document and the
rules alone. It is being ported into the current repository, and it is
deliberately plain data with no test runner involved, so the same results can
drive a continuous integration gate, a report, or a test suite.

**Do the data match what the plan promised?** A quality control of the datasets
and their metadata, written in Python at SOCIB, and used today outside the maDMP
pipeline. The intention is to fold it into the checks a
submitted plan faces, so that a plan can be confronted with the data it describes
rather than with itself alone. That is a longer term item, and
it is the layer that turns a conformance check into a statement about reality.

**Are the described outputs FAIR, and do their links hold?** Whether an identifier
resolves, whether a link is reachable, whether it will still be reachable in five
years. This needs the network, which raises questions the rest of the system has
not had to face: when to check, how often, and what a link that was unreachable on
one particular Tuesday should mean for a plan's verdict. Whether that layer is an
external tool or one of ours is still being decided, and it is where an existing
FAIR assessment tool would plug in.

Stated the other way round, so that nothing is claimed by implication: the checks
derived from the rules answer "is this DMP a valid instance of the standards it
pins?". They do not answer "are the data this DMP describes FAIR?", and they do
not answer "do those data exist and are they any good?". Three questions, three
kinds of evidence, and only the first can be derived from the rules.

### Resolving a generic plan into deployment DMPs

The layout is in place and the contract is written. What is missing is the
program that takes the real values of one deployment and resolves the generic
plan's placeholders into a document of its own.

This is the piece that turns Track from "documents are versioned" into "every
deployment has a plan of its own, traceable to the plan it came from". It is
also the piece that makes the approach interesting to an observing
infrastructure rather than to a funder's compliance process.

### Connecting the plans to a knowledge graph

The OSTrails and RDA models are the foundation of the Scientific Knowledge Graph
developed within JERICO and Blue-Cloud. They let the digital objects attached to a
given maDMP be identified and represented, datasets, software, services and
publications, while keeping their relationships and their provenance. In the
current implementation a maDMP is the entry point from which the research
lifecycle can be navigated.

Two extensions are under way. Reaching **external and domain specific graphs**
through the OSTrails Scientific Knowledge Graph Interoperability Framework, for a
view of research activity that does not stop at our own infrastructure. And a
sustainable strategy for **persistent identifiers** across distributed graphs, so
that a digital object stays identifiable, linkable and up to date as it moves
between infrastructures. The second is the harder of the two, and it is the one
that decides what a plan's own identifier should be.

### A deployment beside an operational instance

The stack described in [section 11](11-the-deployment.md) runs locally and
against a remote instance. The
next step is running the submission webhook beside the institutional instance in
production, which that deployment does not have today.

### An instance reachable from continuous integration

Nothing currently confronts the generated packages with a **running** platform.
The bundle is valid against the metamodel schema, the template body parses, and
the tests render it with the platform's own filters replaced by doubles. A
disagreement about what one of those filters does would only show up at render
time, in front of a researcher.

The fix is not a better imitation, it is an instance a runner can reach. At that
point the question becomes "publish, then render a test DMP" rather than "imitate
the filters more closely".

## Designed, waiting on something specific

**A pre-filled baseline.** A starting DMP derived from an observing facility's
instruments, so a researcher begins from something substantial rather than from
an empty questionnaire. The mechanism is settled. The content is waiting on
validation by a domain professional, and we are not going to invent it.

**A SOCIB application profile.** It does not exist yet, and this document does not
describe it as if it did. What is worth saying is what it would be: only the
fields an institution genuinely has of its own, since anything the RDA standard
or the OSTrails profile already says is not said again. A pilot planning its own
profile can read that as an estimate of its own cost.

**Conditional rules.** A field required only when another has a given value. The
rules language cannot express it, the decision to add it has been noted and not
built, and until then the constraint simply cannot be stated.

## Still being decided

We are naming these rather than presenting them as commitments.

**Which tool performs the non-logic assessment.** An existing FAIR assessment
tool, or one of ours. See above.

**Publishing selected DMPs.** The registry is the versioned store and stays
private. Which DMPs become public, on which platform, and with which
identifiers, is a separate decision that has not been taken.
