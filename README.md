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

kpm fetches `registry.toml` over unauthenticated HTTPS (raw file) and
downloads release assets via the unauthenticated forge API. While this repo is
**private**, on-device `kpm registry refresh` and updates of packages hosted in
private repos will not resolve — make the relevant repos public, or run kpm
from a host that supplies credentials.
