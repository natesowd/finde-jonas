# Finde Jonas!

A browser-based teachable-machine exhibit (Preact + Vite + TensorFlow.js). Visitors train an image classifier to spot a person, then run it over the bundled security-camera images. Everything runs in the browser; there is no backend.

Production URL: <https://ikido.info/finde-jonas/>

## Requirements

- Node.js 20 or newer (tested with Node 22) and npm
- Internet access at runtime: the MobileNet model is downloaded from `tfhub.dev` on every page load (see `src/lib/machineLearning.ts`)

## Build a deployable `dist/` from a fresh clone

```bash
npm ci
npx vite build --base=/finde-jonas/
rm dist/assets/*.map
```

- `npm ci` installs the exact versions from `package-lock.json`.
- `--base=/finde-jonas/` is required because the site lives in the `/finde-jonas/` subfolder. Without it the page loads blank (all asset URLs would point at the domain root). Use `--base=/` if it is ever served from a domain root, or `--base=./` to make it work from any folder.
- The build writes a ~15 MB hidden source map; deleting it keeps the readable source off the public server. Skip that step if you want it.

The result is roughly 2.6 MB of static files in `dist/`.

## Deploy to Apache

Copy the **contents** of `dist/` into the `finde-jonas/` folder under the Apache document root, so that `index.html` is served at `https://ikido.info/finde-jonas/index.html`. No `.htaccess`, rewrite rules or server-side code are needed.

Optional: enable `mod_deflate` and long cache headers for `finde-jonas/assets/` (file names are content-hashed, so long caching is safe).

## Local development

```bash
npm install
npm run dev
```

Opens on <http://localhost:5173/>. A "Load MBN" debug button appears in dev mode only.

## Troubleshooting

- **`npm install` fails with `EACCES` / "Your cache folder contains root-owned files"**: your npm cache was written by `sudo` at some point. Fix it once with `sudo chown -R "$(id -u)":"$(id -g)" ~/.npm`, then retry.
- **Stuck on "App wird geladen…"**: the model download failed. Check the browser console and the visitor's internet connection.
- **"WebGL is not supported" in the console**: TensorFlow.js falls back to the CPU; the app still works, just slower.
