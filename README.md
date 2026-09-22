# TrailRelay community trails

TrailRelay-Trails is the public static community trail-data repository used by
the TrailRelay Android application. The repository is the dataset: it has no
backend, database, account system, GitHub API requirement, or trail-management
application.

GitHub Pages publishes the catalog at:

<https://aldogplays.github.io/TrailRelay-Trails/catalog.json>

Individual GPX files are published at paths such as:

<https://aldogplays.github.io/TrailRelay-Trails/trails/<trail-id>/trail.gpx>

Each trail is kept in its own directory:

```text
trails/<trail-id>/trail.gpx
trails/<trail-id>/trail.json
```

`catalog.json` references these GPX files and is consumed by TrailRelay. Keep
the existing JSON shape and stable trail IDs compatible with the application.
The `state` field uses a two-letter U.S. state code; `region` uses the county
or alphabetized slash-separated counties containing the GPX geometry.

For trail contributions, provide the GPX file to the repository maintainer or
Codex. Codex handles GPX inspection, metadata preparation, trail files,
catalog updates, validation, and the Git handoff. Review the resulting data
before approving a commit or push, because pushing to the published branch
makes the trail public to TrailRelay clients.
