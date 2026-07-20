# kobo-registry

A package registry for [kpm](https://github.com/wolffshots/kpm), the Kobo
e-reader package manager. [`registry.toml`](registry.toml) lists package
definitions that kpm pulls with `kpm registry` / `kpm search` / `kpm install`
/ `kpm sync`.

The registry contains **definitions only** — forge coordinates, asset names,
and uninstall recipes. The actual `KoboRoot.tgz` binaries are downloaded from
each package's own release page.

**Browse the packages at
[wolffshots.github.io/kobo-registry](https://wolffshots.github.io/kobo-registry/)** —
the site renders this repo's `registry.toml` live.

## Use

```sh
kpm registry add github.com/wolffshots/kobo-registry
kpm registry refresh
kpm search
kpm install nickelhardcover
```

## Adding a package

Add a `[packages.<id>]` table to `registry.toml` (id must be `[a-z0-9-]+`):

```toml
[packages.example]
name   = "Example"
source = "github.com/owner/repo"   # or codeberg.org/owner/repo
forge  = "github"                  # "github" | "forgejo"
asset  = "KoboRoot.tgz"            # release asset name; glob allowed

  [packages.example.uninstall]     # optional; see kpm's UNINSTALL.md
  method      = "manifest"
  purge_paths = ["/mnt/onboard/.adds/Example/**"]
```

`schema_version = 1` is required. Do not include `pin` — pins are a local
decision and are never distributed. See the
[kpm README](https://github.com/wolffshots/kpm#readme) for the full format.

## Note on visibility

This registry is **public**, so `kpm registry refresh` resolves on-device
without credentials. kpm fetches `registry.toml` over unauthenticated HTTPS and
downloads release assets via the unauthenticated forge API, so each package it
lists must *also* live in a public repo to be installable. Every package
currently listed (including kpm itself) lives in a public repo.
