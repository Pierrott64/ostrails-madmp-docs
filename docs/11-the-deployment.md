# The deployment

This section stands on its own. A pilot that wants none of the rules machinery
can still take this: a reproducible Data Stewardship Wizard 4.31 deployment,
configured entirely from the environment, with a submission webhook beside it.

It starts from the official deployment example at its 4.31 release, kept as an
upstream remote so the differences stay visible as a diff rather than as
folklore.

SOCIB runs an instance of it at <https://dsw.priv.socib.es/wizard/>. Note the
path: entering through it rather than through the bare host is the first of the
traps this section passes on.

## What runs

| service | published on the host | role |
|---|---|---|
| client | loopback only | the web interface |
| server | loopback only | the API |
| document worker | none | renders documents |
| submission | none | the maDMP webhook, reached over the compose network |
| database | none | Postgres |
| object storage | loopback only | MinIO, and its console |
| bucket creation | one-shot | creates the bucket, under a profile |

Everything is bound to the loopback interface. Reaching the stack from another
machine means putting a reverse proxy in front of it.

Four services carry health checks, which is what makes bringing the stack up
with a wait mean something: it returns when the stack answers, not when the
containers exist. Measured from no image and no volume, about a minute of
downloads and then twelve seconds. On a machine that already has the images,
twelve seconds.

## Configuration comes from the environment

**This is the single most reusable decision here.** There is no configuration
file for the platform in the repository, and no script that generates one.

The platform resolves every setting from the environment before reading its own
configuration file, and the variable names are the configuration paths in upper
case. The compose file therefore composes the platform's own settings out of one
`.env`, filled by hand, which is the single source of truth.

What that buys:

- **no secrets in the repository.** The upstream example ships a configuration
  file carrying published signing secrets. It is gone
- **one place to look.** Nineteen values, of which eleven carry a working local
  value and eight are empty: two passwords, two signing secrets, and the
  webhook's four
- **each empty value documented where it is empty**, with the command that
  produces it where there is one

The setup script reports every value it found and where it came from, then stops
if one is missing. Secrets are reported as set, never printed. It writes nothing
and can be re-run at will.

Reporting the **origin** of each value matters more than it sounds. Compose lets
the environment win over the file, so a name exported in a shell, or supplied by
whatever runs the containers, reaches them while the file still shows something
else.

## Deploying somewhere else

Three values change, and they are the ones the **browser** resolves. They depend
on where the stack is reached from rather than on where it runs: the API address,
the client address, and the object storage address.

Two paths under one domain let the first two live together behind a reverse
proxy. The third is the awkward one: the browser downloads documents straight
from object storage through a presigned URL, so it needs a public address of its
own. A subdomain is simpler than a path, because the bucket name is already in
the path.

Two situations need a change in the compose file rather than in a value: a
reverse proxy on **another machine**, which means widening the bindings and
letting a firewall take over, and infrastructure that provides a **managed
database or object storage**, which means removing services rather than changing
values.

One trap worth passing on, since it costs an afternoon: **enter through the
client's own path, not through the bare origin**. The client image redirects the
root to an absolute address built from the protocol its own web server listens
on, so behind a TLS terminator the browser is sent to plain HTTP on a host that
only serves HTTPS.

## What differs from upstream, and why

- configuration comes from the environment, and the configuration file is gone
  along with its published signing secrets
- the compose project name is pinned, so container and volume names survive a
  folder rename
- named volumes are enabled. Without them, bringing the stack down destroys the
  database and the bucket
- health checks on the database and on the server, the second replacing one that
  could not report healthy in under five minutes
- the database is not published, and object storage is bound to the loopback
- the bucket is created by a compose service under a profile, replacing a script
  that guessed its network and asked for an image tag that does not exist
- the mailer is commented out, having nothing to process while mail is disabled
- upstream's workflows are removed, since they monitor the platform's own images

## The seeded accounts

The platform seeds three demo accounts whose addresses and password are
published. Removing them is a manual step, and the order matters: log in as one
of them, create your own administrator account, check that it works, and only
then delete the three. Deleting them first locks you out of your own instance
with no way back but the database.

The setup script says so at the end of every run for as long as they answer.
That is all it does about it, deliberately: a deployment script that deletes
accounts is a deployment script that can lock you out.

## The webhook, as an operational component

Its design is in [section 12](12-submission-and-registry.md). What matters here is what it demands of the
infrastructure, and it is short.

- **Four variables, and they are the whole contract**: the shared secret the
  platform sends, a fine-grained token with contents read-write on the registry,
  and the registry's owner and repository.
- **They are read once, at startup.** An incomplete configuration leaves the
  container restarting, and the logs name what is missing. Reading them per
  request would let it answer a health check and fail on the first real
  submission, which is the moment nobody is watching. All four are reported
  together, so a fresh deployment is fixed in one pass rather than one restart
  per variable.
- **The registry variables are not called `GITHUB_*`.** Compose lets a shell
  override the file, and `GITHUB_TOKEN` is a name a developer's shell very often
  already holds. The collision would have the webhook commit with somebody
  else's credentials, silently.
- **Outbound HTTPS to the GitHub API** is the one thing in this deployment that
  reaches outside the host. Everything else talks on the compose network, which
  is why the webhook needs no published port at all.
- **No account name ships in the repository.** The owner and repository
  variables ship empty.
