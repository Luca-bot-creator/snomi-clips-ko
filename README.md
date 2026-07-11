# snomi-clips-ko — Korean clip CDN (staging)

**Separate from Japanese** `n1lfy/snomi-clips` / `snomi.net`. Korean MP4s use the
`ko_clip_{N}.mp4` naming convention only — never `clip_{N}.mp4`.

## Repo layout

```
snomi-clips-ko/
├── README.md
├── clip-hosts-ko.json      # mirror list (app fetches when Korean catalog enabled)
├── ko_clip_0.mp4
├── ko_clip_1.mp4
└── …
```

## Create the GitHub repo

```bash
cd snomi-clips-ko
git init
git add README.md clip-hosts-ko.json
git commit -m "init Korean clip staging repo"
gh repo create n1lfy/snomi-clips-ko --public --source=. --remote=origin --push
```

## Upload clips (after bundling)

From the tonka repo, after running `bundle_ko_clips_batch.sh`:

```bash
# Copy MP4s into this repo (do NOT copy to snomi-clips or snomi.net)
cp Tonka/Resources/ClipVideos/ko_clip_*.mp4 snomi-clips-ko/

cd snomi-clips-ko
git add ko_clip_*.mp4 clip-hosts-ko.json
git commit -m "add Korean clip batch N"
git push origin main
```

Git LFS is recommended once the catalog grows beyond a few dozen files:

```bash
git lfs install
git lfs track "*.mp4"
git add .gitattributes
```

## Verify a clip URL

```
https://raw.githubusercontent.com/n1lfy/snomi-clips-ko/main/ko_clip_0.mp4
```

## App integration

- `KoreanClipHostConfig` reads `clip-hosts-ko.json` from this repo.
- `KoreanContentConfig.isCatalogEnabled` must be true (and study language Korean)
  before the app loads CDN clips — defaults **off** in Release builds.
- Japanese `ClipHostConfig` never serves `ko_clip_*` files.

## Production checklist (App Store release)

1. Upload full Korean catalog to this repo (or production R2 bucket under a `ko/` prefix).
2. Set `KoreanCatalogEnabled` = true in Info.plist for the release build.
3. Optionally add a production CDN URL as the first mirror in `clip-hosts-ko.json`.
4. Ship new App Store version — existing installs stay on 3 seed clips until they update.
