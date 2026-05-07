# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-05-07

### Added

- `/spec` skill — guides Claude through a four-phase spec authoring workflow (context → clarification → section-by-section drafting → save). Writes specs to `specs/NN-slug.md` with status `Draft`.
- `/spec-impl` skill — validates spec status is `Approved`, creates a dedicated git branch (`spec-NN-slug`), and implements the spec step by step with diff pauses between each step.
- `scripts/link-skills.sh` — symlinks all skills into `~/.claude/skills/` for local development.
- `scripts/install-to-agent.sh` — translates skills for Cursor, Codex, and Antigravity agents.
- `scripts/list-skills.sh` — lists all available skills.

[Unreleased]: https://github.com/Klerith/fernando-skills/compare/v0.1.0...HEAD
[0.1.0]: https://github.com/Klerith/fernando-skills/releases/tag/v0.1.0
