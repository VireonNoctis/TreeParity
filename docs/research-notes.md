# TreeParity Research Notes

## Research question
Which properties of a directory tree survive a copy, archive/extract operation, or synchronization workflow across different operating systems and filesystems?

## Why parity is property-specific
Filesystem and archive tools preserve different subsets of the source's properties. File bytes may remain identical while timestamps, permissions, ownership, links, alternate streams, sparse allocation, or filename representation changes. A destination may lack the capability to represent a source property.

**Design implication:** never reduce the result to one “copy passed” flag. Report content, structure, metadata, links, names, and platform-specific capabilities separately.

## Unicode and filename comparison
Unicode permits canonically equivalent strings with different byte representations. Normalization decisions can affect matching and collision detection, but compatibility normalization may discard distinctions.
Reference: https://www.unicode.org/faq/normalization.html

**Design implication:** preserve original path bytes or platform-native representation where available. Compute comparison keys separately, record the normalization method, and never rename files silently to make a test pass.

## Platform APIs and tools
Python's filesystem utility documentation illustrates that high-level copy functions expose specific semantics rather than guaranteeing every metadata attribute survives every platform.
Reference: https://docs.python.org/3/library/shutil.html

POSIX defines a set of filesystem and pathname semantics, but support and extensions differ across operating systems and filesystems.
Reference: https://pubs.opengroup.org/onlinepubs/9799919799/

**Design implication:** feature-detect capabilities at source and destination. Report what was checked, not what the tool theoretically might preserve.

## Main fidelity dimensions
- Content: file bytes and stable hashes.
- Structure: paths, file types, empty directories.
- Metadata: timestamps, permissions, ownership, ACLs, attributes.
- Links: symlink targets, hard-link relationships, link count where observable.
- Names: case collisions, reserved names, Unicode representation and normalization.
- Storage: sparse allocation and alternate streams where supported.
- Portability and safety: path traversal, links escaping extraction roots, collisions that overwrite data.

## Threats to validity

| Threat | Why it matters | Response |
|---|---|---|
| Destination cannot represent a property | Missing property may be unavoidable | Classify as unsupported, not automatic tool failure |
| Metadata changes naturally | Access time or timestamps can vary | Define stable and volatile fields |
| Hash-only comparison | Metadata and structure can be lost while bytes match | Compare multiple layers |
| Symlink traversal | Inventory or extraction may access outside the tree | Do not follow links by default; constrain destination paths |
| Name collisions | Case or Unicode differences can collapse distinct paths | Preflight collisions before writing |
| Privilege differences | Ownership and ACL tests may be misleading | Record permissions and capability limitations |
| Host-specific behaviour | One CI runner cannot represent all filesystems | Keep a documented OS/filesystem matrix |

## Open questions
1. Which filesystem properties can be compared portably without elevated privileges?
2. How should sparse-file and alternate-stream comparisons be represented?
3. Which filename comparison profile is appropriate for each destination?
4. How should hard-link topology be compared when cross-volume operations turn links into copies?
5. What fixture set best captures archive and sync-tool differences?

## Research exit criteria
The prototype must preserve source data, detect intentional fixture differences, report unsupported metadata honestly, and test path traversal and collision hazards before the tool writes any destination tree.
