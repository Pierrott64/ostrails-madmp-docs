# Context

The rules-driven engine described in the rest of this document is one component
of a pilot that started well before it, and that reaches further than it. This
section says where the work comes from, what already existed around it, and where
it is going. Without it, a reader would take the part for the whole.

## Where the work comes from

**CORIS.** The Coastal Ocean Resource Infrastructure System is a UN Ocean Decade
endorsed initiative led by SOCIB. It aims to build a digital platform for
discovering, accessing and assessing coastal ocean data together with the wider
ecosystem of digital research objects that support ocean science, and to provide a
collaborative environment for services within the CoastPredict framework.

**JERICO.** JERICO-S3 and JERICO-DS established the foundations of a digital
ecosystem for the Joint European Research Infrastructure of Coastal Observatories,
so that datasets, metadata, software, standards, best practices and Virtual
Research Environments can be reached from one place. The JERICO Coastal Ocean
Resource Environment, CORE, was built as a prototype of that ecosystem and is
becoming the long term operational infrastructure for JERICO-RI.

**Blue-Cloud.** The JERICO-CORE Virtual Research Environment is supported jointly
by JERICO and Blue-Cloud, on the D4Science infrastructure, providing Virtual Labs
where researchers reach data, services and analytical tools. Within it, SOCIB has
developed services that record the digital assets taking part in data workflows
and evaluate whether datasets exist as their Data Management Plan said they would.

**IDEMar.** SOCIB has been writing Data Management Plans since 2021 to document
and coordinate its digital platforms. They began as ordinary documents and became,
through IDEMar, reusable templates promoting harmonised practices at national
level.

That trajectory is what produced the problem this pilot addresses. Templates
harmonise what people write, and they do nothing about what happens next: whether
the described data flow actually ran, whether the resulting datasets exist, and
whether their metadata are FAIR. Answering that needs descriptions a machine can
read.

**OSTrails** takes on exactly this, along three dimensions: turning Data
Management Plans into machine-actionable DMPs that evolve through the research
lifecycle, integrating Scientific Knowledge Graphs so that relationships between
research outputs become explicit, and establishing interoperable ways of assessing
FAIRness. Together they form the Plan, Track and Assess lifecycle that this
documentation is organised around.

## The SOCIB pilot

SOCIB contributes the ENVRI and JERICO thematic pilot, demonstrating these
concepts inside an operational coastal observing infrastructure.

The use case is glider observations, on the Canales endurance line. Operating for
more than a decade, it produces a rich ecosystem of interconnected digital
objects: datasets, metadata records, software, workflows, publications and
documentation. It was chosen because that history makes it well understood rather
than because it is simple.

The intention is to extend the approach to other SOCIB observing systems, then to
the OceanGliders community, and eventually to broader JERICO-RI data flows.

## What already existed around this work

The rules-driven engine did not arrive on empty ground, and the tools around it
are the Track and Assess half of the pilot.

**An earlier maDMP service.** Before the work described here, a maDMP was already
produced on demand: a service reads the data and management API, fills a template,
and returns a plan. The engine described in this document replaces that template
with a derivation from declarative rules, and it is meant to leave the consumers
of the plan undisturbed.

**Three MCP servers**, so that the pilot's information is reachable by tools and
agents rather than only by people. They expose, respectively, information drawn
from the maDMPs, the file system, and the THREDDS data server.

| | |
|---|---|
| maDMPs | `https://mcp_dmp_meditto.priv.socib.es/mcp` |
| file system | `https://mcp_fs_meditto.priv.socib.es/mcp` |
| data server | `https://mcp_ds_meditto.priv.socib.es/mcp` |

These are MCP endpoints rather than web pages. A browser will be refused, a
client sending the right headers will not.

**A quality control of the data.** Written in Python at SOCIB, it assesses
datasets and their metadata. It runs outside the maDMP
pipeline today, and the intention is to fold it into the checks a submitted
plan faces. It is a different question from the one the rules answer, and
[section 14](14-what-is-next.md) keeps the two apart.

**An agent and a monitoring interface.** An agent answers questions about the data
flow asked through the institution's chat, and an interface shows, per project,
which data are available and which are missing. The purpose is to give glider
operators a view of the state of their data, and in time to let them process it
without logging into servers.

**Status, stated plainly.** Nothing in the pilot is operational yet. Parts are
deployed and being tested, and that includes everything described in this
document.

## Already reused elsewhere, as a proof of concept

The models developed for this pilot are already the technical foundation of work
beyond it, and it is worth being exact about what that work is.

In Blue-Cloud2026, SOCIB contributes a federated service drawing on the three
OSTrails interoperability frameworks: SKG-IF for representing and linking digital
objects, maDMP-IF for describing plans and connecting them to concrete objects and
data flows, and FAIR-IF for assessing FAIRness and process compliance. It is
hosted at SOCIB and federated into the Blue-Cloud Virtual Research Environment.

**It is a proof of concept, and should not be read as an operational service
implementing all three.** Its own deliverable is careful about this: the service is
made available *initially* through the JERICO-CORE Virtual Lab *for testing and
validation*, it is described as a *prototype implementation* of how a federated
service can be embedded in a Virtual Lab, and as a reusable pattern other Labs
could adopt *as it matures and becomes more broadly available*. None of the three
frameworks is finished there.

What this document takes from it is narrower, and it is an attribution rather than
a claim. That deliverable records that the service rests on the OSTrails SKG and
maDMP data models "originally developed to support the ENVRI/JERICO OSTrails
thematic pilot for glider data management", which is the pilot described here. Its
first use case assesses whether data from JERICO-S3 Transnational Access projects
followed the data management plan, starting with glider missions operated by
SOCIB.

Reference: Blue-Cloud2026, D5.5, *Blue-Cloud VRE federated infrastructures, 2nd
release*, section 2.3.

## Where it goes

OSTrails ends. The outcomes are folded into CORIS, which has no funding of its own
and serves the broader coastal ocean community, so that what the project produced
remains available to people who were never part of it. That is the sustainability
path for everything in this document, and it is the reason the material is written
to be read by another pilot rather than only by us.
