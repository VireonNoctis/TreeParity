# TreeParity Validation Matrix

## Synthetic fixture set
Build generated trees containing regular files, empty files, nested and empty directories, executable bits, varied timestamps, long names, case-only pairs, Unicode-equivalent names, symlinks, hard links where available, sparse files where supported, and metadata that the test process is permitted to read.

## Required cases

| Category | Test | Pass condition |
|---|---|---|
| Content | Identical and changed file bytes | Hash comparison correct |
| Structure | Added, removed, renamed path and empty directory | Path set difference correct |
| Metadata | Permission, timestamp, ownership or attribute variation | Per-field result and capability state correct |
| Links | Symlink target and hard-link relationship | Link facts are distinct from file bytes |
| Names | Case collision, Unicode normalization variant, reserved path | Preflight identifies relevant risk |
| Capability | Destination lacks property or privilege | Unsupported/denied is not called pass |
| Safety | Link escapes root, absolute archive path, parent traversal | Unsafe write blocked |
| Source integrity | Full transfer test | Source content and metadata unchanged |
| Scale | Many files, long paths, deep tree | Resource limits explicit and safe |
| Portability | Same fixture on declared runners | Results differ only where documented |
| Export | Report schema and redacted paths | Output validates and avoids unnecessary absolute paths |

## Acceptance criteria
- Every path and property comparison has a clear state.
- Source trees remain unchanged.
- Content parity never implies metadata parity.
- Unsupported destination features are reported as such.
- Preflight detects collisions before a tool overwrites data.
- Link traversal and archive extraction remain bounded to the staging tree.
- Reports identify OS, filesystem, transfer tool, arguments, and comparison version.

## Metrics
Measure path detection accuracy, content hash throughput, metadata coverage, false collision alerts, traversal-blocking results, memory use, and completion time across documented tree sizes. Report each metric alongside the source/destination environment.

## Regression policy
Every confirmed bug becomes a synthetic fixture. Any changed comparison rule must document the prior and new behavior. Cross-platform results should be stored as evidence, not generalized from a single runner.
