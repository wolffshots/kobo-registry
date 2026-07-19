# kobo-registry

A package registry for [kpm](https://github.com/wolffshots/kpm), the Kobo
e-reader package manager. [`registry.toml`](registry.toml) lists package
definitions that kpm pulls with `kpm registry` / `kpm search` / `kpm install`
/ `kpm sync`.

The registry contains **definitions only** — forge coordinates, asset names,
and uninstall recipes. The actual `KoboRoot.tgz` binaries are downloaded from
each package's own release page.

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
decision and are never distributed. See kpm's
[REGISTRY.md](https://github.com/wolffshots/kpm/blob/main/REGISTRY.md) for the
full format.

## Note on visibility

This registry is **public**, so `kpm registry refresh` resolves on-device
without credentials. kpm fetches `registry.toml` over unauthenticated HTTPS and
downloads release assets via the unauthenticated forge API, so each package it
lists must *also* live in a public repo to be installable:

- **nickelhardcover** — public (Codeberg); resolves fine.
- **kpm** — currently a **private** repo (`github.com/wolffshots/kpm`), so
  installing or updating kpm through this registry will not resolve until that
  repo is made public (or kpm gains authenticated-fetch support).
