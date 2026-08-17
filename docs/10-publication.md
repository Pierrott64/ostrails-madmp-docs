# Publication

Three targets push a project into a running platform instance, and they are two
different kinds of thing.

| target | what it writes | how it is idempotent |
|---|---|---|
| `km` | a package: an identity, a version, immutable | by the version. An already published package is skipped |
| `template` | likewise | likewise |
| `submission` | the instance's **own configuration**, upserted by identifier next to other projects' entries | by comparing what a write would carry |

Merging them into one command would suggest a single operation where there are
two, which do not replay the same way.

## Idempotence by the version

A package version is immutable, so republishing an identifier already present
would be rejected. Publication therefore lists first and **skips** what is
there, saying so. The intended consequence: running every project on every push
to the default branch costs almost nothing, and **only an incremented version
publishes**. There is no force flag. There is a version to raise.

One honest detail. "Already published" is exact about the latest version and
only that. The listing endpoints return one row per identifier, the most recent,
and a version disappears from the listing the moment a newer one is published.
The consequence is bounded: versions only go up, so the one being published is
always the latest and the answer is right. Publishing an **earlier** version, a
replayed old commit, would be told "not published", would attempt the upload,
and would be rejected by the platform. The degraded mode is a refusal.

Pagination is followed rather than trusted to a large page, for the same reason.
A truncated listing would answer "not published" about something that is, and
that answer is what decides whether anything is uploaded.

## The registry comes before the submission route, and code holds it

The webhook refuses a folder with no metadata file. A submission service
pointing at an unregistered folder therefore turns **every Submit into a
failure**, and the researcher is the one who looks at fault.

So publishing the submission route reads the folder's state first, and refuses.
Holding this by the order of the targets would be holding it by convention, and
a convention is what gets skipped when someone runs one target by hand. The
order in continuous integration remains, but it is no longer what guarantees.

A folder whose metadata is merely out of date passes. It is one sync away, and
the folder is there, which is all the webhook needs.

## Scoping a submission route to one project

Two things make it this project's and nobody else's: the folder in the URL,
which is the **only** routing input the webhook has, never the content of the
document, and the declared supported format naming this project's own template,
so the Submit menu offers this service for this project's documents and nothing
else.

## Writing the tenant configuration without reverting a colleague

There is no endpoint for one submission service. Writing ours means sending the
tenant's **entire** configuration back: organisation, authentication,
appearance. Anything changed in the web console between our read and our write
is therefore reverted without a word.

The guard is to write only when the result would say something else. Getting
that guard right took two passes, and the second one is the real lesson.

The read returns a service **as stored**, with a tenant identifier, timestamps
and a repeated service identifier. The write accepts a payload that has none of
them. Nine keys on one side, six on the other, so the comparison could never be
true, so **every run wrote**, so every run reverted whatever the console had
changed. The guard existed and was unreachable.

Two corrections, and the second is the one that matters:

1. we now produce only what a write carries. A field the API does not read is
   not a field we send.
2. what the instance returns is read back **through that same contract**, and
   that reduction is what gets compared. A field the instance stamped on is not
   a field this run has an opinion about.

**Why the test did not see it.** The fake instance returned exactly what our own
code produces, that is to say what we write rather than what the platform
returns. A double that agrees with the code about a shape neither of them owns
is not testing anything. Two tests now hold both halves together, so a field
added on one side and forgotten on the other cannot fall outside the comparison.

## Publishing consumes what was built, it does not rebuild

The publishing job downloads what the generation job produced in the same run.
One definition of "what this commit produces", and what reaches the platform is
what was produced once rather than a second build nobody looked at.

**Not building is not the same as not checking.** A bundle names its own package
identifier, and its file name carries no version. A version incremented without
regenerating therefore leaves the old bundle exactly where the new one goes, and
nothing downstream catches it: the listing is queried on the **new** identifier,
answers "not published", and the **old** bundle goes up under the version it was
built with. The run then prints a success naming neither the version requested
nor the one it just published.

The two are confronted before anything is uploaded. This is the step with no
undo, so the order of the commands is not something we rely on. We look.

## No coordinate has a default

Every address and credential is read from the environment with no fallback. A
default endpoint is one deployment's address baked into every other, and unlike a
wrong registry, **a wrong instance is caught by nothing downstream**. It accepts
the package, and nobody finds out.

Missing values are reported together, so a fresh deployment is fixed in one pass.
The address of the instance and the identity to publish as are demanded together.
The webhook's address and shared secret are demanded only by the target that
needs them, because publishing a questionnaire should not require knowing where
documents will one day be sent.

The webhook's address carries its **route**, and publication only appends the
project to it. Leaving the route off writes a service that posts to the
container root, which is a Submit button that returns 404, written over one that
worked, on a value nothing downstream can check. That trap was set and avoided
by reading the stored service before writing it.
