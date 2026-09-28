# TC Cure Engine — outreach demo

A one-page product walkthrough for tyre and rubber manufacturers, with the live engine
demo, a 90-second tour and a booking calendar that creates a Google Meet, emails both
sides and logs the lead.

```
index.html              the page (English, worldwide)
demo/index.html         the live TC Cure Engine demo: one offline HTML file, synthetic data
downloads/              simulated HIOKI logger files and sample report PDFs
assets/                 screenshots (WebP), 16-second teaser, 90-second walkthrough (WebM + MP4),
                        posters, Open Graph image, OussamaLabs emblem and favicon
dist/…Overview.html     self-contained single file for email attachments / offline
tools/build_single_file.py   rebuilds dist/ from index.html + assets/
```

Everything in the demo is synthetic: invented compounds, a schematic tyre section and
simulated logger files. No customer data, recipe or drawing is published here.

## Personalise a link per prospect

The bare link works for anyone. Add URL parameters to tailor it:

| Parameter | Default | Changes |
|-----------|---------|---------|
| `company` | —       | "Prepared for …" badge, page title, pre-filled Company field, and the same badge inside the demo |
| `ref`     | —       | Campaign tag stored with the booking (`source` = `tc-cure-engine-demo:<ref>`) and carried into demo links |

Example: `index.html?company=Acme%20Tire&ref=li-wave1`

The demo takes the same parameters on its own, plus `role` (`admin` or `operator`),
`test` (`touring` or `suv`) and `view` (`fill` or `report`) to open straight on a result:
`demo/?role=admin&test=touring&view=report&company=Acme%20Tire`

## Booking flow (n8n)

Workflow **TC Cure Engine - Booking API** (`W8v5glJF56AmY6jf`, project *Futuristic Life*)
on `https://oussama19.app.n8n.cloud`. Same design as the Vehicle Import booking workflow and
the same calendar, so a slot booked through either page is never offered twice.

| Endpoint | What it does |
|---|---|
| `GET  /webhook/tc-cure-engine/slots` | Free/busy from Google Calendar, returned as open 30-min slots (UTC ISO). The page shows them in the visitor's time zone. |
| `POST /webhook/tc-cure-engine/book`  | Body `{name, email, company?, phone?, message?, start, tz, lang?, source?, website (honeypot)}`. Validates, re-checks the slot, books a 20-min Google Meet, answers the page, then logs the lead to *OussamaLabs — TC Cure Engine Demo Leads* and emails Oussama and the prospect. |

Responses: `200 {ok:true, when, meetLink}` · `400 invalid` · `409 slot taken` · `503 calendar unavailable`.
If slots can't be loaded, the page falls back to an email button.

Booking hours: **Sunday to Friday, 08:00-20:00 Algeria time (UTC+1)**, Friday 13:00-14:00 blocked,
30-min grid, 20-min calls, 35 days ahead, 12 h minimum notice. That covers Europe, Africa and the
Middle East all day, India in the afternoon and US East/West Coast mornings. The rules live at the
top of the **Build Open Slots** and **Validate Booking** Code nodes; change both together.

## Rebuild the single-file version

```bash
python tools/build_single_file.py
```

## Deploy

GitHub Pages, published from `main`, root folder:
**https://oussamarevolotionary.github.io/tc-cure-engine-demo/**
If the page moves to another host, update the absolute `og:url` / `og:image` meta tags and
`SITE` in `tools/build_single_file.py`.

---
HIOKI and LR8400 are trademarks of their owner, named only to describe file compatibility.
