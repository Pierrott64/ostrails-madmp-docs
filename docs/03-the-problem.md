# The problem this addresses

Data Management Plans have been mandatory for years. Machine-actionable DMPs
have had a common standard since 2019. What is still missing, in practice, is a
way to produce one without hand-building three artifacts and then keeping them
in agreement forever.

## Three artifacts saying the same thing

A working maDMP setup needs three things, and every institution we have looked
at builds all three by hand.

1. **A questionnaire.** In a DMP platform such as the Data Stewardship Wizard,
   this is a knowledge model: chapters, questions, answers, controlled
   vocabularies. It is normally assembled in the web interface, question by
   question.
2. **A document template.** The thing that turns a researcher's answers into an
   actual maDMP document. In DSW it is a Jinja template, written by hand, whose
   every expression must reference a question by its identifier.
3. **A validator.** Whatever decides whether a produced DMP is any good. Usually
   a separate script, written against the standard rather than against the
   questionnaire.

All three encode the same knowledge: which fields exist, which are required,
which carry a closed vocabulary. Nothing forces them to agree.

## What goes wrong, concretely

The failure is never dramatic and that is the difficulty. Nothing crashes.

- A question is renamed in the knowledge model. The document template still
  references the old identifier. The rendered DMP silently loses a field, and
  the first person to notice is whoever reads the document, if anyone does.
- A field is made required by an institutional profile. The validator is
  updated. The questionnaire is not, so researchers are never asked, and every
  DMP fails a check for a question nobody was given the chance to answer.
- A controlled vocabulary gains a value in the standard. Three artifacts have to
  learn about it. Two do.
- A vocabulary label contains an apostrophe. In a hand-written Jinja template
  that is a syntax error inside a string literal, and it surfaces at render
  time, in front of a researcher.

Each of these is a small mistake. What makes them expensive is that they are
found late, by the wrong person, and that the effort of avoiding them is
permanent: every change to every standard means checking three places.

## A plan written once, describing nothing that happened

The second problem is not about tooling. A DMP is typically written at proposal
time, describing intentions in general terms, and never updated. It is a
document about a future that then diverges from it.

For an observing infrastructure this is particularly visible. A glider mission
is planned in the abstract once, but the actual deployments each have their own
dates, their own platform, their own instrument settings, their own resulting
datasets. One document cannot honestly describe all of them, and writing one per
deployment by hand is not something anyone will do.

## No record of what a plan was judged by

The third problem shows up the moment quality control exists. A DMP is checked
against a standard, and standards have versions. A verdict recorded today is
meaningless in two years unless it also records which version of which rules it
was reached under, and where the document itself was at the time.

Most setups keep the DMP in a platform's database, the rules in someone's head
or in a script, and the verdict in a report. None of the three points at the
others.

## What this case study proposes instead

- **One declarative rule set**, and the three artifacts derived from it
  mechanically, so that disagreement between them is not unlikely but
  impossible. [Section 4](04-the-principle.md) states the principle,
  [sections 6 to 10](06-the-rules.md) show it working.
- **A generic plan and its resolved realisations.** The maDMP produced from the
  questionnaire holds what is fixed, with placeholders for what varies. Each
  actual deployment resolves them into a DMP of its own. One plan, many concrete
  documents, all traceable back to it.
- **A git registry as the destination.** The document, the rule versions it must
  be judged by, and the history of both live next to each other, in a format
  that diffs, and at an address that does not move.
