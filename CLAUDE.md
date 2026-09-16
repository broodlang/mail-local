# mail-local — guidance for Claude

A `mail` `Adapter` that delivers into a keeper process, and hatch pages over a snapshot
of it. The shape is observe-local's: pure folds over the state, a `gen/defserver`
keeper registered by name, `page`/`resource` pure over a `:state` a test hands in, and
`handler` the thin conn wrapper.

- The HTML body is never inlined into the page: the iframe loads `<id>/html`
  (`resource`), sandboxed by attribute and by CSP header. Keep it that way.
- `transmit` is a `gen/call`, not a cast, so the caller gets the entry's id back.
- `stop` unregisters the name BEFORE `gen/stop` (which is asynchronous), so `pid` is nil
  the moment it returns; the adapter test is `:serial` because it owns the one keeper.
- `web/template` escapes text nodes and attribute values; `raw` is used only for the
  stylesheet.
- Run the one test file (the full suite is blocked on this machine) and `nest format`
  before committing. No trailing `!`, no abbreviations.
