# TreeParity Architecture Research

## Pipeline
Create/register source manifest → inventory source → preflight destination constraints → invoke transfer adapter in a staging directory → inventory output → compare properties → classify differences → export report.

## Intended stack
- Rust for cross-platform traversal, hashing, metadata adapters, and explicit error handling.
- Python 3.13+ for fixture generation, experiment orchestration, adapters, and report summaries.
- JSONL plus JSON Schema for manifests and findings.
- SQLite only if local indexing across experiments becomes valuable.
- Cargo tests, pytest, and property-based tests for filesystem edge cases.
- GitHub Actions across supported OS runners; disposable VMs where a filesystem/tool needs isolation.

## Manifest model
Each manifest should contain schema version, run ID, host OS/build, source and destination filesystem identifiers where available, tool and version, arguments, relative path, entry type, content hash where applicable, metadata fields, link relationships, capability check results, errors, and comparison profile.

Never store absolute host paths by default; use relative paths or redacted aliases where possible.

## Metadata capability interface
Each field should return a value plus a state: observed, unavailable, unsupported, denied, or error. Missing values must not be replaced with inferred defaults. The comparator should consider the destination capability profile before classifying a difference.

## Traversal and trust boundaries
Use explicit relative-path handling and avoid following links by default. Treat archive entries and filenames as untrusted. Validate that resolved output paths remain within the staging root before writing. Apply limits on entry count, nesting depth, path length, and decompressed size.

## Comparison engine
Compare path sets first, then content, type, metadata, links, and destination-specific collision profiles. Use algorithm-versioned comparisons. A single path difference may cause related metadata comparisons to be not applicable; report the dependency instead of emitting misleading cascades.

## Output
Produce a summary and a per-path loss matrix with statuses such as preserved, changed, missing, unsupported, denied, error, and not applicable. Include reproduction details and source/output fingerprints.

## Cross-platform strategy
Document every supported source/destination combination. Start with a small matrix and grow it only when CI or recorded manual tests provide evidence. Do not claim universal behaviour from a single operating system.
