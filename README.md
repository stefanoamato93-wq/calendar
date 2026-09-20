# Travel Calendar

A single-file, mobile-first web app to plan and consult where I'll be each day: location, whether I'm working in Milano, whether Marta is with me, and the flights/trains I'm taking (from, to, time), with as many events per day as needed.

**The location on a day is where I SLEEP that night**, not where I work. Working in Milano is a separate flag (see below), so a day can be "sleep in Torino, worked in Milano".

No backend code and no build step: everything (HTML, CSS, JS, and the seed data) lives in `index.html`. State is synced to a Firebase Realtime Database (the single source of truth) and cached in the browser's `localStorage` for instant load / offline. There is no manual backup/export, the cloud holds everything. To deploy: upload `index.html` to GitHub Pages.

## App icon

The app ships its own icon (an inline SVG calendar), wired up at runtime as the **favicon** (the Chrome tab icon and the icon Chrome uses for "Install app"), the **apple-touch-icon**, and a data-URI **web manifest** (`display: standalone`, blue `#007aff` theme). It's all generated in JS from a single SVG string in the `<head>`, so the file stays self-contained (no external image). To change the icon, edit the `svg` string in that head `<script>`.

## Deploy to GitHub Pages

1. Create a repo (e.g. `travel-calendar`) and upload `index.html`.
2. **Settings > Pages**, source "Deploy from a branch", `main`, root `/`.
3. Live at `https://<you>.github.io/travel-calendar/`.
4. On iPhone open in Safari and **Share > Add to Home Screen** for an app-like icon.

## Using it

- **Browse**: `‹` / `›` move between months, or **swipe left/right** anywhere on the calendar (swipe left = next month, right = previous). **Today** jumps to the current month.
- **List / Grid toggle** (top-left): **List** is the scrollable agenda (full chips per day). **Grid** is a Google-Calendar-style month grid with weeks stacked (Mon–Sun). In the grid each day is tinted by its location colour, with the location name, `(MI)` when working in Milano, ✈️/🚆 travel icons (green pill = booked, red = to buy), and a coloured dot per event. **Italian national holidays** are highlighted (gold accent) in both views, with the holiday name shown in List. **Marta** days show a pink dot in the top-right corner. The monthly summary chips are colour-matched to act as the location legend. Your view choice is remembered per device.
- **Colours** (🎨 in the header): pick a colour per **city** and per **event type**. **Torino** defaults to grey and **Milano commute** to a darker grey. The commute colour only tints days where you're based in Torino; on any other city the destination city's colour wins. Tap **Auto** to revert to default. Colour choices sync across devices.
- **Each day card** shows: location badge, holiday badge (if any), `(Milano)` under the date when working in Milan, a pink Marta dot top-right, travel chips (✈️/🚆 with from → to and time, strong green = booked / strong red = to buy), and event chips colour-coded by type.
- **Tap any day** (either view) to open the editor. It is ordered top to bottom: travel, events, flags, then location.
  - **Flights / Trains** — add as many legs as you need (`+ Add flight / train`); each row has mode (train/flight), From, To, time, the **airline / train company** (free text, e.g. Trenitalia, Ryanair), a **booking reference** (flights only, a 6-char alphanumeric code, e.g. a PNR), a **booked toggle** (green ✓ Booked = already bought / red ✗ To buy = still to purchase), and a × to remove it. The company shows on the day's travel chip. An **upcoming flight** (today or later) only shows green when it is booked **and** has a valid 6-char reference; one that is missing the reference (every existing flight starts without one) is shown with the same **red** "to buy" colour coding until you add the code. **Past flights keep the simple rule** (booked = green, no reference required), so flights you have already taken are not flagged red. Trains ignore the reference.
  - **Worked in Milano** — a standalone toggle. Set it on any day I worked in Milano even when no carnet ticket was used (I drove, someone gave me a lift, I bought a single ticket). It tints the day **dark grey** (the "Milano commute" colour) when I slept at home (Torino) or no city is set, shows **MI** in the grid cell, and feeds the **MI Milano** counter in the monthly summary. If the day already uses a carnet ticket the flag is **inferred**: the toggle switches itself on and locks (greyed out), so it can never contradict the carnet.
  - **Trenitalia carnet** — two toggles per day: **Carnet to Milano** (use a carnet ticket outbound) and **Carnet return** (use one for the trip back). A day using the carnet shows 🎫 per ticket in the grid cell (so `🎫🎫` for out + back). Each toggled trip counts as one ticket against the current carnet (see Carnet below) and implies "worked in Milano".
  - **Events** — add as many as you need (`+ Add event`), each with a text and a category colour (Work, Social, Appointment, Leave requested, Leave to request); × removes a row.
  - **Working in Milano** / **Marta** — toggles.
  - **Where will I be** — type a location or tap a quick city chip. Add new cities with the "Add a city" box; tap × on a chip to delete that city from the list.
  - **Save day**, or **Clear** (top-left) to empty the day.
- **Move a flight / train to another day**: in the day editor, every flight/train row has a **📅 Move** button. Tap it, pick the target date, and that leg jumps to the chosen day (it's removed from the current day and the current day is committed at the same time). Both days sync immediately.
- **Bulk edit** (📋 **Bulk** in the header): apply the same change across a date range in one go. Pick a **from** and **to** date (defaults to the visible month), then set any of: **Worked in Milano** (set / remove / no change — "remove" also clears that day's carnet tickets, otherwise they would re-imply the flag), **Marta** (set / remove / no change), **Set location** (overwrite the location on every day in range, with city quick-chips), and **Add an event** (append the same event + category to every day). Tap **Apply to range**, or **Done** (top-right of the sheet), which also saves any pending changes before closing (previously **Done** discarded them, only **Apply to range** saved — fixed). **Clear range** (top-left of the sheet) empties every day in the range (asks for confirmation first). Anything left on "No change" / unchecked is skipped. All changes sync to every device.
- **Monthly summary** (compact card pinned to the **bottom**) auto-counts days per **sleep** location, plus **MI Milano** (days worked in Milano, whether set by hand or inferred from a carnet), **🎫 Tickets** (carnet tickets used that month) and **Marta** days. Each is shown only when non-zero.

## Trenitalia carnet

Track a carnet (a booklet of N train tickets) and how many you've used. A small **🎫 carnet card** sits just above the monthly summary showing the current carnet's company, **used X / size**, **tickets left** (green / amber / red), a progress bar, and the date it started. Tap it (or the **🎫** button in the header) to open the **carnet manager**:

- **Current carnet** — company, purchase date, used / total, and remaining.
- **New carnet bought** — record a freshly bought booklet: pick the purchase date (defaults to today), the number of tickets (defaults to 10), and the company (defaults to Trenitalia). The most recently bought carnet is always the "current" one.
- **All carnets** — history of every carnet with its used / remaining count; × deletes a record.

How the counter works: each day you flag **Carnet to Milano** and/or **Carnet return** in the day editor counts as one ticket used. A carnet only counts tickets flagged **from its own purchase date onwards** and **before the next carnet's date**, so buying a new carnet resets the count from that day.

Two refinements that make a **future-dated** carnet behave correctly:

- **"Current" = the most recent carnet bought on or before today.** A carnet you record with a future purchase date is **upcoming**, not current: it no longer hijacks the card (which used to show it with 0 used and a full "tickets left", while the booklet actually in your pocket was pushed into history). It still counts only the tickets from its own start date onwards, and the carnet in use keeps its own count until that date. If every recorded carnet is in the future, the nearest one is shown and labelled "Next carnet (not started yet)".
- **Used-so-far vs planned.** The headline count and the progress bar are **tickets up to today**; commutes you have already flagged on future days are reported separately as "N more already planned" (with the eventual `used/size` in the manager), so a month of planned trips no longer makes the booklet look spent.

Everything (toggles, leg company, carnet records) is stored in the database and synced across devices.

## Backup / Sync

There is no manual backup anymore: the **Firebase Realtime Database is the single source of truth** and the app syncs to it automatically. The old export/import/reset modal and its functions (`openTools`, `importData`, `resetAll`, `downloadJson`, `copyData`, `buildExportObject`) and the `.export-text` styles have been **removed** from the file. If you ever need a snapshot, read the DB directly via the REST API (see "Debugging sync" below) or re-add an export from git history.

## Cloud sync (across devices)

The plan syncs automatically to a Firebase Realtime Database, so the same data shows up on your phone and your laptop. The coloured dot next to the title in the header shows the connection: **green = online**, **grey = offline**. Edits made offline are queued by the Firebase SDK and pushed when you reconnect.

How it works: the baked-in `SEED` is the baseline; only the days you change from it (and any cities you add) are stored in the cloud under a `travelCalendar` node. `localStorage` is kept as an instant-load / offline cache. On the first ever run the app migrates whatever edits the device already had up to the cloud.

## Changing the published defaults

To change the **published default** baseline (what a brand-new device sees before the cloud loads), send an exported JSON to Kiro and ask to update the `SEED` in `index.html`, then re-upload the file. The cloud overrides still win once they load; the SEED is only the baseline for untouched days.

---

# Developer handoff

## What this is

A single-file vanilla-JS app (`index.html`, no framework, no build). The published
plan is baked into a compact `SEED` string; per-device edits are layered on top,
synced to a **Firebase Realtime Database**, and cached in `localStorage`. Bump
`VERSION` (top of the config `<script>`, currently `4`) on functional changes.

The file has 6 `<script>` blocks: (0) a head `<script>` that builds the app icon
(favicon / apple-touch / data-URI manifest from an inline SVG), (1) a banner
comment, (2) CONFIG, (3) SEED, (4) the main classic-script app logic, and (5) a
`<script type="module">` at the very end that is the Firebase transport. Classic
scripts share one global lexical scope; the module talks to them only through
`window.*` hooks (see below).

## Project state / where we left off (last session)

- **Latest session (Milano flag restored + carnet future fix, VERSION 9).** Three changes:
  1. **`milano` is a real flag again.** `parseSeed` now reads the `milano` seed flag
     (it was ignoring it), `normalizeDay` keeps `!!d.milano || carnetOut || carnetBack`,
     the day editor has an **edMilano** toggle that a carnet ticket force-checks and
     locks (`syncMilanoLock()`), `collectDayFromEditor` ORs the three, the grid shows
     `MI` when there is no carnet, `dayColor` returns the commute grey when
     `milano && (!loc || loc === HOME_CITY)`, and bulk edit gained a `bulkMilano`
     tri-state (its "off" also clears `carnetOut`/`carnetBack`).
  2. **Carnet future-date handling.** `carnetStats()` now marks the current carnet as
     the latest one with `date <= today` (future ones are `future: true`, tagged
     "upcoming") and splits usage into `usedToDate` / `planned`. The card and the
     manager report the to-date figure, with the planned tail called out separately.
  3. **Wording:** the location field is now "Where will I sleep?" and the summary
     header "where I'll sleep", to match the convention that the city is the
     overnight city.


- Feature-complete and deployed to GitHub Pages. Implemented, in order, over the
  sessions: editable days, unlimited events, unlimited flights/trains, editable
  city list, location-at-bottom editor, Jun–Dec 2026 seed, month **Grid** view,
  per-leg **booked** (green/red) status, cloud **sync**, and editable
  **city colours** (Torino grey / Milano-commute darker grey).
- **Latest session (bulk / move / icon / backup removal):**
  - **Bulk edit** sheet (📋 Bulk in the header). Range from/to (defaults to the
    visible month), tri-state selects for Marta and Milano, a "Set location"
    toggle with city quick-chips, an "Add an event" toggle, and a "Clear range"
    action (confirmed). `openBulk`/`applyBulk(clearMode)`/`closeBulk`,
    `dateRangeKeys(a,b)` (inclusive), `bulkLocChipsHtml`/`renderBulkChips`. Writes
    the whole overrides set in one go via `fb.setDays(collectOverrides())`.
  - **Move a flight/train to another day.** Each leg row has a **📅 Move** button
    plus a hidden `.move-date` `<input type=date>` (opened with `showPicker()`).
    `moveLegToDay(dateInput)` appends the leg (read by the new `readLegRow(row)`
    helper, also used by `collectDayFromEditor`) to the target day, removes the row,
    commits the source day, and syncs both days.
  - **App icon.** A head `<script>` generates an inline SVG calendar and wires it as
    favicon + apple-touch-icon + data-URI web manifest (`display: standalone`,
    theme `#007aff`). Self-contained, no image file. `<meta name="theme-color">`
    and `apple-mobile-web-app-title` added.
  - **Backup/Sync removed.** The whole Tools modal and its functions
    (`openTools`/`closeTools`/`importData`/`resetAll`/`downloadJson`/`copyData`/
    `buildExportObject`) and the `.export-text` CSS are gone (Firebase is the only
    source of truth). `fb.removeAll` remains in the transport but is now unused.
  - **Italian national holidays** highlighted in both views (`IT_HOLIDAYS` map,
    keyed by `MM-DD`; Easter/Pasquetta hardcoded for 2026). `holidayName(key)`
    returns the label. List shows a gold `holiday-badge`; Grid tints the day
    number gold (`.gcell.holiday:not(.today) .gnum`) and adds a tooltip.
  - **Editable event-category colours.** `EV_COLORS_DEFAULT` holds the per-cat
    chip background; overrides are stored in the same colours map under the
    `ev:<cat>` key and synced. `evCatColor(cat)`/`evCatHex(cat)` resolve them;
    `catDot(cat)` = `darken(bg, 0.42)` for the grid dots. The 🎨 modal now has an
    "Event types" section (see `renderColorRows`).
  - Event chips and the editor legend now render with **inline** `background`/
    `color` (no longer the `.ev-*` CSS classes), so colour overrides take effect.
  - Each event row in the editor shows a colour **swatch** (`.ev-swatch`) that
    updates live on category change (delegated `change` listener on `dayBody`).
  - **City quick-chips** in the editor are tinted with each city's colour.
  - **Marta** is now a pink dot top-right (`.marta-dot`) instead of a text flag;
    **Milano** shows `(Milano)` under the date in List / `(MI)` in Grid.
  - **Booked/unbooked** colours strengthened (solid green `#1ea84a` / red
    `#ff2d20`, white text) in chips, grid pills, and the editor toggle.
  - **Summary** moved to the bottom, made compact (`.summary-card.compact`), and
    counts cities plus a **Marta** days tally and a **🎫 Milano** (carnet) days
    tally (shown only when non-zero).
  - **Swipe left/right** on `.container` changes month (left = next).
  - **Backup/Sync fully removed** (DB is the source of truth); the Tools modal and
    its functions are deleted from the file (see latest-session notes above).
  - **Latest session (Marta label + counter + bulk-save fix):**
    - Renamed the "Marta here" label to **Marta** everywhere it appeared (day
      editor toggle, bulk-edit select + its options, and the grid Marta-dot
      tooltip). The `marta` data field and Firebase key are unchanged.
    - Added a **Marta days** counter to the bottom summary card (`renderSummary`),
      alongside the existing per-location tallies and the 🎫 Milano counter.
      Shown only when the count for the month is non-zero.
    - **Fixed a bug**: tapping **Done** in the Bulk edit sheet closed it without
      saving; only **Apply to range** persisted changes. `closeBulk()` now calls
      `applyBulk(false, { silent: true })` before hiding the modal, so Done saves
      silently (no toast/confirm-dialog noise) just like Apply to range does.
      `applyBulk` now takes an `opts.silent` flag and a shared `hideBulkModal()`
      helper closes the sheet in both paths.
- **Cloud sync is live and the database is unblocked.** The RTDB security rules
  were the blocker: only `lists/casa` (the grocery app) was readable/writable, so
  every calendar read/write returned HTTP 401 and nothing synced. The user
  published rules granting the `travelCalendar` node (see "Firebase" below).
- **First sync is order-sensitive (one-time only):** the cloud node starts empty,
  so the first device to connect seeds it. Open the device holding the latest
  edits FIRST, let the dot go green, then open the others. After that first
  migration, edits flow both ways automatically.

## Firebase (Realtime Database)

- Config is inline in the final module. It **reuses the grocery-tracker project**
  `groceries-d6616` (databaseURL
  `https://groceries-d6616-default-rtdb.europe-west1.firebasedatabase.app`), under
  a separate top-level node `travelCalendar` so it never touches grocery data.
- Node layout: `travelCalendar/days/<YYYY-MM-DD>`, `travelCalendar/locs` (array of
  city names), `travelCalendar/colors` (a flat `{key: "#hex"}` map; keys are a
  city name, `__commute__` for the Milano-commute colour, or `ev:<cat>` for an
  event-category colour), and `travelCalendar/carnets` (array of
  `{date, size, company}` carnet-purchase records).
- **Security rules (required).** The DB is not globally open; rules are per-node.
  The published rules must include the calendar node:
  ```json
  {
    "rules": {
      "lists": { "casa": { ".read": true, ".write": true } },
      "travelCalendar": { ".read": true, ".write": true }
    }
  }
  ```
  Edit at Firebase console → project `groceries-d6616` → Realtime Database → Rules
  → Publish. If reads/writes ever 401 again, this is the first thing to check.
  The `travelCalendar` rule is recursive, so the `days`, `locs`, `colors`, and
  `carnets` sub-nodes are all covered; no rules change was needed to add carnets.
- **No per-user auth** (same posture as the grocery app): anyone with the DB URL
  can read/write. Fine for personal planning data; do not store anything sensitive.

## Cloud sync internals

- The module is a thin transport. It exposes `window.fb` with:
  `ready`, `setDay(key,val)`, `removeDay(key)`, `setDays(obj)`, `setLocs(arr)`,
  `setColors(obj)`, `setCarnets(arr)`, `removeAll()`.
- It calls back into the classic app via these `window` hooks (all defined in the
  CLOUD SYNC GLUE block of the main script):
  - `onRemoteDays(remoteObj)` → `applyRemoteDays`: rebuild `DATA` = SEED defaults +
    remote overrides, save local cache, re-render.
  - `onRemoteLocs(arr)` → `applyRemoteLocs`: `setLocs(arr|[])`.
  - `onRemoteColors(obj)` → `applyRemoteColors`: `setColors(obj|{})`, re-render,
    refresh the colours modal if open.
  - `onRemoteCarnets(arr)` → `applyRemoteCarnets`: `setCarnets(arr|[])`, re-render
    the carnet card and (if open) the carnet manager.
  - `onSyncStatus('online'|'offline')` → paints the header `#statusDot`.
  - `collectOverrides()` → days differing from SEED (incl. cleared days).
  - `localLocsOverride()` / `localColors()` / `localCarnets()` → this device's
    local customizations, used by the migration.
- **Writes** are granular, from the classic side: `cloudSyncDay(key)` writes or
  removes one day (removes when the day equals the SEED default, so the DB stays
  lean and SEED updates still flow through); `cloudSyncLocs()` / `cloudSyncColors()`
  push the whole array/map. Called from `saveDayEditor`, `clearDay`, the city
  add/delete handlers, the colour editor, and the **Bulk edit** / **Move leg**
  actions. The bulk apply and a moved-leg commit both push via
  `fb.setDays(collectOverrides())` / `cloudSyncDay(key)`.
- **One-time migration (per node).** On the FIRST snapshot of each node
  (`daysInit`/`locsInit`/`colorsInit` flags in the module), if the cloud node is
  `null` the device pushes its local customizations up instead of applying empty
  (this is what prevents a fresh/empty device from wiping a device that has edits,
  and was the bug fixed last session for colours specifically). After init, a
  `null` snapshot is treated as a real Reset and clears local to defaults.
- **localStorage keys:** `travelCalendarData` (day overrides cache, `{version,days}`),
  `travelCalendarLocs` (city list), `travelCalendarColors` (colour overrides),
  `travelCalendarCarnets` (carnet-purchase records), `travelCalendarView`
  (`list`|`grid`, device-local, NOT synced).

## Debugging sync

The RTDB has a REST API; append `.json` to any path. No auth needed once rules
allow the node. Examples (PowerShell):

```powershell
$base = "https://groceries-d6616-default-rtdb.europe-west1.firebasedatabase.app"
Invoke-RestMethod "$base/travelCalendar.json"            # whole calendar node (null if empty)
Invoke-RestMethod "$base/travelCalendar/days.json"       # just the day overrides
```

- HTTP **401** = security rules don't allow the node (fix the rules).
- `null` = node empty (no successful write yet; check the user opened the app and
  the status dot is green).
- Returns JSON = sync is working; that's the shared truth all devices converge to.

## Colour system

- **Cities.** `cityColor(loc)` returns `{bg, fg}` for a city: user override (hex,
  from the 🎨 editor) > `DEFAULT_COLORS` (Torino grey, Milano-commute grey) >
  `LOC_COLORS` (legacy pairs) > hashed pastel. `dayColor(dy)` applies the rule:
  the Milano-commute colour only wins on `HOME_CITY` (`'Torino'`) days, otherwise
  the destination city's colour wins. Used by grid cells and list badges; summary
  tallies use `cityColor` (no commute).
- **Events.** `EV_COLORS_DEFAULT` is the per-category chip background (pastel hex).
  `evCatColor(cat)` returns `{bg, fg}` (override under `ev:<cat>` > default), and
  everything derives from `bg`: chip text via `readableFg`, the editor swatch, and
  the grid dot via `catDot(cat)` = `darken(bg, 0.42)`. Event chips/legend render
  with inline styles (not `.ev-*` classes) so overrides apply.
- **Storage.** All overrides (cities, `__commute__`, and `ev:<cat>`) live in one
  `localStorage` map (`travelCalendarColors`) and sync to `travelCalendar/colors`.
  `setColor(key, hex)` (hex `null` = revert to auto); `COMMUTE_KEY` is the
  pseudo-key for the commute colour. `readableFg(hex)` picks dark/white text by
  luminance; `hslToHex`/`cityHex`/`evCatHex` give the `<input type=color>` a valid
  hex even for hashed cities. The 🎨 modal (`renderColorRows`) lists commute +
  cities, then an "Event types" section; `colorRowHtml(key,…)` branches on the
  `ev:` prefix.

## Holidays

`IT_HOLIDAYS` maps `MM-DD` → label for fixed Italian national holidays, plus the
movable Easter/Pasquetta hardcoded for 2026. `holidayName(key)` looks up by the
`MM-DD` slice of a `YYYY-MM-DD` key. Rendering adds the `holiday` class to the day
(`.day.holiday` gold accent + `holiday-badge` in List; `.gcell.holiday` gold day
number + tooltip in Grid). To support a different year's Easter, add the right
`MM-DD` entries (or switch to full-date keys).

## Data model

A day object:

```js
{
  loc: 'Torino',            // free text: the city where I SLEEP that night ('' = unset)
  milano: false,            // worked in Milano that day (own flag; any carnet ticket forces it true)
  marta: false,             // Marta is with me
  carnetOut: false,         // uses a Trenitalia carnet ticket to Milano (outbound)
  carnetBack: false,        // uses a Trenitalia carnet ticket on the way back
  travel: [ { mode:'f'|'t', from:'FCO', to:'PMO', time:'18:00', co:'Ryanair', ref:'AB12CD', booked:false } ], // any number of legs
  events: [ { cat:'social', text:'Compleanno Marta' } ]            // any number of events
}
```

Keyed by `YYYY-MM-DD`. `mode` is `f` (flight) or `t` (train). `co` is the airline /
train company (free text, optional). `ref` is the flight booking reference (a
6-char alphanumeric code; flights only). An **upcoming** flight (day >= today) is
shown green only when `booked` is true AND `ref` is a valid 6-char code;
otherwise it uses the red "to buy" colour coding. A **past** flight (day < today)
keeps the toggle-only rule (booked = green, no ref needed). `carnetOut` /
`carnetBack` each count as one
ticket against the current carnet. Event `cat` is one of the keys in `EVENT_CATS`
of the keys in `EVENT_CATS` (`''`, `work`, `social`, `appt`, `leave_ok`,
`leave_todo`); chips are coloured inline via `evCatColor(cat)` (overridable, see
Colour system), not by a CSS class.

## SEED format (the baked-in defaults)

One line per day, `;`-separated:

```
date ; loc ; flags ; travel ; events
```

- **flags** — comma list from `{milano, marta, cout, cback}` (`milano` = worked in Milano, `cout` = carnet to Milano, `cback` = carnet return). `cout`/`cback` imply `milano`; `parseSeed` reads the `milano` flag on its own too (it used to ignore it and derive Milano purely from the carnet flags, which silently dropped every Milano day in the seed).
- **travel** — legs joined by `|`, each `mode:FROM-TO@TIME` (time optional; keep the `@`, e.g. `f:FCO-MAD@`). Route is split on the first `-`. A booked (already bought) leg adds `!` to the mode: `f!:FCO-OLB@16:00` (booked) vs `f:FCO-OLB@16:00` (to buy). Legs default to "to buy" (red). An optional company is appended after a `#`, e.g. `f:FCO-OLB@16:00#Ryanair`. An optional flight booking reference (6-char alphanumeric) is appended last after a `~`, e.g. `f!:FCO-OLB@16:00#Ryanair~AB12CD`. An upcoming flight stays red until it is booked AND has a valid `~ref` (so existing seed flights, which have none, show red); past flights stay green when booked even without a ref.
- **events** — slots joined by `|`, each `cat:text`. Split on the first `:`, so times like `18:30` in the text are fine (`appt:Parrucchiere 18:30`).

`parseSeed()` turns this into `DEFAULT_DATA`. `loadData()` overlays stored edits.
`saveData()` stores **only days that differ from the default**, so future SEED
updates flow through to untouched days. The current SEED covers **2026-06-01
through 2026-12-31** (214 days). Add months by appending lines.

## Key functions

- `renderMonth()` — dispatcher: sets the month label, then calls `renderList()` or `renderGrid()` per `viewMode`, plus `renderSummary()`.
- `renderList(daysInMonth, tk)` — the agenda day rows (full chips, `(Milano)` under the date, holiday badge, pink Marta dot); `renderGrid(daysInMonth, tk)` — the Mon-first month grid (location-tinted compact cells, `(MI)`, holiday-tinted number, Marta dot). `setViewMode('list'|'grid')` toggles and persists under `travelCalendarView`. Day tint comes from `dayColor(dy)` (commute-aware); event dot colours from `catDot(cat)`.
- `renderSummary()` — per-sleep-location day counts, plus **MI Milano** (days with `milano`), **🎫 Tickets** (carnet tickets in the month) and **Marta** tallies (each shown only when non-zero); in the compact card at the bottom.
- `carnetStats()` — one row per carnet with `used` (whole window), `usedToDate` (stops at today), `planned` (`used - usedToDate`), `remaining` / `remainingToDate`, `future` (`date > today`) and `isCurrent` (the last carnet with `date <= today`, else the earliest if all are future). `currentCarnet()` / `upcomingCarnet()` read off it; `addDaysKey(key,n)` / `minKey(a,b)` are the date helpers behind the today cut-off.
- `openDayEditor(key)` / `collectDayFromEditor()` / `saveDayEditor()` — the bottom-sheet editor. `readLegRow(row)` reads a single flight/train row (shared with the move feature). The legend and per-row event swatches are colour-driven by `evCatColor`.
- `moveLegToDay(dateInput)` — moves one flight/train leg to the date picked in its row's hidden `.move-date` input (📅 Move button); appends to the target day, removes the source row, commits + syncs both days.
- `openBulk()` / `applyBulk(clearMode, opts)` / `closeBulk()` / `hideBulkModal()` — the Bulk edit sheet; `closeBulk()` calls `applyBulk(false, { silent: true })` before hiding so **Done** saves pending changes (not just **Apply to range**); `dateRangeKeys(a,b)` builds the inclusive key list; `bulkLocChipsHtml`/`renderBulkChips` render the city quick-chips.
- `shiftMonth(delta)` — month nav, also called by the swipe handler on `.container`.
- Colours: `cityColor`/`evCatColor`/`dayColor`/`catDot`/`darken` (see Colour system); `LOC_COLORS`/`DEFAULT_COLORS`/`EV_COLORS_DEFAULT` defaults; `IT_HOLIDAYS`/`holidayName` for holidays.

## Common edits

- **Change the plan defaults**: edit the `SEED` string. Add new days as new lines.
- **Add a location quick chip**: edit `KNOWN_LOCS` (the seed defaults) and optionally `LOC_COLORS`. At runtime the city list is editable in-app and stored in `localStorage` under `travelCalendarLocs` (`getLocs`/`setLocs`/`addLoc`/`removeLoc`); it falls back to `KNOWN_LOCS`.
- **Add an event category**: add to `EVENT_CATS`, give it a default colour in `EV_COLORS_DEFAULT`, and a short label in `LEG_SHORT` (the editor legend). It is then editable in the 🎨 modal automatically.
- **Add/adjust a holiday**: edit `IT_HOLIDAYS` (`MM-DD` → label).
- **Events / travel legs**: unlimited. Rows are added via the `+ Add` buttons (`legRowHtml`/`evRowHtml`) and removed via the `.row-del` × (delegated on `dayBody`). `collectDayFromEditor()` reads every `.leg` / `.evrow` in the DOM. A leg can be moved to another day with its **📅 Move** button (`moveLegToDay`).
- **Bulk-edit a range**: 📋 **Bulk** in the header (`openBulk`/`applyBulk`); set Marta / Milano / location / an event across a from–to range, or **Clear range**.
- **Change the app icon**: edit the `svg` string in the head `<script>` that builds the favicon / apple-touch / manifest.
- **Promote a device's edits to defaults**: read the DB via the REST API (see "Debugging sync"), grab the `days` node, and convert it into `SEED` lines.
