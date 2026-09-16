# mail-local

A [mail](https://github.com/broodlang/mail) adapter that keeps every email in the app
itself, with a page to read them — Swoosh's mailbox preview, for development and staging.
No SMTP, no vendor account, no network.

```lisp
(def mailer (mail-local/adapter))          ; starts the keeper, registered :mail-local
(mail/deliver email mailer)                ; → [:ok {:id 1}]
(mail-local/emails)                        ; newest first, each stamped :id and :at
```

In the router, behind whatever guards the operational pages:

```lisp
(get "/mailbox" (mail-local/handler {:refresh-ms 4000}))
(get "/mailbox/*path" (mail-local/handler {:refresh-ms 4000}))
```

`/mailbox` lists the inbox (newest first, refreshing itself when asked to); `/mailbox/<id>`
shows one email — every header, the HTML body as it renders, the text body, the
attachments to download, the provider options. `handler` takes `:prefix` when it is
mounted somewhere other than `/mailbox`, and `:title`.

The HTML body is shown in a **sandboxed iframe from its own URL** (`/mailbox/<id>/html`,
served with `Content-Security-Policy: sandbox`), never inlined into the page: an email's
HTML is content the app built from data, and the reader is signed in as a developer.

The mailbox is bounded (`*max-emails*`, 200): the oldest go first. `clear` empties it,
`stop` ends the keeper. In memory; a restart forgets.

For tests, `mail`'s own `memory-adapter` is the lighter choice — this package is for
looking at email in a browser.

## Running

```bash
nest fetch
nest test tests/mail-local_test.blsp
nest format
```
