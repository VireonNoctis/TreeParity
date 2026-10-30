# TreeParity

<!-- REPOSITORY-HEALTH:START -->
[![Code quality](https://github.com/VireonNoctis/TreeParity/actions/workflows/repository-health.yml/badge.svg?branch=main)](https://github.com/VireonNoctis/TreeParity/actions/workflows/repository-health.yml)
[![Top language](https://img.shields.io/github/languages/top/VireonNoctis/TreeParity?style=flat-square)](https://github.com/VireonNoctis/TreeParity)
[![Languages](https://img.shields.io/github/languages/count/VireonNoctis/TreeParity?style=flat-square)](https://github.com/VireonNoctis/TreeParity)
[![Automated tech stack](https://img.shields.io/badge/tech_stack-auto--detected-64748b?style=flat-square)](TECH_STACK.md)
<!-- REPOSITORY-HEALTH:END -->


<p align="center">
  <strong>A successful copy is not always a faithful copy.</strong><br/>
  A filesystem transfer conformance laboratory for measuring what survives across filesystems, operating systems, archives, and sync tools.
</p>

---

## The problem

A directory tree can contain more than file bytes. It may include empty directories, executable bits, permissions, timestamps, symbolic links, hard links, extended attributes, alternate streams, sparse allocation, case-sensitive names, Unicode filenames, and ownership information. A destination may not support all of those properties, and a copy tool may silently change them.

TreeParity is planned to create a controlled source tree, transfer it through a selected tool or format, and compare the result. It should identify preserved, changed, missing, unsupported, and ambiguous properties rather than claiming universal parity.

## Planned capabilities

- Generate synthetic filesystem fixtures with known metadata and content.
- Fingerprint files and compare directory trees before and after transfer.
- Compare path sets, file bytes, directory structure, symlink targets, timestamps, permissions, and platform-specific metadata where permitted.
- Detect likely name collisions caused by case sensitivity or Unicode normalization.
- Test archive and synchronization workflows through adapters.
- Record source/destination filesystem type, OS, tool version, options, and capabilities.
- Produce a loss matrix showing preserved, transformed, unsupported, failed, and untested properties.
- Keep source trees immutable and write all transfer experiments into isolated staging directories.

## Workflow

1. Create or register a synthetic tree and its manifest.
2. Record file hashes and available metadata from the source.
3. Execute a copy, archive/extract, or sync operation in a dedicated destination.
4. Inventory the destination without changing it.
5. Compare bytes and metadata independently.
6. Classify each difference as preserved, transformed, unsupported, failed, or unknown.
7. Export results with environment details and a reproduction command.

## Planned technology stack

| Component | Planned technology | Purpose |
|---|---|---|
| Filesystem observer and comparison core | Rust | Explicit cross-platform traversal, hashing, and metadata adapters |
| Experiment orchestration and reports | Python 3.13+ | Fixture generation, tool invocation, comparison summaries |
| Result records | JSONL + JSON Schema | Durable, versioned manifests and findings |
| Local case metadata | SQLite if required | Experiment indexing without a server dependency |
| Tests | Cargo tests, pytest, property-based tests | Filesystem edge cases and regressions |
| Cross-platform CI | GitHub Actions and supported OS runners | Exercise documented OS differences |
| Isolation | Temporary directories and disposable VMs for risky tools | Keep test trees separate from user data |

The implementation must feature-detect platform capabilities instead of assuming every OS exposes identical metadata.

## Documentation

- [Research notes](docs/research-notes.md)
- [Mitigation strategy](docs/mitigation-strategy.md)
- [Architecture research](docs/architecture-research.md)
- [Validation matrix](docs/validation-matrix.md)

## Parity is property-specific

A copy may preserve file bytes while losing timestamps or permissions. A destination may be unable to represent a source property. In that case the honest result is “unsupported on destination”, not “pass” and not necessarily “copy tool bug”.

TreeParity separates:

- **Content parity:** file bytes and stable content hashes.
- **Structural parity:** paths, file types, and empty directories.
- **Metadata parity:** permissions, timestamps, ownership, and attributes.
- **Link parity:** symlinks, hard links, and link targets where observable.
- **Name parity:** exact names, case collisions, and Unicode equivalence.
- **Storage parity:** sparse allocation or alternate streams where supported.

## Principles

- Never experiment directly on a user's only copy.
- Preserve source data and compare against a pre-operation manifest.
- Report unavailable properties as unsupported or unknown.
- Never follow a symbolic link blindly when traversing untrusted trees.
- Treat archives and filenames as untrusted input.
- Detect path traversal and collision hazards before extraction or writing.
- Avoid claiming “bit-perfect” unless the exact property set and validation method are stated.

## Project status

**Research / early planning.** The docs define the comparison model and validation strategy; implementation claims must track code and evidence.

## Roadmap

- [ ] Define a cross-platform manifest schema.
- [ ] Implement stable file hashing and tree inventory.
- [ ] Build synthetic fixtures for case, Unicode, links, timestamps, and permission differences.
- [ ] Add copy and archive adapters with explicit tool/version capture.
- [ ] Implement metadata capability detection and the loss matrix.
- [ ] Add cross-platform regression runs.
- [ ] Document unsupported features per platform and destination filesystem.

## Research references

- Unicode normalization FAQ: https://www.unicode.org/faq/normalization.html
- Python file-copy and filesystem utilities: https://docs.python.org/3/library/shutil.html
- POSIX specification: https://pubs.opengroup.org/onlinepubs/9799919799/
- OWASP Path Traversal guidance: https://owasp.org/www-community/attacks/Path_Traversal

## Contributing

Add synthetic fixture trees, their expected manifest, the transfer command, platform details, and expected differences. Avoid including personal files or dangerous archives.

## License

Add an explicit licence before treating this repository as licensed for reuse.
