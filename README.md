# Social Home pinned viewer

This repo serves the page that opens **public share links** for
[Social Home](https://github.com/social-home-io/socialhome) highlights and
moments, at **https://viewer.social-home.io**.

## Why a separate origin

A share link carries its decryption key after `#` (`#k=…`), the part of a URL
browsers never send to a server. The page that reads that key must not be
served by a GFS: a GFS that could change the page's script could read the
key. So the viewer lives on its own origin, built and published only from
Social Home releases.

## How a version gets here

Nothing pushes into this repo from outside. The
[`Publish the pinned viewer`](.github/workflows/publish.yml) workflow (daily,
or by hand):

1. picks a Social Home release (or a commit on its `main`);
2. checks that version out and rebuilds the viewer
   (`pnpm run build:pinned-viewer`);
3. compares the build with that release's `socialhome/pinned_viewer.json`,
   which names the content-addressed path `v/<hash>/` and the sha384 of
   `viewer.js`, and stops if they differ;
4. adds `v/<hash>/` to the `gh-pages` branch, which GitHub Pages serves.

Every version is built twice, by Social Home's CI and here, and the two must
match. Versions are only ever added; an existing `v/<hash>/` never changes,
so links minted by every earlier release keep opening.

## Settings (for org admins)

- Pages: source `gh-pages` / root, custom domain `viewer.social-home.io`,
  Enforce HTTPS. DNS: `CNAME viewer → social-home-io.github.io`, **DNS only**
  (no proxy), so GitHub issues the certificate and nothing rewrites the files.
- Ruleset on `gh-pages`: block force-push and deletion.

## License

[MPL-2.0](LICENSE), like Social Home.
