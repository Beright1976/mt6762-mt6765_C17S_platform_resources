# A Controlled Methodology for AI-Assisted Device-Level Reverse Engineering and Security Research

### The C17S / LOKMAT 5 MAX Independent Research Project

**Author:** Albert Pittman (beright1976)
**Document class:** Methodology paper — public release
**Subject device:** LOKMAT APPLLP 5 MAX (C17S), MT6762V/WB silicon on MT6765 BSP, Android 10

---

## Authors thesis

An independent researcher with no formal development background produced a
device-complete, mathematically verifiable reverse-engineering and security
disclosure package for a hostile MediaTek architecture by strictly treating
chat-interface AI as a constrained, deterministic instrument rather than an
autonomous agent. Leveraging thirty years of physical engineering
discipline as a former flooring contractor, combined with formal
education in network infrastructure and security, the operator abandoned
the open-source community's standard "compile and pray" guesswork in
favor of absolute, byte-level forensic accounting. By explicitly denying
the AI swarm—comprising Gemini, Claude, and ChatGPT—any interpretive 
autonomy, the methodology forced the models into a rigid framework of
micro-batch command generation where tool output served only as a lead 
and the raw binary remained the singular proof. This obsessive, 
human-in-the-loop verification gate successfully neutralized large language
model hallucination and context degradation, ultimately unmasking a deeply
obfuscated Vendor ODM control plane and proving that industry-eclipsing 
rigor is born from methodological constraint, not intrinsic model or 
tooling capability.

---

## Abstract

This paper describes the methodology used to produce a complete hardware and
software forensic reverse-engineering of a modern Android device — including a
from-source-outward TWRP build protocol, a fully decrypting recovery port, a
security assessment identifying a malicious-capable ODM data-collection
architecture, and a proof-of-concept clean ROM — using no tools beyond a
standard chat interface to several large language models, over roughly eight
months of daily work.

The result was produced by someone with no formal software development
background and no prior device-level reverse-engineering experience. That is
stated plainly because it is the central claim of this paper: the rigor of the
work did not come from advanced tooling, agentic automation, or elevated model
capability. It came from a methodology — a disciplined, logic-governed
interaction model between a human operator and one or more AI systems, in
which the AI is treated as a constrained research instrument rather than an
autonomous collaborator.

This document exists to make that methodology explicit, reproducible, and
available to anyone doing comparable work — independent researchers,
students, and professionals alike — regardless of which AI system they use or
how technically advanced that system is.

---

## 1. Introduction

Reverse engineering an undocumented commercial Android device is, by nature,
an exercise in resolving uncertainty from incomplete evidence. Every claim
made about the device has to be traceable back to something that can actually
be measured — a byte in a binary, a line in a live process table, a register
read from the running silicon. There is no specification to consult. There is
only the device, and what can be proven about it.

Large language models are, by their nature, tools that generalize. They are
trained to produce the statistically likely continuation of a pattern, to
fill gaps plausibly, and to be agreeable and helpful. Those are exactly the
properties that make an unsupervised AI dangerous to use on a nonstandard
device, because a nonstandard device is precisely the place where the
statistically likely answer is wrong. An MT6762 physical chip reporting
itself under an MT6765 board string is not a typo to be smoothed over. It is
the finding.

This paper is not an argument that AI should not be used for this kind of
work. It is the opposite — a demonstration that AI, used correctly, is a
genuine force multiplier for a single independent researcher, sufficient to
produce work at a depth and rigor that would ordinarily require a funded
team. The argument is about *how* it must be used. Get the interaction model
wrong, and the tool actively works against the goal. Get it right, and a
person with no formal training can out-document a commercial lab.

---

## 2. The Governing Principle: Logic Before Everything

Every rule in this methodology, without exception, exists because it is the
logical consequence of the work's actual goal. None of it is procedure for
its own sake, and none of it is borrowed convention adopted because it looked
rigorous. The test applied to every decision — from how a file is named to
how a contested claim is closed — is the same: *what does the evidence
actually support, and what is the most direct, reproducible way to establish
that?*

This shows up in small ways as much as large ones. Files, partitions, and
findings are named for exactly what they are, using the vocabulary the
device itself already uses — a partition is called what the device's own
partition table calls it. Nothing is renamed for the sake of a cleaner-looking
convention, because a cleaner-looking convention that obscures the object's
actual identity is a step backward, not forward. A reader who has never seen
this project before should be able to open a directory and know, from the
name alone, exactly what they are looking at and where it came from. That is
not a stylistic preference; it is a requirement of reproducibility, and
reproducibility is a requirement of the work being evidence rather than
narrative.

The same logic governs the larger architecture of the methodology described
below. Evidence is ranked by how directly it can answer a question, not by
convenience. Contradictions are preserved rather than reconciled, because
reconciling two disagreeing measurements without resolving which one is
correct replaces evidence with a guess dressed as a conclusion. An AI's
tendency to normalize, summarize, and smooth is treated as a specific,
named risk to be actively defended against — not because AI is untrustworthy
in general, but because smoothing is *logically* the wrong operation to
perform on evidence whose entire value is in its exactness.

Nothing in this methodology asks the reader to trust an authority, a
convention, or a tool's reputation. It asks the reader to trust the chain of
measurement that produced each claim — and to be able to reproduce that
chain themselves.

---

## 3. Why the Interaction Model Is Chat, Not Autonomy

A significant part of this methodology is a deliberate rejection of the
direction most AI-assisted engineering tooling has moved in: agentic
execution, direct filesystem and shell access, and multi-step autonomous
task completion. This was not a limitation worked around. It was a decision,
made after direct comparison.

Tooling that gives an AI system direct, autonomous access to a filesystem,
shell, or device — whether through a CLI agent or a desktop application with
host integration — optimizes for throughput. It reduces the number of times a
human has to approve a step, and it lets the system decide for itself what
information it needs and go get it. For most software engineering tasks,
that is a reasonable trade. For evidentiary reverse-engineering work, it is
the wrong trade, for a specific and demonstrable reason: an agent that reads
what it thinks it needs, on a device whose actual structure diverges from
every normal assumption, will read the *wrong* thing with total confidence,
and nothing in the interaction forces a human checkpoint before that
information enters the working context and starts shaping every conclusion
that follows.

The chat interface, by contrast, forces a checkpoint before every single
action. A command batch is proposed. The operator reads it, understands
exactly what it will do, and decides whether to run it. The output comes
back to the operator first, not to the model. The operator can inspect it,
catch a malformed capture, catch a tool defect, catch an assumption baked
into the command itself — before any of it becomes something the model
reasons from. This is not a slower version of agentic work. It is a
structurally different relationship: the model proposes, the human verifies
and executes, the human decides what re-enters the conversation. Every
step is a deliberate act, not a delegated one.

This also has a second, less obvious benefit: it keeps the entire evidentiary
record inside a reviewable conversation. A chat session is not just a
transcript — for this kind of work, it functions as an auditable log of
exactly what was asked, what was run, what came back, and what was
concluded from it, in order, without hidden intermediate steps. That property
turned out to be valuable well beyond the original session: entire
conversations were later handed to a different model for independent
verification, which is not something a sequence of autonomous tool calls
inside an agent session naturally supports in the same way.

None of this requires an advanced model. It requires a disciplined operator
and a bounded interaction pattern. That is the reproducible part.

---

## 4. Core Epistemic Principles

The methodology rests on a small number of principles, each of which exists
to prevent a specific, observed failure mode of AI-assisted analysis. They
are stated here independently of the project's specific vocabulary so they
can be applied to any comparable research effort.

### 4.1 Evidence authority is stratified, and never inverted

Not all evidence answers a question equally well, and the methodology ranks
sources explicitly rather than treating anything that looks authoritative as
equally trustworthy:

1. The live, running device — the highest authority for any question about
   current runtime state, because nothing else can answer what the running
   system actually received, computed, or is doing right now.
2. Raw binary images captured directly from the device.
3. Correctly extracted filesystem trees, treated as a convenience witness for
   content, not a replacement for the raw image when the raw image can
   answer the same question directly.
4. Static analysis tied back to a specific source object.
5. Prior documentation and databases — compiled *output*, never a
   verification anchor in their own right.

The critical rule is not the ranking itself but the direction it must never
run: a database entry, however well-written, is never used to prove a
claim about the raw device. It is only ever a record of what a prior pass
already proved, checkable and correctable against the device at any time.
Inverting this — trusting the summary over the source — is the single
fastest way rigor quietly degrades in any documentation-heavy project,
human-run or AI-assisted.

### 4.2 Witness separation

A declaration in a configuration file is not the same fact as that
declaration being active at runtime. A device-tree node existing is not the
same fact as the hardware it describes being physically populated. A stored
driver name is not the same fact as that driver currently running. A
function boundary a disassembler drew is not the same fact as a byte-proven
function boundary.

These distinctions sound pedantic until the device in question routinely
violates every one of them — declaring hardware paths that were never
populated, carrying driver references for components that do not exist on
this board revision, exposing sysfs nodes for subsystems with no consumer.
Collapsing "declared" into "active" is exactly how a reverse-engineering
effort ends up documenting the manufacturer's intentions instead of the
device's actual behavior. The methodology treats these as genuinely separate
classes of evidence that are never silently merged, and requires that when
two witnesses disagree, both statements are recorded rather than one being
quietly discarded.

### 4.3 Contradictions are preserved, not resolved into a narrative

This is the principle most directly aimed at how a language model naturally
behaves. Left unconstrained, a model presented with two disagreeing pieces of
evidence will tend to produce a single, coherent, plausible-sounding account
that reconciles them — because a coherent narrative is a more helpful-looking
answer than an unresolved tension. That instinct is precisely backward for
this work. An unresolved contradiction between two measurements is itself a
finding, and it is very often the finding that leads somewhere real. The
methodology requires that contradictions be recorded exactly as observed,
with both sides stated, until an actual measurement — not a plausible
account — resolves one side or the other.

### 4.4 A hash sum proves identity of the exact bytes hashed. Nothing else.

This rule is stated in absolute terms because it is one of the most common
places rigor is faked without anyone noticing. A matching hash between two
files proves those two specific byte sequences are identical. It proves
nothing about what either of them does, how they are wired into the system,
what depends on them, or what else might be nearby. A reverse-engineering
record that documents a component by citing its hash and calling it
identified has documented an identifier, not a mechanism. The requirement
throughout this project is that every component's record states what it is,
what it does, and how it does it — the actual mechanism — with a hash used,
at most, as a passive archival marker, never as a substitute for that
description.

### 4.5 Forced rediscovery

When a prior pass of analysis exists for an area of the device, it is
tempting — and typical practice — to use that prior work as a checklist:
confirm what it already found, and move on. This methodology deliberately
forbids that. Each expansion pass is required to search the actual scope
fresh, as though the prior documentation did not exist, specifically so that
anything sitting immediately next to, underneath, or inside an
already-documented item is actually found, rather than skipped because that
area was marked "covered." Several of the project's most significant
findings — including the parallel data-collection frameworks documented in
the security assessment — were located precisely because an area already
believed to be closed was searched again from nothing rather than
confirmed against what was already written down.

### 4.6 Human final authority, explicitly scoped

The AI's role in this methodology is bounded deliberately: acquisition
design, correlation across large volumes of evidence, mathematical and
structural verification, and documentation under the explicit constraints
above. Judgment calls about severity, interpretation, and conclusion are
retained by the human operator. This is not a disclaimer added for legal
comfort — it reflects a real division of labor that this methodology
depends on. The AI is extremely good at holding and cross-referencing more
raw evidence than a human can keep in working memory at once. It is not the
authority on what that evidence means, and it is never treated as one.

---

## 5. Multi-Model Collaboration as Structured Cross-Verification

This project did not rely on a single AI system, and the reason is not
redundancy for its own sake — it is that different systems have measurably
different strengths, and using them interchangeably wastes that difference.

Broader, retrieval-heavy research and large-context document correlation
were handled by systems built for exactly that — able to hold an entire
project's accumulated documentation in context and compile evidence across
it on demand. High-volume, lower-stakes grinding work — generating and
checking large batches of candidate findings — was assigned to models suited
to throughput. Technical judgment, contradiction detection, and final
verification against raw evidence were reserved for the model best suited to
that specific task.

Critically, this is not the same as simply asking several chatbots the same
question and picking a favorite answer. It is role-separated agency: each
system works a bounded surface, one model's output is never treated as
another model's assumed premise, and disagreement between models is treated
the same way disagreement between any two evidence witnesses is treated —
recorded, not smoothed over. The human operator remains the single point
where all of it is reconciled, verified against the actual device, and
either accepted or rejected.

---

## 6. Evidence Architecture

A methodology is only as good as the record it produces, and this project
uses a specific, consistent architecture for that record — designed so that
anyone with only a single component's directory and the raw device data
could independently verify every claim made about it.

**Target-based organization.** The device is decomposed into individual
targets — partitions, binaries, subsystems — each resolved on its own, one
at a time, to a defined completion standard before the next is opened. Each
target's directory is self-contained: raw evidence, derived artifacts, and a
structural record live together, without requiring the reader to chase
context across the rest of the project.

**The closure log.** Every substantive target carries a permanent,
append-only ledger recording the state of every finding related to that
target — not the evidence itself, which lives in the target's own files, but
the current status of each claim, with an explicit vocabulary: closed,
open with an unresolved boundary and exactly what evidence would close it,
narrowed, corrected, superseded, retired with a stated reason, or an
anomaly retained exactly as observed. Vague status language — "further work
needed," "mostly resolved" — is not permitted; every open item names the
specific evidence or procedure that would close it.

**Routing by subject, not by discovery session.** A finding belongs to the
target it is *about*, never to the target that happened to be under
analysis when it was discovered. If work on one component produces evidence
that closes, corrects, or narrows a claim made about a different, already
documented component, that update is written into the *original* component's
record, not appended somewhere more convenient. This single rule is what
keeps a large, long-running project coherent across many sessions and many
months — without it, later corrections accumulate in whatever document was
open at the time, and no single record stays trustworthy on its own.

**No document is deleted or silently edited away.** A mistake, once
recorded, is not erased. It is corrected in place, with the correction
itself recorded as a correction — preserving the full history of how a
conclusion was reached, not just the final state.

---

## 7. Command and Session Discipline

The operational rhythm of every session follows a small number of fixed
constraints, applied without exception:

- Commands are proposed in small, direct, individually inspectable batches —
  never long chained pipelines, never scripts run without the operator
  having read and understood every step in advance. If a genuinely more
  complex operation is warranted, the model states why and the operator
  decides, rather than the model quietly reaching for automation as a
  convenience.
- Every output is reviewed by the operator before it is treated as
  established. A malformed or incomplete capture is stopped and resolved
  before continuing — it is never patched around or built on top of.
- Files are never modified on the operator's own system without explicit,
  specific direction for that exact action. Reading is unrestricted;
  writing is deliberate.
- The model is required to read source documents in full, not sample or
  search them for what it assumes it needs — a document handed over is read
  end to end, because sampling a document and then making claims about its
  contents is exactly the kind of unverified inference this methodology
  exists to prevent.
- Written output is checked back against its source before being accepted.
  Writing something down is not the same act as verifying it, and multiple
  real errors in this project's history were caught only by that separate
  read-back step — never by the act of writing itself.

None of this is exotic. It is the same discipline a careful human researcher
would apply to their own work, made explicit and applied consistently to an
AI collaborator as well.

---

## 8. Catalogued Failure Modes

A methodology is more credible when it names its actual failures rather than
only its ideals. The following are recurring, observed failure modes of
AI-assisted analysis, each with the specific countermeasure this project
adopted in response.

**Premature summarization.** A model asked to characterize a large body of
evidence will tend to summarize before every relevant data point has
actually been examined, presenting the summary's confidence as though it
reflected complete coverage. Countermeasure: explicit, staged review with
required full traversal before any characterization is accepted.

**Inferred platform norms overwriting device-specific evidence.** A model's
training data is saturated with "how Android devices normally work," and it
will default to that norm unless actively prevented from doing so — even
when the device in front of it is demonstrably nonstandard. Countermeasure:
every claim is required to be sourced to a measurement made on *this*
device, with generic platform knowledge explicitly excluded as evidence.

**Silent reclassification of verified facts.** Across a long project, a
fact correctly established early can be restated slightly differently in
later passes until it drifts from its original, verified form — without any
single restatement looking wrong in isolation. Countermeasure: the closure
log's append-only, sourced-to-a-specific-measurement structure, which makes
drift visible by forcing every restatement to cite its own evidence rather
than inherit authority from an earlier claim.

**Batched commands hiding acquisition failures.** A failed or partial
capture, buried inside a larger automated sequence, can look identical to a
successful one unless each step's output is individually inspected.
Countermeasure: the small-batch, human-reviewed command discipline described
above.

**Helpful normalization destroying real anomalies.** A model's instinct to
present clean, tidy, reassuring output will — left unchecked — quietly
smooth over exactly the irregularities that matter most in reverse
engineering. Countermeasure: anomalies, discrepancies, and negative results
are explicitly protected categories that must be preserved verbatim, never
condensed for readability.

**One model's unsupported statement propagating as apparent fact.** Once a
claim is written into a working document, later sessions — including
sessions with a different model — tend to treat it as established simply
because it is already there. Countermeasure: prior documentation is
explicitly excluded as proof of a raw-evidence claim; only the device itself
proves a device-level claim.

---

## 9. Severity Framing as an Extension of the Same Discipline

The same logical discipline that governs technical claims governs security
claims. Structural evidence can establish that an architecture is
*capable* of a given behavior — broad, unguarded privilege; covert data
collection paths; multiple independent, obfuscated frameworks performing
overlapping surveillance-adjacent functions. Structural evidence alone
cannot establish *intent*. This project's findings are therefore stated in
capability terms — "malicious-capable architecture" — and never as a claim
of confirmed malicious intent or confirmed espionage, because that
distinction is not a legal hedge but the same evidentiary standard applied
everywhere else in the methodology: a claim is stated exactly as strongly
as the evidence supports it, and no further.

---

## 10. A Reproducibility Guide

The specific device, findings, and tooling in this project are not
required to apply this methodology. What is required is the discipline
described above, which reduces to a small number of concrete, adoptable
practices:

1. **Rank your evidence sources explicitly, in writing, before you start —**
   and never let a summary or a prior conclusion outrank the primary
   source it was derived from.
2. **Use a chat interface, or an equivalent bounded interaction model, for
   evidentiary work — not an autonomous agent.** Every step the AI proposes
   should pass through your review before it executes, and every result
   should pass through your review before it shapes the next question.
3. **Work in small, fully inspected batches.** If you cannot read and
   understand a batch of proposed actions before approving it, the batch is
   too large.
4. **Record contradictions instead of resolving them prematurely.** If two
   pieces of evidence disagree, write down that they disagree. Resolve it
   only when a further measurement actually resolves it.
5. **Separate what a source declares from what is actually active or
   present.** Treat these as different classes of evidence at all times.
6. **Re-derive, don't just confirm.** When you return to an area you have
   already documented, search it again as though your prior notes do not
   exist, and only then compare.
7. **Never let a hash, an identifier, or a label stand in for describing
   what something actually does.**
8. **Keep a permanent, append-only, explicitly-vocabularied record of the
   state of every finding**, and route every correction to the place it is
   about, not the place it was discovered.
9. **State every conclusion — technical or otherwise — at exactly the
   strength the evidence supports, and no further.**
10. **If you use more than one AI system, give them genuinely different
    roles rather than asking the same question of each, and treat
    disagreement between them as a signal, not noise to average away.**

None of this requires a specific model, a specific vendor, or a specific
tier of AI capability. It requires an operator willing to do the verifying
work the methodology assigns to a human — which is, in the end, the actual
subject of this paper.

---

## 11. Scope and Honest Limits of This Methodology

This methodology is deliberately slow, and it is not the right approach for
every kind of work. It optimizes for provenance and reproducibility over
throughput, and it assumes the researcher is willing to personally review
every step rather than delegate that review to the tool. For well-understood,
standard hardware where established conventions already resolve most
questions, or for work where speed genuinely matters more than an
independently reproducible evidentiary chain, a more automated approach is
a reasonable and correct choice. This methodology is written for the
opposite case: nonstandard, undocumented, or adversarially obscured systems,
where the cost of a plausible-but-wrong conclusion is high and the
tolerance for unverified inference is close to zero.

---

## 12. Closing

The result of applying this methodology for eight months, daily, to a single
undocumented device is a complete hardware and software forensic record, a
working decryption and recovery solution for a device family with no prior
public support, a documented and disclosed security architecture assessment
filed through proper CVE and CISA channels, and a proof-of-concept clean
replacement build — produced entirely through chat-interface interaction
with AI systems, by a researcher with no formal background in the field.

That outcome is the argument. The rigor did not come from the tools. It came
from treating every claim as something that has to be earned from evidence,
refusing to let an AI system's fluency substitute for that evidence, and
applying that standard with total consistency for as long as the work took.
Anyone willing to hold themselves to the same standard can reproduce it,
regardless of which AI system they use to do it.

---

*This paper accompanies the full C17S / LOKMAT 5 MAX research release,
including the hardware and software forensic databases, the TWRP build
protocol, the security assessment, and the associated evidence resources.*
