# plugin-example

The canonical "a plugin is a candy" reference — a minimal plugin serving the
`exampleprobe` check verb (`verb:exampleprobe`).

The plugin is a deterministic pass-with-marker probe: it echoes
`plugin_input.marker` back in its result, so a bed can assert the value
round-trips author → provider → result. It is the same
authoring/discovery/validation/ADE as any candy, plus a `plugin:` block — the
template every other plugin follows.

## What it provides

| Capability | Surface |
|---|---|
| `verb:exampleprobe` | the `exampleprobe:` check verb — a deterministic pass echoing its `marker` |

The plugin is **dual-placement**: its Go lives in the candy (an importable
provider package plus a `cmd/serve` shim). It is compiled into charly when listed
in `compiled_plugins:`, and served out-of-process over go-plugin gRPC by the shim
when it is not. Placement is invisible above the provider registry — the verb
dispatches identically either way.

## How to use it

Compose the plugin candy in a box or check bed's `candy:` list:

```yaml
- '@github.com/opencharly/plugin-example/candy/plugin-example:<tag>'
```

Then author the verb in a plan:

```yaml
- check: the exampleprobe verb passes
  id: exampleprobe-passes
  exampleprobe: {marker: exampleprobe-marker-ok}
  context: [build, deploy]
```

## Layout

- `candy/plugin-example/` — the plugin module: `plugin.go` (the provider +
  `NewProvider()`/`NewMeta()`), `schema/exampleprobe.cue` (the self-contained
  `#ExampleprobeInput`), `params/cue_types_gen.go`, `cmd/serve/main.go`.
- `charly.yml` — the root project manifest (`discover: candy`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.

## Related

- Owning skill: `/charly-check:check` — the check verb catalog. This candy
  carries no `skill:` entity of its own; the gap is tracked in
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- `/charly-internals:plugin` — the plugin/provider model.
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI.
