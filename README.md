# Invitely — party invite generator

A single static page. Fill in the occasion, date/time, venue and details; it renders a
1080x1350 invitation card on a `<canvas>` and produces a shareable link.

## How it works

There is no backend and no database. The whole event is JSON, gzip-free, encoded as
base64url and parked in the URL fragment:

```
https://example.com/#i=eyJ0aCI6ImR1c2siLCJ0IjoiU2Ft...
```

- Fragment (`#…`) never leaves the browser, so the event details are not sent to any server.
- Opening a link with `#i=` switches the page into **viewer** mode; without it you get the **editor**.
- Links do not expire and nothing can be deleted out from under the guests.
- A fully-loaded invite (every field at max length) lands around 800 characters — well inside
  what WhatsApp, SMS and iMessage handle.

## Sharing

`Share invite` uses the Web Share API. On phones that support file sharing it hands WhatsApp
the PNG *and* the text + link in one go. Elsewhere it copies the text and downloads the PNG so
the user can attach it manually.

## Viewer extras

- **Add to calendar** — generates an `.ics` (floating local time, so it is correct in every timezone
  the guests happen to be in).
- **Open in maps** — Google Maps search for the venue + address.
- **Pass it on** — re-shares the same link.

## Limits worth knowing

- The link preview in WhatsApp shows the generic OG tags, not the event, because there is no server
  to render a per-event OG image. The image is shared as an attachment instead. If per-event link
  previews matter later, that needs a serverless function (and a short-link store to go with it).
- No RSVP tracking — that also needs a backend.

## Local dev

```powershell
python -m http.server 8787
```

Then open http://127.0.0.1:8787/index.html

## Deploy

Static; publish the folder as-is. `netlify.toml` sets `publish = "."`.
