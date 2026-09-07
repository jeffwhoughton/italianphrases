# Parla! — Italian travel phrasebook (PWA)

An offline-first progressive web app: 130 travel phrases in 10 categories, each with
English, Italian, a plain-English pronunciation guide, and a button that speaks the
Italian out loud.

## Files

```
index.html              the whole app (HTML + CSS + JS + phrase data, one file)
sw.js                   service worker — makes it work with no signal
manifest.webmanifest    makes it installable on the Android home screen
icons/                  app icons (192, 512, maskable, apple-touch)
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

A PWA has to be served over **https** to be installable and to run offline, so the
folder needs to live on a host. Any of these works — pick one:

**Netlify Drop (fastest, no account needed to start)**
1. Go to https://app.netlify.com/drop
2. Drag this whole `italian-phrases` folder onto the page
3. You get a URL like `https://something-random.netlify.app` — open it on your phone

**GitHub Pages (free, permanent)**
1. Create a repo, push these files to it
2. Settings → Pages → Source: `main` / root
3. Your app is at `https://<user>.github.io/<repo>/`

**Cloudflare Pages** — same idea: upload the folder, get an https URL.

Then on the phone, in Chrome: **⋮ menu → Add to Home screen** (or *Install app*).
It opens full-screen with no address bar, and the service worker keeps a copy of
everything on the device, so it loads with the phone in airplane mode.

Open it once on wifi before you leave so the service worker can cache it.

## Using it

- **Speaker button** — say the phrase at normal speed
- **½×** — say it slowly, for practising or for someone who didn't catch it
- **☆** — pin a phrase to the **Saved** tab (survives closing the app)
- **Search** — matches English, Italian, or the pronunciation, and ignores accents
- **Settings → Speaking about yourself** — switches gendered phrases between
  *"Sono vegetariano"* and *"Sono vegetariana"*
- **Settings → Speaking speed** — the default 0.90× is a little slower than a
  native speaker, which is easier for a waiter to parse from a phone speaker

## Editing the phrases

All 130 phrases live in one `const DATA = [...]` array near the top of the
`<script>` block in `index.html`. Each entry looks like:

```js
{en:"I am vegetarian", it:"Sono vegetariano", ph:"SOH-noh veh-jeh-tah-RYAH-noh",
 f:{it:"Sono vegetariana", ph:"SOH-noh veh-jeh-tah-RYAH-nah"},
 note:"optional tip shown under the phrase"}
```

`ph` is the pronunciation guide — CAPITALS mark the stressed syllable. `f` is the
feminine form and is optional. To add a category, copy one of the
`{id:…, en:…, it:…, items:[…]}` blocks.

After editing, bump `const VERSION = 'parla-v1'` in `sw.js` so installed phones
pick up the new version instead of serving the cached old one.
