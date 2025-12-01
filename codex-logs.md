# Codex Session Log — lyrical-miracles-v2

## Initial Goals
- Clean up the original lyrical-miracles repo into a structured v2 layout (content/data/media/platform).
- Set up Cloudflare deployment scripts: R2 uploads, D1 schema seeding, Vectorize/AI ingestion.
- Prepare infra for automatic CMS updates and future RAG/chatbot integration.

## Key Work Completed
1. **Repo Restructure**
   - Created `lyrical-miracles-v2/` with clear directories (`content/`, `media/`, `data/`, `platform/`, etc.).
   - Copied all HAWK-ARS assets and docs into the new structure; added pipeline documentation and structure docs.

2. **Automation Scripts**
   - `scripts/normalize_assets.py`: slug enforcement + asset map.
   - `scripts/build_manifest.py`: aggregates content/media/catalog metadata.
   - `scripts/publish_cloudflare.sh`: manifests, R2 uploads, D1 schema + seed (uses `lyrical-env.sh`).

3. **Vectorize / Worker Path**
   - Added Cloudflare Vectorize index (`lyrical-miracles`) and ingestion CLI (`scripts/autorag/ingest.py`).
   - Deployed `platform/vectorize-worker/` exposing `/ingest` (embeddings + upserts) and `/search` (query endpoint).
   - CLI now supports `--worker-url` to post chunk batches via the Worker.

4. **Docs & Env Updates**
   - `docs/PIPELINE.md`, `docs/EverLights_autorag_guidebook.md` describe the end-to-end workflow (normalize → manifest → R2 → D1 → Vectorize ingest/query).
   - `lyrical-env.sh` / `.example` contain all required env vars (CF account/token, R2 bucket, D1 DB, Vectorize worker URL, AI model).

5. **GitHub Sync**
   - Repo committed and pushed to `https://github.com/OmniversalMediaLLC/lyrical-miraclesv2.git`.
   - Large binaries excluded via `.gitignore`; Worker + scripts are committed.

## Current State
- `dist/manifest.json` generated with 200+ content entries.
- D1 database (`lyrical-miracles`) seeded via publish script.
- R2 bucket `lyrical-miracles-v2` holds audio/images (synced manually earlier).
- Vectorize index populated via Worker ingestion (`CF_VECTORIZE_WORKER_URL=https://lyrical-vector-ingest.omniversalmail.workers.dev/ingest`).
- Worker `/search?q=...` returns matches for site search/chat.

## Next Steps
1. **Workers API / CMS Exporters**
   - Create Worker endpoints that serve structured data (tracks/releases) for WordPress/Ghost/Drupal or static builds.
   - Implement exporters under `platform/exporters/` to push manifest data into each CMS.

2. **Front-End Wiring**
   - Build a search/chat widget (using `/search`) and integrate into the site.
   - Wire CMS/site to the new API so lyric pages auto-populate.

3. **CI/CD**
   - Add GitHub Actions to run `normalize_assets`, `build_manifest`, optional tests, and maybe auto-trigger ingestion or Worker deploys.

4. **Further AI Features**
   - Expand Worker `/search` to include more filters or combined QA.
   - Add chat endpoint (RAG) using Vectorize matches + Workers AI text generation.

## Notes
- To rehydrate on a new machine/server, simply `git clone https://github.com/OmniversalMediaLLC/lyrical-miraclesv2.git`, copy `.example` env to `lyrical-env.sh`, fill secrets, then run the helper scripts.
- Worker deploy commands live in `platform/vectorize-worker`; remember to redeploy after changes (`wrangler deploy`).
- The ingestion CLI (`scripts/autorag/ingest.py`) requires `requests` and Python 3.10+.

