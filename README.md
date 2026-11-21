# Reproducible AI-Assisted Research Packets

Reproducibility is not a screenshot of a result and not a dependency lockfile alone. A reviewer needs to know **which claim was made, which immutable inputs and code support it, which environment and procedure produced the observation, where AI assistance entered, what varied across runs, and what remains unavailable or unproven**.

This guide defines a research packet around that chain. It does not promise that a result is correct merely because it can be rerun. It also does not require publishing data that participants, licenses, contracts, or law require you to protect.

The central artifact is [`reproduction-manifest.yaml`](assets/reproduction-manifest.yaml). It binds claims to evidence and distinguishes `NOT_RUN`, `REPRODUCED`, `NOT_REPRODUCED`, `PARTIAL`, and `INDETERMINATE` without turning missing evidence into a pass.

## The claim ladder

### Level 1 — statement

Write a bounded claim with population, task, metric, comparison, uncertainty, and conditions. “Model A is better” is not reproducible. A useful statement names the dataset/version, evaluation unit, model and inference configuration, metric implementation, seed/repetition policy, and scope where the observation may apply.

### Level 2 — evidence identity

Assign immutable or versioned identities to data, code, environment definitions, prompts, model artifacts, and result files. A mutable branch or “latest” URL is discovery metadata, not an identity. Record licenses, data governance, and access restrictions beside the identity.

[DataLad](https://handbook.datalad.org/en/latest/) and [DVC](https://dvc.org/doc/use-cases/versioning-data-and-model-files) document approaches to data/version tracking. They do not automatically establish consent, representativeness, or a right to redistribute the data.

### Level 3 — procedure

Describe acquisition, preprocessing, exclusions, prompt or agent behavior, training/inference, metric computation, and expected resource bounds. [ReproZip](https://github.com/VIDA-NYU/reprozip) is a reference for capturing computational environments. Environment capture does not replace a readable protocol or prove the captured run had no external dependency.

### Level 4 — observation

Keep raw outputs, run events, failures, resource use, and transformations. [MLflow Tracking](https://mlflow.org/docs/latest/ml/tracking/) documents experiment tracking concepts. A tracking server does not determine whether a metric is valid or whether runs are comparable.

### Level 5 — interpretation

Separate computed observations from author interpretation. State alternative explanations, known confounders, failed runs, sensitivity checks, and whether an independent person reproduced the result. Do not ask an AI system to grade its own unsupported output and then label the score independent evidence.

## Build a portable research object

[RO-Crate 1.2](https://www.researchobject.org/ro-crate/specification/1.2/) defines a method for packaging research data with structured metadata. Use a crate or equivalent manifest to link, not blur, these objects:

- `claims`: exact text and scope;
- `inputs`: data, prompts, documents, model weights, calibration material;
- `software`: repositories, commits, containers, compilers, drivers, scripts;
- `procedure`: ordered protocol and deviations;
- `runs`: configuration, seed, timestamps, resources, state, raw output;
- `results`: derived tables/figures and the code that produced them;
- `people/organizations`: roles, review, consent/governance ownership;
- `licenses/access`: what may be shared and under which terms;
- `archives/citations`: persistent identifiers and retrieval receipts.

The guide recommends one claim-to-evidence edge per material result. That is an author protocol, not an RO-Crate requirement.

## Research packet stack map

The projects below are complementary at several layers; choosing one does not automatically replace the others.

| Layer | Candidate | Primary object | Preserves bytes? | Captures environment/run? | Persistent reference | Selection boundary |
|---|---|---|---|---|---|---|
| Package metadata | RO-Crate | entities and relationships around a research object | links or packages according to implementation | describes them; does not execute the run | identifiers may be linked | use when a navigable metadata graph is the missing layer |
| Dataset/worktree identity | DataLad | datasets composed from Git and annexed content | content availability depends on configured stores | not a full run capture | commit/content identities | useful for distributed datasets and versioned retrieval |
| Pipeline data versions | DVC | files, stages, and pipeline dependencies | bytes live in configured local/remote storage | tracks pipeline declarations and metrics, not an OS execution capsule | hashes plus repository state | useful when code/data pipeline changes must be reviewed together |
| Execution capture | ReproZip | system-call-observed computational environment | captures selected dependencies into its package model | yes, for supported captured execution | package identity must be assigned by the operator | use for a runnable execution capsule; inspect external dependencies |
| Experiment tracking | MLflow | parameters, metrics, artifacts, and runs | artifact retention depends on backend configuration | records runs; does not freeze all external inputs by itself | run/artifact identifiers | useful for comparing declared runs and retaining raw outputs |
| General archive | Zenodo | deposits and records | deposited files are preserved under service policy | no execution semantics | DOI/record identifiers | use for citable release snapshots, not as proof of execution |
| Source archive | Software Heritage | source-code history/content | preserves collected source objects | no experiment execution | SWHIDs | use to reference archived source independently of a forge URL |
| Citation/license metadata | CFF and SPDX | contributors, citation, licenses, relationships | metadata rather than research bytes | no | identifiers/relationships within their models | complements, never replaces, the packet and its governance records |

The table summarizes documented roles only. Backend configuration, version applicability, authentication, storage guarantees, and cross-tool adapters remain `NOT_EVALUATED` until tested.

## Disclose AI assistance at the point of effect

A generic “AI was used” sentence is insufficient. Record where AI affected:

1. literature discovery or triage;
2. translation or summarization;
3. hypothesis or protocol drafting;
4. code generation or modification;
5. data labeling, exclusion, or transformation;
6. model selection/tuning;
7. statistical analysis;
8. figure or prose generation;
9. review, criticism, or citation checking.

For each effect, record system/provider or local runtime, model identifier/version if available, relevant configuration, prompt or instruction hash plus governed storage location, tool access, human reviewer, and resulting diff/artifact. Do not publish sensitive prompts or participant data merely for completeness; document why access is restricted and how an authorized reviewer can inspect it.

[Nature Portfolio’s AI editorial policy](https://www.nature.com/nature-portfolio/editorial-policies/ai) is one publisher policy reference, not a universal rule. Check the target venue, institution, funder, and jurisdiction.

## Archive identity without overstating preservation

- [Zenodo’s API](https://developers.zenodo.org/) supports deposit/record workflows and identifiers.
- [Software Heritage](https://www.softwareheritage.org/save-and-reference-research-software/) documents preserving and referencing source code.
- [Citation File Format](https://github.com/citation-file-format/citation-file-format/blob/main/schema-guide.md) documents software citation metadata.
- [SPDX 3.0](https://spdx.github.io/spdx-spec/v3.0/) provides a specification for software-related metadata and relationships.

A DOI does not prove a claim. An archived repository does not prove the executed code matches it. A citation file does not settle authorship. An SPDX document does not grant rights absent a valid license and provenance chain. Store archive receipts and identifiers, then verify that cited objects resolve to the intended bytes/metadata.

## Independent reproduction protocol

### Packet freeze

The original team freezes a packet and records its checksum. It includes a minimal “first run” path, but does not hide exclusions or failed trials behind that convenience path.

### Cold review

A reviewer who did not author the run reads only the packet, lists ambiguities before contacting the authors, and identifies required protected inputs. This tests the documentation rather than the reviewer’s access to tribal knowledge.

### Controlled execution

The reviewer runs in an authorized isolated environment, verifies input/code identities, records deviations, and preserves raw outputs. Restricted data remains in its governed environment. Synthetic data may test plumbing but cannot reproduce a claim about the protected population.

### Claim comparison

For each claim, report:

- reproduced within the predeclared tolerance;
- not reproduced, with observed difference;
- partially reproduced;
- indeterminate because evidence/access/resources are missing;
- out of scope because the packet does not support the claim.

Do not silently tune until the original number appears. Any correction becomes a new run linked to the failed attempt.

## Variance and negative results

For stochastic systems, one successful run is weak evidence. Predeclare repeats, seeds where meaningful, sampling settings, confidence/uncertainty method, and acceptable deviation. Record unavailable deterministic controls rather than pretending they exist. For hosted models whose exact version can change, freeze request/response evidence and label reproducibility limits.

Negative results and crashes belong in the packet. Excluding them after observation changes the claim and must be justified. Distinguish infrastructure failure, implementation defect, data defect, model variance, and hypothesis failure.

## Ethics, privacy, and legal boundary

For work involving people, the [Belmont Report](https://www.hhs.gov/ohrp/regulations-and-policy/belmont-report/index.html) is a US ethics reference for respect for persons, beneficence, and justice. The [45 CFR 46 landing page](https://www.hhs.gov/ohrp/regulations-and-policy/regulations/45-cfr-46/index.html) identifies the US regulatory framework and additional subparts. These sources do not decide whether a particular activity is regulated research, do not apply universally, and do not replace the responsible institution's ethics and legal review.

- Never fabricate a run, citation, participant, approval, dataset license, or model identity.
- AI-generated text, labels, code, or critique are research inputs until independently checked; they are not evidence by themselves.
- Do not upload confidential, personal, export-controlled, embargoed, or licensed data to an AI service without authorization and a valid governance basis.
- Preserve consent, de-identification, retention, deletion, and access controls; reproducibility does not require public disclosure of protected data.
- Verify citations against the actual source. A plausible title, DOI, quotation, or code reference can still be false.
- Record conflicts of interest and vendor/model dependence.
- Obtain domain and ethics review for human/animal subjects, medicine, safety-critical systems, or consequential policy claims.

## Packet acceptance

Hold the packet when a material claim lacks an evidence edge, a mutable object lacks a frozen identity, AI assistance cannot be traced to its effect, a protected input lacks an access path for authorized review, or the metric cannot be regenerated from retained outputs.

A passing packet means another reviewer has a defined route to test the claim. It does not certify scientific truth, ethical approval, legal compliance, or generalization.

## Source boundary and upkeep

[`sources/sources.json`](sources/sources.json) contains ten precise upstream sources. Each supports a narrow packaging, tracking, archival, citation, metadata, or disclosure role. The claim ladder, status vocabulary, independent protocol, and acceptance rule are author recommendations.

Create a new packet version whenever a claim, data snapshot, code commit, environment, model, prompt/instruction, metric, exclusion, analysis, or disclosure changes. Never rewrite a frozen packet in place.

## License

Original guide and reproduction manifest: CC BY 4.0. Linked standards, policies, documentation, and projects retain their own terms.
