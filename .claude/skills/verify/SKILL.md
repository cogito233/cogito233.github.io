---
name: verify
summary: Build and drive this Jekyll academic site locally.
description: Verify homepage, publication pages, links, and static CV artifacts through a localhost Jekyll server.
---

# Verify the Jekyll site

1. Install the locked gems outside the system Ruby path if needed:
   `BUNDLE_PATH=/private/tmp/cogito233-bundle bundle install`
2. Build with:
   `BUNDLE_PATH=/private/tmp/cogito233-bundle bundle exec jekyll build`
3. Serve with local URL overrides so collection links stay on localhost:
   `BUNDLE_PATH=/private/tmp/cogito233-bundle bundle exec jekyll liveserve --config _config.yml,_config.dev.yml --host 127.0.0.1 --port 4000`
4. Drive affected routes over `http://127.0.0.1:4000`, including `/`, `/publications/`, individual collection permalinks, and static files under `/files/`.
5. Check rendered links, not just source. Without `_config.dev.yml`, `base_path` uses the production `site.url`, so publication-card links point to GitHub Pages rather than localhost.
6. Treat the GitHub Metadata missing-auth warning as environmental unless rendered content depends on missing metadata.
