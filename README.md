# Motherboard AR Scanner — Setup Guide

## Enna panna poringa (overview)

1. Un motherboard oda **oru clean top-down photo** eduka.
2. Adha photo ah **MindAR "target file" (.mind)** ah convert panna — idhu oru free online tool.
3. Component boxes (CPU socket, RAM slots etc) oda position ah un photo ku match aagura madhiri **adjust** panna.
4. **GitHub Pages** la free ah host panna.

Total time: ~30-45 mins for first setup, konjam trial-and-error irukum box positions ku.

---

## Step 1 — Motherboard Photo Eduka

- Nalla lighting la, top-down (directly mela irundhu), motherboard **muழுசா** frame la varra madhiri photo eduka.
- Shadows/glare minimize pannunga.
- Same angle la than nee scan pandra podhum work aagum (idhu image tracking, 3D object tracking illa) — so andha exact same photo angle la than best results varum.
- File ah `board-photo.jpg` nu save pannunga.

## Step 2 — Target File (.mind) Compile Panna

1. Poidunga: **https://hiukim.github.io/mind-ar-js-doc/tools/compile**
2. Adhula un `board-photo.jpg` ah upload pannunga.
3. "Start" click pannunga — konjam nerathula processing aagum.
4. Download button vandhathum, `targets.mind` file ah download pannunga.
5. Andha file ah copy panni `motherboard-ar/assets/targets/targets.mind` nu vekkanum (already irukura placeholder ah replace pannunga).

> **Note:** Ithu image ah "feature points" ah convert pannum (edges, corners, patterns) — adhu than camera la board ah recognize panna use aagum.

## Step 3 — Local ah Test Panna

AR camera access ku **HTTPS mandatory** (localhost mattum exception). Terminal la:

```bash
cd motherboard-ar
npx http-server -S -C cert.pem -K key.pem -p 8080
```

Adhukku mun oru self-signed cert generate pannunga (one-time):

```bash
openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem -days 365 -nodes -subj "/CN=localhost"
```

Alladhu **easiest way**: neenga anyway GitHub Pages ku deploy pannitu, adha URL vechu directly phone la test pannalam (Step 5 paarunga) — HTTPS automatic ah kedaikum.

## Step 4 — Component Positions Adjust Panna (Important!)

`index.html` file open pannunga, `<a-box>` tags theduga. Ovvoru box ku:

```html
<a-box class="clickable" data-name="CPU Socket"
  data-desc="..."
  position="-0.05 0.10 0.02" width="0.10" height="0.10" depth="0.005"
  ...>
```

**Coordinate system:**
- Target image oda center = `(0, 0, 0)`
- `x`: left(-) → right(+), full width = `1` unit
- `y`: bottom(-) → top(+), full height = `image_height / image_width` units (example: 4:3 photo na height ≈ 0.75)
- `z`: chinna positive value (0.01–0.03) vechu box ah board surface la irundhu konjam mela lift pannunga (illa na board texture kooda clip aaipom)

**Iteration process** (Unity la scene tweak panra madhiri than):
1. Phone la app open pannunga, board ah scan pannunga.
2. Box eppadi irukku nu paarunga — correct component mela irukka?
3. `index.html` la andha box oda `position`/`width`/`height` values ah edit pannunga.
4. Refresh pannunga (GitHub Pages na push pannitu wait pannanum, so local testing use pannunga idhukaga if possible).
5. Repeat till ella boxes um correct place la varum.

Idhu trial-and-error dhan — 6 boxes ku maybe 15-20 mins eduthukum.

## Step 5 — GitHub Pages la Deploy Panna (FREE Hosting)

1. GitHub account illana create pannunga (free): https://github.com/signup
2. New repository create pannunga — example name: `motherboard-ar`
3. Andha `motherboard-ar` folder (idhula irukura ella files um — `index.html`, `assets/` folder mothamum) andha repo ku push pannunga:

```bash
cd motherboard-ar
git init
git add .
git commit -m "Motherboard AR scanner"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/motherboard-ar.git
git push -u origin main
```

4. GitHub repo la **Settings → Pages** ku poidunga.
5. "Source" → **Deploy from a branch** → Branch: `main`, folder: `/ (root)` → Save.
6. 1-2 mins wait pannunga. Un app URL kedaikum:
   `https://YOUR_USERNAME.github.io/motherboard-ar/`

7. Andha URL ah phone browser la (Chrome, Android) open pannunga, camera permission allow pannunga, board ah scan pannunga!

> iPhone Safari lum work aagum, aana Android Chrome la better AR camera performance kedaikum.

## Adding More Components

Puthusa component add panna, `index.html` la existing `<a-box>` block ah copy-paste panni, `data-name`, `data-desc`, `position`, `width`, `height` mattum change pannunga. Adhu than — automatic ah clickable aayidum, JS code already ella `.clickable` boxes kum listen pannudhu.

## Common Issues

| Problem | Fix |
|---|---|
| Camera open aagala | HTTPS illa nu check pannunga, browser permission check pannunga |
| Board detect aagala | Lighting change pannunga, photo angle exact ah match panna try pannunga, target photo la enough "texture/detail" irukka nu check pannunga (plain/blank boards mostly detect aagathu) |
| Boxes wrong place la irukku | Step 4 iteration process follow pannunga |
| GitHub Pages 404 | `index.html` root la than irukanum (subfolder la illa), Settings → Pages la branch correct ah select pannirukeenga nu check pannunga |

## Future Upgrades (later, if you want)

- **Multiple motherboard models**: MindAR ஒரே target file la multiple images support pannum (`targetIndex: 0, 1, 2...`) — different board scan panna different info kaata mudiyum.
- **Better graphics**: A-Frame la GLTF 3D models (arrows, icons) import pannalam plain boxes ku pathila.
- **Voice/audio**: Un existing Unity projects la irukura audio cue pattern (`PlayAudio()`) madhiri, ivlolayum Web Audio API vechu component click aana audio play pannalam.
