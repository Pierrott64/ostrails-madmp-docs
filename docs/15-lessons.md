# Lessons

Every one of these was learned by getting it wrong first. They are ordered from
the most general to the most specific, and the last section is the one another
pilot deploying the same platform will get the most immediate use from.

## 1. Derive rather than synchronise

Two artifacts kept in agreement by discipline will diverge. Not because anyone is
careless, but because the effort is permanent and the failure is silent.

Producing both from the same data with the same program removes the question
instead of monitoring it. The test that matters is not "are they in agreement
today", it is "could they be in disagreement at all".

The transferable version: whenever you find yourself writing a checklist to keep
two things in step, look for the one place both could be generated from.

## 2. What has no undo is looked at, not reasoned about

Publishing a package cannot be taken back. Writing an instance's configuration
overwrites whatever a colleague changed in the console since your last read.

For those steps we stopped relying on the order of operations being right and
started reading the current state first. That is how we caught a bundle about to
be uploaded under a version it was not built with, and a submission route about
to be written without its path, over one that worked.

The rule we ended up with: an irreversible step reads before it writes, even when
the reasoning says it does not need to.

## 3. A default is one deployment's address baked into every other

No address or credential in this system has a fallback value. A missing one is an
error naming every missing variable at once.

This started as pedantry and turned out to be load-bearing. A wrong registry is
caught downstream. **A wrong instance is caught by nothing**, because it accepts
the package and nobody finds out. The same reasoning killed a tempting fallback
to a well-known token name: a token without access reads as a 404, which the
client would translate as "no file", so the fallback would have reported a
project as unregistered when the truth was "wrong credentials". Green, and wrong,
exactly where no write comes along to contradict it.

## 4. Report, or act

The useful line through a pipeline is not "before or after validation". Jobs that
**report** run in parallel and each names its own culprit. Jobs that **act** wait
for every verdict and for each other.

And a job that abstains should abstain **visibly**. Our writing jobs show as
skipped on a pull request rather than being absent, because an abstention nobody
can see is indistinguishable from a check that does not exist.

## 5. A test double that agrees with your code proves nothing

Our guard against overwriting an instance's configuration was correct in
principle and unreachable in practice: the platform returns nine keys, a write
carries six, so the comparison could never be true and every run wrote.

The test did not see it because the fake instance returned exactly what our own
code produced. Both sides of the test agreed about a shape **neither of them
owns**.

The habit that came out of it: when faking an external system, make the double
return what that system returns, not what your code sends it. If you cannot say
where the shape came from, the test is a mirror.

## 6. Refusing is kinder than silence

Several of our checks exist because something was being dropped without a word:
prose an author wrote that no generator would ever read, a recommended vocabulary
that a merge silently discarded, a chapter description in a place where it means
nothing.

In each case the fix was to refuse the file and say why. Where we were unsure
whether something *should* be allowed, we refused as well, deliberately. A
refusal can be discussed. A silent drop is discovered by whoever reads the output
carefully, which is nobody.

## 7. Build the general mechanism early, and say honestly that it has no user yet

The merge that stacks profiles onto a base standard supports conflicts, ordering
independence and provenance. On the standards actually loaded it currently
performs zero tightenings, because the OSTrails profile only adds fields.

We think building it first was right, because it is what makes a future national
or institutional profile a **data** change rather than a code change. But calling
it central to today's implementation would be false. Code without a user for now
is not dead code, and the distinction is worth stating out loud rather than
blurring.

## Platform pitfalls we hit

For pilots deploying the same DMP platform. None of these is documented as a
trap, and each cost us at least an afternoon.

- **The order of the events is the order of the questions.** There is no ordering
  field. Emit a parent after its child and the child disappears without an error.
- **A bundle outside the metamodel schema publishes fine.** The server ignores
  unknown keys, so extra fields are accepted and produce a package nobody else
  can validate. Ours had two, and 60 schema errors, until we checked against the
  published schema.
- **A vocabulary label ends up as source code**, not as data, inside a generated
  document template. A label containing an apostrophe stops the body being valid
  template syntax, and the failure surfaces at render time in front of a
  researcher. Escape labels the same way you escape identifiers.
- **The package listing returns only the latest version** per identifier. A
  version disappears from the listing the moment a newer one exists, so "is this
  published?" is exact for the newest and not for anything else.
- **There is no endpoint for a single submission service.** Writing one means
  sending the tenant's whole configuration back, which reverts everything changed
  in the console since your read.
- **The submission service's URL must carry its route.** Omitting the path writes
  a service that posts to the container root, which is a Submit button that
  returns 404, written over one that worked.
- **Enter through the client's own path, not the bare origin.** The root redirect
  is built from the protocol the container's web server sees, so behind a TLS
  terminator it sends the browser to plain HTTP on an HTTPS-only host.
- **The seeded demo accounts have published credentials**, and deleting them
  before creating your own administrator locks you out of your own instance with
  no way back but the database.
