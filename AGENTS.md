# AGENTS.md — plugin-example-builder

Standalone plugin repo for the `examplebuilder` capability
(`builder:examplebuilder`) — the reference build-time plugin-execution builder.
The plugin is a Go module at `candy/plugin-example-builder/` (module path
`github.com/opencharly/plugin-example-builder/candy/plugin-example-builder`); the
root `charly.yml` only declares `discover: candy` so the repo is a project and
its candy is scanned.

Canonical files:

- `candy/plugin-example-builder/charly.yml` — the `plugin-example-builder:`
  candy entity (`plugin:` block, `plan:` check).
- `candy/plugin-example-builder/plugin.go` — the provider (`NewProvider()` +
  `NewMeta()`) and the `OpResolve` `BuilderResolveReply`.
- `candy/plugin-example-builder/schema/examplebuilder.cue` — the self-contained
  `#ExamplebuilderInput`.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:plugin` — the plugin authoring reference: the `plugin:`
  block, the unified Provider model (incl. the `builder` class), the per-plugin
  CUE-schema contract, placement. Load before touching the provider or schema.
- `/charly-build:build` — the image build + Containerfile generation surface the
  builder splices into.
- `/charly-internals:git-workflow` — before any git/PR action.

## Build / validate / test

- `go build ./...` in `candy/plugin-example-builder/` — compile the plugin
  module.
- `go test ./...` in `candy/plugin-example-builder/` — the plugin's Go tests.
- `charly box validate` at the repo root — the structural check (the candy +
  `plugin:` block, CUE schema).
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate.

## Modify this repo

- Edit the `plugin-example-builder:` candy entity, the Go source, and
  `schema/examplebuilder.cue` **together**.
- The plugin is **out-of-tree / out-of-process** (host-built and connected at
  image build); keep the returned `BuilderResolveReply` deterministic.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
