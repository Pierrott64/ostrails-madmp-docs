# Standards and profiles

This is the section a pilot planning its own application profile should read
first. It describes how profiles stack onto a base standard, what a profile is
allowed to do, and what happens when two of them disagree.

## Exactly one base, any number of extensions

One file declares itself the base. The rest are extensions. A project's
configuration pins the versions it is built from, base first:

```yaml
rules:
  - rda_dcs: "1.0.0"
  - ostrails: "1.0.0"
```

The OSTrails Application Profile is a pure extension. It declares 21 fields, of
which 17 are new leaves such as `dmp.data_access`, `dmp.dataset[].methodology`
or `dmp.dataset[].distribution[].access_methods`. The other four are structural
parents redeclared exactly as the base declares them, purely so the tree can be
walked down to the new leaves. It redefines no cardinality of the base.

## The invariant

> A document valid under the merged model must remain valid under each standard
> taken on its own.

Everything else in this section is a consequence of that sentence rather than a
preference. Loosening would break it: a DMP conforming to our merged model could
violate the RDA standard, and we would have produced a format that *claims* to
implement a standard without doing so.

## What an extension may do, and what it may not

**May**, in two cases only:

- **redeclare a field identically**, which is the common case, repeating a
  structural parent solely to reach its own leaves underneath
- **tighten**: make an optional field required (`0..1` to `1`, `0..n` to
  `1..n`), narrow a vocabulary to a subset, close an open field with a
  vocabulary of its own, or close a recommended vocabulary over the values it
  recommends

**May not**: weaken a cardinality, turn a single value into a list or the
reverse, change a type, widen a vocabulary, or reopen a closed vocabulary as
merely recommended. Each of those is a conflict, reported by name.

## Each extension is judged against the base, never against another extension

This is the rule everything else rests on, and it comes from a fact about
authors. OSTrails is written against the RDA standard. A future institutional
profile will be too. **Neither of them knows what the other requires**, or even
that some project will pin them together.

The merge originally did the opposite, applying extensions one after another,
each compared to the result of the ones before. An extension redeclaring a
cardinality identically, the common case, therefore became a "loosening" as soon
as another had passed first:

```
[base 0..1, tightens to 1, redeclares 0..1]  ->  CONFLICT
[base 0..1, redeclares 0..1, tightens to 1]  ->  accepted
```

Same files, opposite verdicts depending on the order the pins were written in.
It was an artifact of the implementation, and it blamed an author for something
they could not have known.

Each extension is now validated against the frozen declaration of the standard
that **introduced** the field, and what the extensions require then combines:

- the strictest cardinality wins. Two tightenings cannot contradict each other,
  since a shape has only one required form
- vocabularies **intersect**. A DMP respecting both standards respects both
  restrictions, so the field keeps only the values both accept
- an **empty** intersection is a conflict naming both standards, rather than a
  field nobody can fill in

The operation is commutative and associative, so the result no longer depends on
the order of the pins. That is verified over **every permutation** of three
extensions, rather than over the one order a test author would have written.

## A merged field never carries two vocabularies

The loader forbids a closed and a recommended vocabulary on the same field
*within one file*. The merge is the only other path by which a field could end
up carrying both, and it used to keep the promise by halves: the two keys were
merged independently, so a base that recommends plus an extension that closes
produced a field carrying both, and the recommendation was silently dropped
downstream. The rule could be worked around by writing two files.

The pair is therefore read as **one fact with a nature**, closed or recommended,
and changing that nature is a move like any other.

- **recommended to closed** tightens: a departure was a warning, it becomes a
  failure. Accepted, and the recommendation **disappears** from the merged
  field. It is not information kept for the record, it has become false.
- **closed to recommended** loosens. Conflict.

**A closure must cover a subset of the recommended values.** Closing on `["z"]`
a field where the base recommends `["a", "b"]` is formally stricter, since
everything was permitted before, but it **forbids what the base recommends**.
That is not a tightening, it is a disagreement between two standards, and making
it visible is exactly what tighten-only merging is for.

## The order of a vocabulary belongs to the base

It is the order a researcher reads the options in, and it is not a constraint.
Validity is judged on sets, but two consequences follow that were not obvious.

- A vocabulary **rewritten in a different order** is the same vocabulary. It is
  the no-op of a redeclared field, not a tightening, and it no longer changes
  the order of the options.
- A tightening **keeps the base's order**, filtered of the removed values. An
  extension says *which* values are offered. Layout is not something tighten-only
  lets it decide.

## Prose follows the same rule, for the same reason

Descriptions constrain nothing, but they are not unregulated either. An
extension may **describe a field the base left undescribed**, and may **repeat**
what the base says, which is what a redeclared structural parent looks like.
Saying something *different* is a conflict.

Previously the base won silently and the extension's prose was thrown away
without a word. That is precisely the fault the coherence checks exist to catch:
text an author wrote, that the generators ignore, with nothing warning them.

Should an extension be able to replace a description? The question is not
settled, and that is exactly why we refuse. A refusal says so, silence hides it.

## Provenance, and an honest count

Every tightening is recorded on the field along with the standard that imposed
it. That is what lets quality control say "required by OSTrails, optional in RDA
DCS" instead of silently blaming the base standard. A conflict message names the
standard that **wrote the value in question**, which is a different question from
"who produced the current state" as soon as three standards are involved.

**On the real model the tightening count is currently zero.** RDA DCS plus
OSTrails produces none, because OSTrails adds 17 fields and restricts nothing of
the base. The machinery is built and tested on fixtures.

We are stating that plainly rather than presenting the merge as load-bearing
today. It is not dead code, it is code without a user *for now*, and the
distinction matters: the first institutional or national profile that makes an
optional RDA field mandatory is the moment it carries weight. Building it before
that point is what makes such a profile a data change rather than a code change.
