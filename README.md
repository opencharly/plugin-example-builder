# plugin-example-builder

The reference **build-time plugin-execution** builder (`builder:examplebuilder`)
— a plugin that runs while charly generates an image and splices a multi-stage
block into the Containerfile.

At image build, charly's build-path connect seam host-builds and connects this
plugin out-of-process, then invokes its `OpResolve`. The returned
`BuilderResolveReply` — a multi-stage `FROM … AS …` stage plus a `COPY --from`
artifact — is spliced into the generated Containerfile, so a built artifact
proves the plugin executed at build. A candy selects it with
`external_builder: examplebuilder`.

## What it provides

| Capability | Surface |
|---|---|
| `builder:examplebuilder` | the `OpResolve` build-time hook — returns the Containerfile stage + copy artifacts |

The plugin is a standalone Go module (importable provider package plus a
`cmd/serve` shim) served over go-plugin gRPC via the charly plugin SDK. It is the
**builder-leg** counterpart of the verb/step example plugins.

## How to use it

Compose the plugin candy, then select it as a candy's external builder:

```yaml
# in the consuming candy:
external_builder: examplebuilder
```

## Layout

- `candy/plugin-example-builder/` — the plugin module: `plugin.go` (the provider
  + `NewProvider()`/`NewMeta()` + the `OpResolve` `BuilderResolveReply`),
  `schema/examplebuilder.cue` (the self-contained `#ExamplebuilderInput`),
  `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-internals:plugin` — the plugin/provider model and the
  build-time plugin-execution mechanism. This candy carries no `skill:` entity of
  its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-build:build` — the image build + Containerfile generation surface.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
