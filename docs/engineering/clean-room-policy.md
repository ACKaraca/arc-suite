# Clean-room policy

What clean room means for Arc, which inputs contributors may and may not use, how behavior derived
from observation is recorded, and what happens when something is not clean.

This policy is engineering practice adopted by the project. It is not legal advice.

## What clean room means here

Every part of Arc is created by its contributors from sources they are entitled to use, in their own
words and code. Arc knows what a function must do from published specifications, public
documentation read for understanding, and observation of how software behaves from the outside. It
never knows how another product implements that function. The result must be independently
written, and its origin must be demonstrable from the project's own records.

## Permitted inputs

| Input | Conditions |
|---|---|
| Published specifications and standards | Used for the format or protocol they specify, within their license terms (for example IFC, STEP, DXF reference, glTF, OpenUSD, LandXML, BCF, PDF) |
| Public documentation, read for understanding | Read to understand what a function does; never copied, paraphrased closely, or used as a structure for Arc documents |
| Observed behavior of legitimately obtained software | Using a product's user interface with self-made inputs and recording what it does: results, not internals |
| Public demonstrations | Recorded talks, tutorials and demonstrations watched to understand workflows and results |
| Original reasoning | Domain knowledge, textbooks, academic papers, engineering practice |
| Contributor-owned sample files | Files the contributor created and owns, admitted under the [corpus rules](#corpus-admission) |
| Libraries on the dependency allowlist | Used through their public APIs under their licenses |

## Prohibited inputs

| Input | Why |
|---|---|
| Source code of other products, including open-source code under licenses not on the allowlist | Copying risk and license contamination; applies to reading, searching, diffing and quoting, including excerpts in forums or generated answers |
| Decompiled or disassembled code, memory dumps and debugger traces of other products | Reverse engineering of implementation |
| Reverse engineering of binaries or undocumented file formats, including byte-level analysis of files written by a product without a published specification | Legal and contractual risk |
| Copied documentation text, help pages, dialog text, option wording, menu structures and default-value lists | Copyright in expression |
| Icons, fonts, hatch patterns, line types, symbols, templates, materials, textures and other content files of other products | Copyright and license terms of those files |
| Vendor sample files and files extracted from installed products | License terms; ownership unclear |
| Material under licenses incompatible with the [dependency policy](dependency-policy.md) | Cannot be distributed under Apache-2.0 |
| Any earlier implementation known to be unclean | Taints whatever is derived from it |

Running another product's binary as a black box to observe results is permitted only where its
license allows that use, and only through its normal user interface or documented command line.

## Derivation records

Any file format, protocol or behavior that Arc derives from observation rather than from a published
specification gets a derivation record, committed under `clean-room/` in the crate that implements it.

| Record | Content | Rule |
|---|---|---|
| `assumptions.md` | What the contributors believe before consulting any source, each item marked unverified | Written first, committed once, never edited afterwards |
| `source-log.md` | Every source used: published documents, observation sessions (software, version, inputs, what was observed), searches run; any accidental exposure to prohibited material | Appended as work proceeds |
| `specification.md` | The derived behavior or format, complete, in original words, with tables as data | Its commit is the freeze point; later changes are new, dated revisions with the reason |
| `verification.md` | Tests of the frozen specification against fresh observations or samples not used for derivation, with results, including failures | Written after the freeze, with no tuning of the specification to pass |

Implementation follows the frozen specification. Where practical, the person who observes and the
person who implements are different people, and the implementer works only from
`specification.md`.

## Naming of features

Arc features, commands, objects, panels and settings have descriptive names in Arc's own vocabulary.
They are never named after another company's distinctive feature names, even unregistered ones.
Generic industry terms (layer, block, family, alignment, corridor, sketch, modifier) are used freely.
Each product's glossary records Arc's names.

## Corpus admission

A file may be added to an `arc-corpus-<domain>` repository only if:

1. the contributor created it, or holds the rights to it and states the basis;
2. it is licensed `CC0-1.0` or `CC-BY-4.0`, or is project-original under Apache-2.0;
3. it contains no personal data, client names, project names, addresses, people's names in metadata
   or file paths, and no confidential design;
4. it is not a vendor sample file, template or file shipped with or extracted from another product;
5. its ledger row states path, owner, SPDX license, origin (the software and version that wrote it,
   if any), SHA-256 and what it exercises.

Files produced by other products' software from contributor-created content are admissible under
these rules; files that embed that software's own content libraries are not. Maintainers may remove
any file whose provenance is questioned while the question is resolved.

## When something is not clean

1. **Stop** work on the affected part.
2. **Record** what happened in the issue: what material, how it was encountered, which code or
   documents may be affected.
3. **Tell** the repository maintainers. Do not remove history or rewrite records yourself.
4. The maintainers **decide**: re-derive the affected part by a different contributor from the
   records, replace it with a properly licensed source, or remove it. Later work by the same person on
   the same part is treated as suspect until the decision is made.
5. The decision and its outcome are recorded in the issue and, if a release is affected, in the
   release notes.

Reporting exposure is expected and never penalized; concealing it is a breach of this policy.
