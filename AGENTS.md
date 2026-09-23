# TrailRelay-Trails repository guidance

## Repository purpose

TrailRelay-Trails is the public static community trail-data repository used by
the TrailRelay Android application. This is **not** the Android application
repository.

GitHub Pages serves the repository contents. The live catalog is:

`https://aldogplays.github.io/TrailRelay-Trails/catalog.json`

Individual GPX files are served from paths such as:

`https://aldogplays.github.io/TrailRelay-Trails/trails/<trail-id>/trail.gpx`

There is no backend, database, account system, custom trail-management
application, or GitHub API requirement in the Android app. The repository
itself is the community dataset.

The intended structure is deliberately small:

```text
catalog.json
trails/
  <trail-id>/
    trail.json
    trail.gpx
AGENTS.md
README.md
```

Do not add a permanent CLI, TUI, database layer, backend, or management
application for trail contributions. Codex performs the repetitive data work
during the maintenance session. Temporary one-off parsing or validation code
may be used in the session but does not belong in the repository.

## Codex's role

Codex is the maintainer and curator assistant for TrailRelay community trail
data. When asked to add a trail, Codex should inspect the GPX, derive reliable
metadata, ask only for important information that cannot be determined, create
the trail files, update the catalog, validate the result, and present a clear
Git handoff.

The normal workflow is:

1. Start Codex from this repository and describe the requested trail files,
   for example `Add ~/Downloads/cleghorn.gpx as a community trail.`
2. Inspect repository state first with `git status`, `git branch --show-current`,
   and `git log --oneline --decorate -8`.
3. If unexpected uncommitted changes are present, stop and explain them before
   overwriting anything. Changes explicitly requested in the current task are
   expected and may be handled carefully.
4. Create a sensible data branch when needed, following the repository's
   existing branch conventions. Do not silently discard, reset, stash, or
   overwrite unrelated work. Never use `git reset --hard`.
5. Read and parse every supplied GPX before asking questions.
6. Derive as much reliable metadata as possible and ask efficiently for the
   remaining important values. Shared values may be requested once for several
   trails when that is clear.
7. Create `trails/<trail-id>/trail.gpx` and `trails/<trail-id>/trail.json`.
8. Update `catalog.json` without changing the schema casually.
9. Validate every affected trail and the complete catalog.
10. Report what changed and wait for explicit approval before committing or
    pushing.

The user should not normally need to edit JSON, create trail directories,
calculate distance, or update `catalog.json` manually.

## GPX is the primary source

GPX is the canonical trail geometry. Inspect the GPX itself before asking
questions. Preserve useful values when present, including:

- metadata name and description
- track name and description
- metadata and track links
- creator and source information
- keywords and track type
- bounds
- distance calculated from actual track geometry
- track, segment, and track-point counts
- useful elevation information

Use GPX values when reliable. Do not trust obviously malformed values, invent
missing metadata, or edit the geometry merely to make it look cleaner unless
the user explicitly asks for geometry editing.

Validate that the GPX is valid XML with usable `trk`/`trkseg`/`trkpt`
geometry, valid latitude and longitude values, enough points for meaningful
geometry, and a non-empty calculated distance. Preserve multiple tracks and
segments. Distance must not add an artificial jump between separate segments.

Metadata precedence:

- Name: GPX metadata name, then track name, then a cleaned human-readable
  filename, then ask if still ambiguous.
- Description: GPX metadata description, then track description, then ask or
  leave blank when appropriate.
- Source URL: GPX metadata link, then track link, then other clearly embedded
  source information, then ask if useful and unavailable.
- Tags: GPX keywords and type may be used as hints.

Calculate distance from track geometry instead of trusting a manually typed
catalog distance. Generate a stable lowercase URL-safe trail ID from the name
unless an existing stable ID already applies.

## External sources

GPX files may come from AllTrails, OpenTrails, or another source. Preserve a
source URL or source metadata that is actually embedded in the GPX, and it is
fine to recognize an obvious source domain. Do not scrape websites, automate
authentication, bypass access controls, use undocumented private APIs, or make
trail addition depend on an external service.

If the user supplies a public trail-page URL and explicitly asks for its
metadata, publicly available information may be inspected as appropriate.
Otherwise the GPX remains the primary source.

## Trail metadata and compatibility

Inspect `catalog.json` and existing trails before changing data. Preserve the
Android application's existing community catalog parser and keep
`schemaVersion` unchanged unless a schema migration is explicitly requested.

Current trail records may include:

- `id`
- `name`
- `description`
- `state`
- `region`
- `difficulty`
- `vehicleTypes`
- `tags`
- `distanceMiles`
- `gpxUrl`
- optional `sourceUrl` when embedded source metadata is available
- `updatedAt`

`state` is the two-letter U.S. state code. `region` is always the U.S. county
or counties containing the GPX geometry, not a national forest, mountain
range, city, vague area, or state name. Use `San Bernardino County` for one
county. When geometry genuinely crosses county boundaries, list all affected
counties alphabetically as `Orange County / Riverside County`; do not select a
primary county.

Resolve counties using the cheapest reliable evidence first: trustworthy trail
or source metadata, a known trail location, or one or a few representative GPX
coordinates. If that evidence clearly identifies one county, use it and stop.
Perform detailed county-boundary or geometry checking only when there is a
specific reason to suspect a crossing, such as representative points resolving
to different counties, a route known to be near a county boundary, conflicting
source information, or geometry that visibly spans a boundary area. Do not
download Census shapefiles or perform exhaustive per-point GIS analysis for
ordinary, clearly single-county trails.

Use the existing repository schema as authoritative. If an important value
cannot be derived reliably, ask the user. Never invent difficulty, legal
vehicle access, vehicle compatibility, land-access status, closures, or trail
legality.

Vehicle values are limited to `2wd` and `4x4`. Use exactly one of
`["2wd"]`, `["4x4"]`, or `["2wd", "4x4"]`, based on reliable route-access
information. Do not use activity labels such as `hiking` or `bicycle` as
vehicle types, and do not infer vehicle access merely because a GPX exists.

Every trail uses exactly:

```text
trails/<trail-id>/trail.gpx
trails/<trail-id>/trail.json
```

The GPX filename remains `trail.gpx`, the metadata filename remains
`trail.json`, and `catalog.json` uses `trails/<trail-id>/trail.gpx` for
`gpxUrl`. Do not create inconsistent filenames such as
`trails/<id>/<id>.gpx`.

## Catalog management

Whenever a trail is added, removed, or updated:

- keep `catalog.json` valid JSON;
- preserve its current `schemaVersion`;
- keep catalog entries consistent with their `trail.json` files;
- ensure every `gpxUrl` points to an existing GPX;
- prevent duplicate IDs;
- preserve stable IDs for existing trails;
- keep output reasonably deterministic and avoid rewriting unrelated data.

If `trail.json` and `catalog.json` disagree, investigate the discrepancy rather
than arbitrarily choosing one file.

## Git and publication approval

Inspect Git state before work. When appropriate and safe, start from clean
`main` with `git switch main` and `git pull --ff-only`; if fast-forwarding is
not possible, stop and explain the problem. Create a sensible branch for new
work. Never force-push, rewrite published history, or change repository
visibility or GitHub Pages settings unless explicitly requested.

Pushing community trail data to `main` publishes it through GitHub Pages and
makes it available to TrailRelay clients. After data work is complete, report
in plain English:

- trails added or changed, including IDs and names;
- metadata derived from GPX and metadata supplied by the user;
- calculated distances;
- files created or changed;
- validation performed;
- current branch and Git status;
- the exact commit and push actions proposed.

Then ask for explicit approval before committing or pushing. For example:

> I've added Cleghorn Ridge Trail, updated the catalog, and validation is
> clean. I'm ready to commit these data changes and push them to GitHub, which
> will make the trail available through GitHub Pages. Should I do that?

Do not commit or push merely because validation passes.

## Validation without repository tooling

Use straightforward system tools or temporary one-off code during maintenance.
At minimum, validate that:

- every JSON file parses;
- every catalog `gpxUrl` exists locally;
- trail IDs are unique and stable;
- every referenced GPX has usable track geometry;
- calculated distances are sensible and do not cross segment gaps;
- catalog and trail metadata agree where expected;
- region values use the county standard;
- `git diff --check` passes.

Do not recreate `scripts/trails.py`, a TUI, or another custom trail-management
program in this repository.
