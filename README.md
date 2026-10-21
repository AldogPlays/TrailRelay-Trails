# TrailRelay community trails

GPX routes and trail metadata for the [TrailRelay Android app](https://github.com/AldogPlays/TrailRelay). The app downloads this catalog, searches it locally, and saves selected GPX files for offline use.

This repository contains trail data. For app downloads, screenshots, and navigation features, visit [TrailRelay](https://github.com/AldogPlays/TrailRelay).

## Catalog

GitHub Pages serves the [live catalog](https://aldogplays.github.io/TrailRelay-Trails/catalog.json). Each entry links to a GPX file under `trails/<trail-id>/trail.gpx`.

The catalog is a static set of files; no account or API key is needed to read it.

## Contributing a trail

Open an issue or pull request with a GPX file you have permission to share. Include the trail name, source or attribution, and any known access restrictions. Difficulty and vehicle access need supporting information; a GPX track alone does not establish that a route is open or legal to drive.

The maintainer will review the route geometry and metadata before publication.

## Repository structure

```text
catalog.json
trails/
  <trail-id>/
    trail.gpx
    trail.json
```

GPX is the source for route geometry. Each `trail.json` stores metadata, and `catalog.json` lists the routes consumed by the app. Keep trail IDs stable and preserve the existing schema so older app versions can read the catalog.

The `state` field uses a two-letter U.S. state code. The `region` field lists the county, or alphabetized slash-separated counties, containing the route. Maintainer validation and publication steps are documented in [AGENTS.md](AGENTS.md).

## Before heading out

Route metadata may be incomplete or out of date. Check land access, closures, and local conditions independently, and verify your downloaded GPX and map coverage before losing connectivity. A listed route is not a guarantee of legal access or safe passage.
