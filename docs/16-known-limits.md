# Known limits

These are the limits we know about and have not solved. Each one says what
would make us reconsider it, because a limit without a trigger is a limit nobody
ever revisits.

Listing them is deliberate. A proof of concept that only reports what works is
not evidence, it is advertising.

## Nothing confronts the generated packages with a running instance

This is the biggest one.

What is verified: the questionnaire bundle conforms to the platform's published
metamodel schema, checked against the official file with zero errors and held by
a test. The document template body parses as valid template syntax, for every
project. The tests render it with the platform's own three filters replaced by
doubles.

What is not verified: that a given instance **accepts** what we publish, and that
the template actually renders there. Two metamodel versions are pinned by hand.
They were re-verified against the platform's source at the 4.31 tag rather than
taken on trust, but the check is against the source, not against a live server. A
disagreement about what one of those filters does would surface at render time,
in front of a researcher.

**Trigger to reconsider:** an instance a continuous integration runner can reach.
At that point the question stops being "imitate the filters better" and becomes
"publish, then render a test DMP".

## The pinned versions describe one platform release

The two metamodel versions describe the **instance**, not the project. They are
correct for the 4.31 release this was built and tested against. Nothing in the
system checks that the instance it is publishing to accepts them, and a newer
platform release is an unknown until someone tries it.

**Trigger:** the first upgrade, or the first deployment onto an instance somebody
else operates at a different version.

## Quality control is not yet in the chain

The engine exists and has been exercised, but it is not wired into the pipeline
described in this document, and the checks that need the network are not written.
Until it is, a submitted DMP is stored and versioned but not automatically
judged.

There is a visible consequence today. A DMP submitted from an empty questionnaire
carries empty required fields, exactly as designed, and nothing currently says
so out loud at submission time.

**Trigger:** the port described in [section 14](14-what-is-next.md). It is the
next slice.

## One project and one profile

The repository holds one project configuration, and the profile stack is one base
standard plus one extension that only adds fields. Continuous integration
generates every project precisely because a second project's rules, vocabularies
and pins are data no unit test has seen, but there is only one.

The merge machinery is therefore exercised on fixtures rather than on real
disagreement between two standards.

**Trigger:** a second profile, national or institutional. That is when the
tighten-only rules stop being a design and start being a service.

## The pre-filled content is not validated

The mechanism that derives a starting DMP from an observing facility's
instruments is settled. The content it would carry is not, and needs validation
by a domain professional. We have deliberately not invented it, which is why this
document describes the mechanism and not an instrument catalogue.

**Trigger:** that validation.

## Two operational limits of the submission webhook

**Idempotence stops above one megabyte.** The GitHub API inlines a file's content
up to that size and returns it empty above. A DMP larger than that never compares
equal to what is stored, so every submission commits again instead of reporting
that nothing changed. Nothing breaks, the guarantee quietly stops holding.

**No retry, and no rate limit handling.** GitHub answering with a refusal or a
rate limit surfaces to the platform as a gateway error. That is acceptable for
one instance submitting occasionally, and would not be for many.

**Trigger for both:** a DMP that large, or a second instance submitting into the
same registry.

## Scale has not been tested

One registry repository, one folder per project, one file per DMP. Git handles
that comfortably for the volumes an institution produces, and we have no evidence
about the volumes an infrastructure with many facilities and many deployments per
year would produce.

**Trigger:** the resolution step of [section 14](14-what-is-next.md) landing,
since that is what turns one plan into many documents.

## What we chose not to fix

Two small things, listed so that nobody spends an afternoon rediscovering that we
knew.

**A pin that did not come from a validated configuration** would fail with a bare
type error rather than a named one. The only caller today loads its configuration
first, so it cannot happen. The candidate caller that would change this is
quality control reading the pins recorded beside a submitted DMP, and the fix is
decided with that caller in view rather than in advance.

**The formatting of the data files is not checked.** Their *content* is validated
thoroughly. Enforcing their layout would mean a second dependency ecosystem in a
single-language pipeline, for a cosmetic gain.

**Trigger for both:** someone other than the current authors editing those files
regularly.
