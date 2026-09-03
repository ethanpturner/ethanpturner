## Claimed, or verified

A security claim should be a checkable artifact rather than an assertion. These projects apply that
to four parts of the AI stack, because in each of them the ecosystem currently transcribes claims
and calls the result verification.

- Model signing attests **bytes**, and says nothing about **lineage**.
- AI-BOM generators read a model card and report what it **claims**.
- Vector databases enforce whatever tenant tag they were **handed**, never whether it is **true**.
- Authorization engines answer *is this principal allowed to call this tool* — and during a
  confused-deputy retrieval, the honest answer is yes.

The common shape is a system that cannot distinguish *this was checked and holds* from *nobody
looked*. Every project here refuses to collapse those two, which is why each one carries a
three-valued verdict — `verified`, `contradicted`, `unverifiable` — and never a boolean.

| | Domain | Status |
|---|---|---|
| **[trace](https://github.com/ethanpturner/trace)** | Security architecture review | Pipeline runs end to end; evaluation harness with authored truth sets |
| **[whence](https://github.com/ethanpturner/whence)** | Model supply chain | Phase one runs — resolution and CycloneDX ML-BOM |
| **[tearline](https://github.com/ethanpturner/tearline)** | Retrieval entitlements | Design and benchmark corpus; implementation not started |
| **attestrun** | Evaluation attestation | Not started |

### trace

Reads architecture documentation and produces a security assessment where every conclusion traces
to a passage in a source document. Six model-assisted agents, each behind a deterministic validator,
with two human approval checkpoints that are graph nodes rather than configurable options.

Its founding rule is that **missing documentation is never proof of a vulnerability**. A finding
means evidence supports a weakness; a documentation gap means it could not be determined whether a
control exists. Collapsing those two is the failure the project exists to avoid, and it is where
everything below inherits from.

### whence

Resolves a published model's dependency graph and records, per edge, whether the relationship was
**asserted** by the publisher or **established** by the tool. Nodes pin to a revision digest rather
than a name, because a name is an assertion about the present: `benchmarks/transferred-namespace`
captures a live case where a widely-declared base reference redirects into an organization
controlled by someone else, and file requests under the old path are served from the new namespace
with no error.

Every edge it currently emits is `unverifiable`. That is the finding, not a defect — cards name a
base and stop, so resolution establishes that the named artifact exists and can be pinned, never
that the derivation happened.

### tearline

Verifies that a retrieval index's entitlements match the source system's, that they have not drifted
since ingestion, and that retrieval under one identity never returns what only another may see.

OWASP's RAG Security Cheat Sheet prescribes per-chunk access-control metadata, signed source
attribution, and regular cross-tenant testing, and names no tool for any of them.

It measures **under-retrieval** alongside leaks. Where filtering runs after an approximate
nearest-neighbour scan, a selective policy can return nothing while matching content exists:
confidentiality holds, completeness fails silently, and the generation step answers anyway from
parametric memory. A leak-only verifier scores that system as perfectly secure.

### How they are built

Each carries a numbered decision log where every entry is Accepted or Rejected and none is Proposed;
benchmark scenarios registered in a file rather than discovered by scanning directories; authored
truth sets the tool never sees, including a negative set so abstention is measured rather than
assumed; and a dated journal recording the reasoning rather than the diff.

Design documents come before code, and they are checked rather than trusted. Authoring `whence`'s
truth sets found four things its design documents could not, and the first run of the resolver
surfaced a contradiction between two of a scenario's own expected files that had been invisible
through five sessions of design work.
