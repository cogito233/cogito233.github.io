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

## Fallback when system Ruby cannot build native gems (verified 2026-09-15)

On this Mac the system Ruby 2.6 fails to compile `commonmarker 0.17.13`, so the
`BUNDLE_PATH` route above dies at `bundle install`. Build in the GitHub Pages
Docker image instead; it matches the production toolchain and needs no local gems:

```
docker run --rm --platform linux/amd64 \
  -e JEKYLL_NO_BUNDLER_REQUIRE=true -e JEKYLL_ENV=production \
  -v "$PWD":/srv/jekyll jekyll/jekyll:pages \
  jekyll build --config _config.yml -d /srv/jekyll/_site_docker
```

`JEKYLL_NO_BUNDLER_REQUIRE` is required: without it Jekyll reads the pinned
`Gemfile.lock` and fails on gems the image does not carry. Do not mount anything
over `/usr/local/bundle`; that is where the image keeps `jekyll`. Output lands in
`_site_docker/` (git-ignored). For a local preview, serve it with
`python3 -m http.server 4000 -d _site_docker` and note that links point at the
production `site.url`.
