# Installing OrthoFlow in Claude Cowork (single-plugin edition)

This repository **is** the built OrthoFlow marketplace, packaged as **one combined `orthoflow` plugin**
(all 60 skills). No build step — push it and add it.

> 🩺 Educational / decision-support tooling — not a medical device, not medical advice. Surgeon sign-off required on every output.

## 1. Push this folder to a private GitHub repo

```bash
git init
git add .
git commit -m "OrthoFlow v0.4.0 (single plugin)"
git branch -M main
git remote add origin https://github.com/<you>/OrthoFlow-marketplace.git
git push -u origin main
```

## 2. Add it in the Cowork desktop app

1. Open **Customize → Plugins**.
2. **Add marketplace** / **Add from a repository**.
3. Enter `<you>/OrthoFlow-marketplace` (or the full git URL); authorize GitHub if prompted.

## 3. Install and test

- Install the single **OrthoFlow** plugin.
- In a chat type `/` and look for skills namespaced `orthoflow:ortho-…`
  (e.g. `orthoflow:ortho-aoota-classification`). Trigger one to test.

## Updating later

Cowork caches by version. To ship a change: bump the version in the source repo's `domains.json`,
rebuild (`scripts/build-marketplace.mjs --single`), push, then **Sync** in Cowork.

---
Generated from the OrthoFlow source repo — do not hand-edit. Contact: **coordinator@maivan.ai** · https://maivan.ai
