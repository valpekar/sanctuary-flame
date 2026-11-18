# sanctuary-flame

The Sanctuary's upkeep manifest: one small static JSON the Architect updates by hand, roughly once a month.

The app reads it from:

```
https://raw.githubusercontent.com/valpekar/sanctuary-flame/main/flame.json
```

**This repository must stay public and must stay under an account the Architect holds.** It is read by a shipped binary, so whoever controls the name controls the figure and the roll the Temple shows every Acolyte. The file may be absent for as long as you like — the bar simply stays hidden — but the name it waits under is not optional.

## The file

Create `flame.json` at the repository root:

```json
{
  "current": 42,
  "target": 1000,
  "guardians": 19,
  "guardianNames": ["Wren", "mothlight", "A. Okonkwo"],
  "updated": "2026-11-01"
}
```

| Field | Type | Required | Meaning |
| --- | --- | --- | --- |
| `current` | number | yes | This month's figure so far. Must be `>= 0`. |
| `target` | number | yes | The month's goal. Must be `> 0`, or the whole file is ignored. |
| `guardians` | number | no | How many Guardians stand behind the figure. |
| `guardianNames` | string[] | no | The roll the Temple shows, by whatever name each Guardian chose. |
| `updated` | string | no | For your own eyes. The app does not read it. |

`current` and `target` share whatever unit you choose — currency, or a percentage of 0–100. The Temple prints the figure itself, not only the bar's fraction, so pick one and keep it.

## What the app does with it

- Fetched with an ephemeral session: no cookies, no credentials, no query string, nothing about the Acolyte. The Sanctuary is a private confessional and this request keeps it that way.
- Fetched at most once every six hours, and the attempt is stamped whether or not it succeeds — so a broken file costs one request per six hours, not one per screen.
- **A bad file changes nothing.** Non-2xx, unparseable JSON, or `target <= 0` all leave the last good figure in place. Nothing is ever invented, seeded or guessed; with no figure ever read, the bar stays hidden rather than showing zero.
- The roll is bounded at 300 names of 48 characters, long ones cut with an ellipsis rather than dropped — no Guardian loses their name to a long one.
- An **empty** `guardianNames` array is read as you clearing the roll, and is honoured. A non-empty array that bounds down to nothing is treated as malformed, and the last good roll stands.

## Only names people agreed to show

`guardianNames` is a credits roll. Put in it only names Guardians have agreed to have shown publicly. Nothing here is ever derived from the app, from anyone's reading, or from anything an Acolyte did — the app sends no data and has none to send.

## Turning the bar on

Commit `flame.json` to `main`. Devices pick it up within six hours of their next look; a fresh install sees it at once.
