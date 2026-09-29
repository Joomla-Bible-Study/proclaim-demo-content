# Proclaim Demo Content

Opt-in demo content for a fresh [Proclaim](https://github.com/Joomla-Bible-Study/Proclaim) install — a handful of teachers, a series, several messages, topics, a second location, and media referencing each server type Proclaim supports. Offered from the setup wizard; nothing in a fresh install depends on it.

## Why this is a separate repository

Demo content needs real images, and a demo worth opting into is easily 1–2 MB. Proclaim's own repository — and the package downloaded by every existing site on every update — should not carry that. Content here is a **build/runtime input**, not shipped code: Proclaim references a release of this repo by version and checksum, with nothing pinned and no submodule link added to the existing `lib_cwmscripture` → `CWMScriptureLinks` → `Proclaim` chain.

## Format

`content/manifest.json` is the exact payload shape [`Cwmcontentimporter`](https://github.com/Joomla-Bible-Study/Proclaim/blob/development/admin/src/Lib/Cwmcontentimporter.php) accepts: `teachers`, `series`, `messages`, `mediafiles` sections, each entry a plain object with a source `id` other entries reference. Importing is additive and tracked — every row and file it creates is recorded in Proclaim's import-set manifest, so it can be found again and cleanly removed. See that class's own docblock for the full contract.

`content/images/` holds the teacher/series images the manifest's `files[]` entries reference by relative path — the only binary content this repo ships. Audio and video are demonstrated by URL (a real YouTube link for the platform/YouTube server type), never bundled as bytes.

## Versioning

Tagged against the Proclaim schema version it was authored against (e.g. `v11.0.0`), not a single moving "latest" — schema drift between what an archive expects and what an older Proclaim install has is the reason. The setup wizard requests the newest release compatible with the installed schema, not simply the newest tag.

## Distribution

Released as a GitHub release asset and catalogued in Akeeba Release System (ARS) as a `type: link` item — exactly how `pkg_proclaim` itself is distributed. One artifact serves the setup-wizard fetch, CI/E2E fixtures, and manual review, all from the same bytes. The archive's checksum is published alongside the release and pinned in the Proclaim package at release time, not fetched at runtime from this repo's own host.

## Building a release archive

```bash
cd content
zip -r ../proclaim-demo-content-<version>.zip manifest.json images/
sha256sum ../proclaim-demo-content-<version>.zip
```

(A proper build script belongs here once the archive format is finalized — see the open items below.)

## Status

See [Joomla-Bible-Study/Proclaim#2178](https://github.com/Joomla-Bible-Study/Proclaim/issues/2178) for the design discussion and [#2145](https://github.com/Joomla-Bible-Study/Proclaim/issues/2145) for the epic.

`content/manifest.json` is drafted and live-verified against a real Proclaim install (imports cleanly, every state renders correctly, removes cleanly). Still open before a release can be cut:

- **A real YouTube URL.** `mediafiles[0].params.filename` is a placeholder — needs a rights-clear, real public video before this ships.
- **A second location, not yet added to the manifest.** [Proclaim#2196](https://github.com/Joomla-Bible-Study/Proclaim/issues/2196) shipped `Cwmcontentimporter` support for a `locations` section, so this is now unblocked — the manifest just doesn't reference one yet. Note for whoever adds it: `messages[].location_id` is closed-world against `locations[]` in the *same* payload, so this repo needs its own location entries; it cannot reference a site's existing seeded one.
- **Podcast enclosure, local-file and external-embed media types are not demonstrated at all**, by decision — avoids any new CWM hosting commitment for demo audio/video. YouTube (real public link) is the only media type this set demonstrates.
- **ARS cataloguing** — needs a Category and Update Stream created via the ARS admin UI (`plg_webservices_ars` does not expose a creation API for either yet); `cwm-build.config.json` in this repo carries placeholder ids until then.
- **The wizard-side fetch** (Proclaim issue #2177) and the network-fetch/checksum-verification path it needs.
- **No images yet** — `content/images/` is empty; teacher photos were deliberately skipped for this draft.

## License

GNU General Public License v2 or later — see [LICENSE](LICENSE). Demo content authored for this repository is original and fictional; it does not represent any real person, sermon, or organization.
