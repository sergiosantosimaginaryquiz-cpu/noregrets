# DOM: The Final Mission

A cinematic, first-person website for Dom's stag weekend in Barcelona. One file: `index.html`. HTML, CSS and JavaScript only, with Three.js (r128) and GSAP (3.12.2) loaded from cdnjs.

## What's in it

| Chapter | What happens |
|---|---|
| 01 Terminal | First-person London terminal: window wall, parked and taxiing aircraft, passengers, moving clouds, floor reflections, departures board. Title, "Enter the mission", "Skip to the itinerary". |
| 02 Departures | Camera walks to a split-flap board that flips to the outbound flight from the config. Readable intel panel beside it. |
| 03 Boarding pass | 3D souvenir pass that unfolds; "Save keepsake image" renders a PNG. Clearly marked *not a travel document*. |
| 04 Boarding | Camera walks through the jet bridge, the aircraft door slides open, white-out into the cabin. Skippable. |
| 05 Takeoff | Window-seat view with the wing in shot: runway lights stream past, rotation, London (Thames, Shard, Eye) falls away, climb through the cloud deck. Skippable. |
| 06 The flight | Above the clouds, flight-path HUD and upcoming missions. |
| 07 Barcelona | Descent through cloud over the coast, Eixample grid, Sagrada Família, Torre Glòries, Montjuïc, then down into street level. "Welcome to Barcelona. The mission starts now." |
| 08 Missions | Dusk mission map with a beacon per mission. Selecting one flies the camera there, changes the mood (day / golden / dusk / night) and opens the details panel. |
| 09 The squad | Night orbit with the mission roster. Copy-to-WhatsApp trip summary. |
| 10 Return | Return-flight intel, an aircraft taking off over the sea. |
| 11 Finale | Night pull-back, chapter montage, "Mission complete." Trip poster image. |

Always available: chapter rail (prev/next, jump to any chapter), **Trip info** dossier (itinerary, flights, stay, costs, squad, links & docs, emergency), "Next up" chip, sound toggle, settings (motion, quality, organiser view).

## Built for phones first

- Bottom tab bar in thumb reach: **Journey** (chapter list), **Plan** (itinerary with a "Next up" card, flights, stay, costs, links), **Map** (mission map), **Squad**, **SOS** (112, squad contacts, hotel address).
- Story-style progress bar along the top; swipe left/right to move between chapters, or use ‹ › next to the chapter name.
- Chapter info sits in bottom sheets with the 3D scene above. Flight details show as four tiles (flight, date, departs, meet) with the rest one tap away.
- Mission details open as a bottom sheet: drag the handle down to close, tap it to make the sheet taller.
- Light haptic taps on Android, sharper rendering on high-density screens, safe-area aware on notched phones, and a sideways layout for landscape.
- Add to Home Screen gives it a full-screen app look with its own icon.
- On a laptop the same layout centres the tab bar and docks the panels to the left.

## Editing the trip

Everything trip-specific lives in one block near the bottom of `index.html`, starting with `const TRIP = {`. Nothing else needs touching.

- Leave unknown values as `null`. Empty mission fields are hidden; core flight fields show **TBC**.
- Dates `"YYYY-MM-DD"`, times `"HH:MM"` (local time of where that thing happens).
- Mission `status`: `"confirmed"`, `"tentative"` or `"to-organise"` (drives the colour of the beacon and badge).
- Mission `type` sets the scene mood: `arrival`, `hotel`, `restaurant`, `beach`, `nightlife`, `activity`, `return`.
- Mission `district` places the beacon: `airport`, `eixample`, `sagrada`, `gracia`, `gothic`, `born`, `barceloneta`, `beach`, `olimpic`, `poblenou`, `port`, `montjuic`, `city`. Positions are approximate, so the page says so until venues have addresses.
- `surprise: true` keeps a mission **Classified** for the squad. Organisers can reveal it on their own device under Settings → Organisers.
- `address` or `mapUrl` adds an "Open in Maps" button.
- `image` (a path or URL to a photo you own) shows in the mission panel; `photo` does the same for squad members. A missing image is simply skipped.
- Set `isPlaceholder: false` once the real plan is in. That removes the "Placeholder plan" banner.
- All headline copy (arrival lines, finale lines, the seat joke, the joke rows on the departures board) is under `copy` and `board`.

The eight missions that ship are **examples only** and are labelled as such. No flights, venues, prices or times have been invented.

## Running locally

Open `index.html` in a browser, or serve the folder:

```
npx serve .        # or: python3 -m http.server 8000
```

No build step and no dependencies to install.

## Deploying (static hosting)

Any static host works. Two quick options:

- **Netlify Drop**: drag the folder onto app.netlify.com/drop. You get a public URL immediately.
- **GitHub Pages**: push the folder to a repo, then Settings → Pages → deploy from the main branch.

Then send the URL to the group chat. For a rich WhatsApp preview, add a 1200×630 image (for example `og.jpg`) next to `index.html` and add `<meta property="og:image" content="https://your-site/og.jpg">` in the head. Open Graph images must be absolute URLs, so this can only be set once you know the address.

No environment variables, API keys or backend are used.

## Assets

Everything visual is generated in code: geometry, sky and sea shaders, cloud sprites, textures drawn on canvas, and sound synthesised with Web Audio. There are no image, model, audio or video files to host, so nothing can fail to load except the two CDN libraries and Google Fonts.

| Asset | Source | If it fails |
|---|---|---|
| Three.js r128 | cdnjs | Page switches to the 2D cinematic mode; all information still works |
| GSAP 3.12.2 | cdnjs | Required for animation; self-host `gsap.min.js` if your network blocks cdnjs |
| Fonts (Big Shoulders Display, Space Grotesk, Inter) | Google Fonts | System fonts are used |
| Mission / squad photos (optional) | Your own files | Skipped |

## Performance and accessibility

- Quality tiers: **Full 3D**, **Lite 3D** (picked automatically on phones and low-memory devices: fewer clouds and passengers, no antialiasing, pixel ratio 1) and **2D** (no WebGL). If frame rate drops in Full mode the page switches itself to Lite.
- London loads first; the flight and Barcelona worlds build in the background after the opening is on screen.
- Reduced motion (system setting or Settings → Motion) replaces camera flights with quick cuts and skips the long sequences.
- Every sequence has a Skip button. Arrow keys / Page Up / Page Down move between chapters, `I` opens the itinerary, `Esc` closes panels. Scrolling and swiping also move between chapters, but never lock the page.
- Sound never autoplays. The choice is remembered for the session.
- Deep links: `#itinerary`, `#sos`, `#missions`, `#squad`, `#finale`.

## What doesn't exist (by design)

- **No live flight status.** Status is whatever the organisers type into the config.
- **No sync between phones.** The page is read-only; changes reach everyone when the updated file is re-published.
- **Keepsake downloads** work as a normal download on a self-hosted site. Inside the claude.ai artifact viewer, downloads are blocked, so the image is shown full-size to press-and-hold or right-click to save.

## Tested

Checked in headless Chromium (software WebGL) at 390×844 (phone, the main target) and 1440×860: every chapter, the boarding pass, a mission panel, the dossier tabs, and the 2D fallback with Three.js blocked. No script errors. Not yet tested on real iOS Safari or Android hardware, and frame rates on a real GPU were not measured.
