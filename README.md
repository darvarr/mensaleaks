# SchoolPuzzle

**English** · [Italiano](README.it.md)

_The school puzzle, solved every morning._

SchoolPuzzle is a small offline Progressive Web App that answers the three questions every parent asks:
**What are they eating today? Is there school tomorrow? When is the parents' meeting?**

It runs on Android, iPhone and any desktop browser. There is no backend, no account and no API key.
This repository contains only the app code: the canteen menu and the school calendar are imported on each phone
and never leave the device.

## Features

- **Day view**: today's canteen menu (Monday's on weekends) with allergens and frozen-product markers.
  The day's events (meetings, parties, early dismissal) appear above the menu. On closure days, a
  "School closed" card replaces the menu. An **Upcoming** box at the bottom lists the next closures,
  early dismissals and meetings.
- **Week view**: all five school days at a glance, closures highlighted.
- **Calendar view**: closures, early dismissals, meetings, parent–teacher talks, parties and events,
  grouped by month, with filters. Past events are hidden by default.
- **Menu rotation**: the menu repeats over N weeks (usually 4). On import you say which week you are in; the app
  works out the rest. A −/+ control fixes the rotation if the canteen skips a week.
- **Child profile** (⚙): pick your child's group and section (e.g. "Piccoli · Gialli") to hide talks and
  notices meant for other classes.
- **Phone calendar export** (⚙ → .ics): exports upcoming events, with a reminder the evening before closures,
  early dismissals and meetings. On iPhone the file opens directly; on Android, import it from Google Calendar
  on the web (Settings → Import).
- **Share with another phone** (⚙): sends menu, current week, calendar and profile in a single message
  (WhatsApp, SMS, email…). On the other phone: ⚙ → Import → paste the whole message.
- **Offline**: once installed, the app works without a connection. Light and dark themes are supported.

## Privacy

Menu, calendar and settings live in the browser storage of each device (`localStorage`). Nothing is sent to any
server. GitHub Pages only serves the static app files. Keep your school's JSON files **out of this repository**:
they usually contain the school's name and location.

## Deploy on GitHub Pages

1. Put `index.html`, `sw.js`, `manifest.webmanifest` and `icons/` in the **root** of the repository.
2. Settings → Pages → Source: **Deploy from a branch** → `main` / `(root)` → Save.
3. Use the address shown in Settings → Pages ("Your site is live at `https://<user>.github.io/<repo>/`").

**Updating:** whenever you change a file, bump `VERSION` in `sw.js`. Installed apps download the new version
the next time they are opened and use it from the following launch.

## Install on a phone

- **Android**: open the link in Chrome → "Install app".
- **iPhone**: open the link in **Safari** → Share → "Add to Home Screen".
  Import your data **from the Home Screen app**, not from the Safari tab: on iOS they have separate storage.

## Importing data

⚙ → **Import**, then pick a file or paste the text. The app recognizes the content automatically:

| Content | Recognized by | After import |
|---|---|---|
| Canteen menu | `settimane` | asks which rotation week the current week is |
| School calendar | `eventi` | opens settings to choose group and section |
| Message from another phone, or a backup | `mensaleaks` | restores everything as it was |

Text around the JSON (a chat message, Markdown code fences) is ignored, so you can paste an AI's answer as is.

Suggested workflow: take a photo of the paper menu or calendar, send it to an AI assistant with one of the
prompts below, check the JSON, then import it. Twice a year for the menu, once a year for the calendar.

> The data format uses Italian field names (`settimane`, `giorni`, `eventi`, `chiusura`…), because the app
> and the source documents are Italian. Keep them as they are.

### Prompt: canteen menu (attach the photo)

```
Transcribe this school canteen menu into JSON. Return only the JSON, using exactly this schema:
{
  "versione": 1,
  "nome": "<menu title and school>",
  "allergeni": {"1": "Glutine", ...},          // full allergen legend from the footer
  "note": ["<footer notes>", ...],
  "settimane": [
    {"numero": 1, "giorni": {
      "lun": [{"nome": "Riso e prezzemolo", "allergeni": [3, 7], "surgelato": false}, ...],
      "mar": [...], "mer": [...], "gio": [...], "ven": [...]
    }}, ...
  ]
}
Rules: one entry per dish, in table order, fruit included. Keep dish names in the original language,
sentence case, without asterisks. "surgelato": true if the dish is marked with an asterisk (frozen product).
"allergeni": the numbers printed under the dish (empty list if none).
If a dish has an alternative ("O ..."), add "oppure": {"nome": "...", "allergeni": [...]}.
```

### Prompt: school calendar (attach the PDF or photo)

```
Transcribe this school calendar into JSON. Return only the JSON, using this schema:
{
  "tipo": "calendario",
  "versione": 1,
  "nome": "<title, school year and school>",
  "periodo": {"inizio": "YYYY-MM-DD", "fine": "YYYY-MM-DD"},   // first and last day of school
  "gruppi": ["Piccoli", "Mezzani", "Grandi"],                  // only if the calendar names groups
  "sezioni": ["Verdi", "Gialli", "Blu"],                       // only if the calendar names sections
  "eventi": [
    {"data": "YYYY-MM-DD", "titolo": "...", "tipo": "..."}
  ]
}
Event fields:
- "data" (required) and "fine" (only for multi-day events, inclusive), format YYYY-MM-DD.
  If the date is not set yet, use "data": null and "mese": "YYYY-MM".
- "tipo", one of:
  "chiusura"  = school closed (holidays, public holidays, bridge days, patron saint's day)
  "uscita"    = school day with early dismissal
  "riunione"  = parents' meetings, class meetings, parents' help desk
  "colloquio" = individual parent–teacher talks
  "festa"     = parties and celebrations with the children
  "evento"    = open days, awareness days, external events, back-to-school days
  "orario"    = settling-in periods and timetable changes
- optional: "ora" (e.g. "17:00", "9:30-11:30", "dalle 13:00"), "uscita" (early dismissal time, e.g. "13:00",
  also allowed on parties or other events), "gruppo" and "sezione" (string or list, only when the event concerns
  specific groups or sections), "note", "da_confermare": true if the calendar says "to be decided/confirmed".
Keep titles in the original language.
Rules: "Last day of school ... dismissal at 13:00" becomes "tipo": "uscita" with "uscita": "13:00".
Holiday periods ("from 23 December to 6 January") become a single "chiusura" event with "data" and "fine".
Check that the weekday written in the calendar matches the date; if it does not, say so in "note".
Sort events by date.
```

## Data format at a glance

**Menu**: `settimane[]` → `giorni` (`lun`…`ven`) → dishes `{nome, allergeni[], surgelato, oppure?}`,
plus `allergeni` (legend) and `note`.

**Calendar**: `eventi[]` → `{data, fine?, mese?, titolo, tipo, ora?, uscita?, gruppo?, sezione?, note?, da_confermare?}`,
plus `periodo`, `gruppi`, `sezioni`. Days outside `periodo` are shown as closed.

## Tech notes

- A single `index.html` (vanilla JS, no build step, no dependencies), a cache-first service worker (`sw.js`)
  and a web app manifest.
- The storage key `menuMensa.v1` and the `mensaleaks` share marker come from the app's first version
  (MensaLeaks); they are kept so existing installs and shared messages keep working.
- To rename the app, change `APP` in `index.html`, the `<title>` and `apple-mobile-web-app-title` tags, and
  `manifest.webmanifest`.

## License

MIT, see [LICENSE](LICENSE).
