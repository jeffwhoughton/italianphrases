# Parla! — Italian travel phrasebook (PWA)

An offline-first progressive web app: 43 travel phrases in 5 sections, laid out two cards
to a row. Each card shows the English and the Italian; tapping it speaks the Italian out
loud.

## Files

```
index.html              the whole app (HTML + CSS + JS + phrase data, one file)
sw.js                   service worker — makes it work with no signal
manifest.webmanifest    makes it installable on the Android home screen
icons/                  app icons (192, 512, maskable, apple-touch)
.nojekyll               tells GitHub Pages to serve the files as-is
```

## How the audio works

The app uses the **Web Speech API**, which on Android hands the Italian text to
Google's built-in text-to-speech engine. No audio files to download, no licensing
problems, and every phrase can be replayed at normal or half speed.

**Do this before you fly** (it's the one thing that needs internet):

1. Android **Settings → System → Languages & input → Text-to-speech output**
2. Tap the gear next to **Speech Services by Google** → **Install voice data**
3. Choose **Italiano (Italia)** and download it
4. Open the app → gear icon → press **Test**. It should say
   *"Buonasera! Sono canadese e sono vegetariano."*

Once that voice pack is on the phone, speech works in airplane mode. The app's
Settings panel shows which voice it found and whether it is offline-ready.

## Putting it on your phone

Configured for **https://jeffwhoughton.github.io/italianphrases/** — the manifest's
`scope`, `start_url` and `id` all point at that sub-path, so publish it to a repo
named `italianphrases` under your account.

```bash
cd C:\Users\Jeff\Documents\italian-phrases
git init -b main
git add .
git commit -m "Parla! Italian phrasebook"
git remote add origin https://github.com/jeffwhoughton/italianphrases.git
git push -u origin main
```

Then in the repo: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.
Give it a minute and it's live at https://jeffwhoughton.github.io/italianphrases/

(The `.nojekyll` file in this folder stops GitHub's Jekyll build from touching the files.)

On the phone, open that URL in Chrome → **⋮ menu → Add to Home screen** (or
*Install app*). It opens full-screen with no address bar, and the service worker keeps
a copy of everything on the device, so it loads with the phone in airplane mode.

Open it once on wifi before you leave, so the service worker can finish caching.

**If you ever move it to a different address**, change the three `/italianphrases/`
paths in `manifest.webmanifest` and the `canonical`/`og:` tags in `index.html` to match.

## Using it

- **Tap a card** — speaks the Italian. The whole card is the button, so it's hard to miss
  on a moving train. Tapping the card that's currently talking stops it.
- **☆** — pins a phrase to **Saved** at the top of the list. A pinned phrase moves there
  rather than being copied, so it only ever appears once.
- **The chips in the header** stay on screen at all times and scroll you to a section; the
  one you're currently in is highlighted. Once you scroll down, the title and gear button
  fold away and only the chip strip remains, so the list gets more of the screen. Scroll
  back to the top to bring them back.
- **Settings → Speaking about yourself** — switches gendered phrases between
  *"Sono vegetariano"* and *"Sono vegetariana"*.
- **Settings → Speaking speed** — defaults to 0.70×, well under native pace, which is what
  makes it intelligible from a phone speaker in a noisy room.

## Editing the phrases

All 43 phrases live in one `const DATA = [...]` array near the top of the `<script>` block
in `index.html`. Each entry looks like:

```js
{en:"I am vegetarian", it:"Sono vegetariano",
 f:"Sono vegetariana",          // optional: the feminine form for the gender toggle
 note:"short tip shown under the phrase",   // optional, keep it to a few words
 wide:true}                     // optional: card spans both columns, for long lines
```

Each section also carries a `short:` label — that's what the header chip shows, and it has
to stay short enough that all the chips fit one row on a 320px phone.

To add a section, copy one of the `{id:…, en:…, it:…, items:[…]}` blocks. The header chips,
the phrase count in the footer and the scroll-spy all derive from `DATA`, so nothing else
needs updating.

Notes cost vertical space in a two-column layout — a card with a note is roughly a third
taller than one without — so only add one where it changes what you'd say.

After editing, bump `const VERSION = 'parla-v9'` in `sw.js` so installed phones pick up the
new version instead of serving the cached old one.
