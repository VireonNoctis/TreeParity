# TreeParity Mitigation Strategy

| Risk | Planned control | Validation | Residual limitation |
|---|---|---|---|
| Bytes preserved but metadata lost | Separate content/structure/metadata comparisons | Fixture with unchanged bytes and changed permissions | Some metadata is unavailable without privileges |
| Unsupported destination property reported as failure | Capability detection and explicit unsupported state | Run on a destination lacking a source feature | Capability can vary with mount options and privilege |
| Case/Unicode name collision | Preflight using destination-specific comparison rules | Two source names collide under target profile | Rules may differ from actual target implementation |
| Symlink escapes destination | Do not follow symlinks by default; resolve and validate output paths | Link points outside the staging root | Platform link semantics vary |
| Archive path traversal | Reject absolute paths and parent traversal; constrain extraction root | Crafted archive entry fixtures | Extractor bugs remain possible |
| Source accidentally modified | Read-only source model; pre-operation manifest; staging destination | Verify source hashes and metadata after run | External tool may still change unrelated system state |
| Timestamp noise misreported as loss | Document stable fields and tolerances | Vary access time intentionally | Filesystems may update timestamps asynchronously |
| Hard links become independent copies | Compare link topology separately from content | Multiple names share an inode fixture | Some platforms do not expose equivalent identifiers |
| Permission tests misleading | Record process privilege and capabilities | Repeat under different allowed privilege profiles | CI runners cannot model every deployment |
| Transfer tool overwrites collisions | Collision report and dry-run/preflight | Collision must block unsafe write by default | Tool may have additional undocumented behaviours |

## Operating safeguards
- Always operate on generated or explicitly selected fixtures, never the only copy of user data.
- Source trees are read-only; use a fresh destination.
- Preflight path names and archive entries before extraction.
- Do not follow symbolic links during inventory unless a test explicitly requires it.
- Enforce file-count, path-length, archive-expansion, and time budgets.
- Never silently normalize names, timestamps, or permissions to obtain a pass.
- A result of unsupported or unknown is preferable to fabricated parity.

## Release gates
The tool requires tests for source immutability, traversal resistance, case and Unicode collisions, symlink handling, file hash correctness, and metadata availability. Claims must identify the OS, filesystem, and property set tested.
