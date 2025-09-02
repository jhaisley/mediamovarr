# Media Organizer CLI - Implementation Plan

Last updated: 2025-09-01

## 1. High-level description

A Python CLI that reads `scan_dir` and `dest_dir` from a `config.json`, detects downloaded media folders, determines each folder's media type (TV Show, Movie, Music, Audiobook), renames folders/files to follow Plex naming guidelines, optionally enriches metadata using TheMovieDB (TMDb), and moves organized content into subfolders under the destination directory (e.g., `Movies/`, `TV Shows/`, `Music/`, `Audiobooks/`).

## 2. Requirements checklist

- [ ] Read `scan_dir` and `dest_dir` from `config.json`.
- [ ] Discover downloaded media folders under `scan_dir`.
- [ ] Detect media type: `TV Show`, `Movie`, `Music`, `Audiobook`, or `Unknown`.
- [ ] Rename files/folders according to Plex guidelines for TV and Movies.
- [ ] Move organized media into subfolders under `dest_dir` (one folder per type).
- [ ] Optional: Use TMDb API to resolve titles/years/season info for movies and TV shows.
- [ ] Provide `--dry-run`, `--verbose` flags, and logging.
- [ ] Unit tests for core detection + renaming logic.
- [ ] CLI entry point and small README with usage.

## 3. Constraints & assumptions

- TMDb integration requires a user-provided API key in config.json.
- The app will operate on local filesystem paths.
- It will not alter files outside `scan_dir` or `dest_dir` unless explicitly moved by the tool.
- The first implementation focuses on deterministic rules and TMDb lookup as an enhancement; advanced fuzzy matching and subtitle handling are out-of-scope for v1.

## 4. Config file

Example `config.json` (required fields):

We should support multiple scan dirs, and include C:\Downloads and %USERPROFILE%\Downloads

```json
{
  "scan_dir": "C:\\Downloads", 
  "dest_dir": "D:\\Media",
  "tmdb_enabled": true,
  "tmdb_api_key": "56129363bc29f103b8b87884368b05f8"
  "tmdb_read_access_token": "eyJhbGciOiJIUzI1NiJ9.eyJhdWQiOiI1NjEyOTM2M2JjMjlmMTAzYjhiODc4ODQzNjhiMDVmOCIsIm5iZiI6MTc1NjczODQ3Ni4wNiwic3ViIjoiNjhiNWIzYWNkYzA4ZGRmZmQ0MjAwZTdlIiwic2NvcGVzIjpbImFwaV9yZWFkIl0sInZlcnNpb24iOjF9.1GqWj3arQgLh1kRXvQ22rnooqMK7C0HjRx0tCoLSpcc"
}
```

Notes:

- `tmdb_enabled` is optional (defaults to `false`).
- `tmdb_api_key` is required if `tmdb_enabled` is `true`.
- ALL config information and rules should be stored in config.json
- A temporary api key and read access token was included for testing purposes these will automatically expire after 24 hours

## 5. Implementation plan (phases)

### Phase 1 — Project skeleton & config

1. Create package layout (`mediamovarr/`), CLI entry, and `pyproject.toml` or `requirements.txt`.
2. Implement config loader that validates `scan_dir` and `dest_dir`.
3. Add CLI flags: `--dry-run`, `--verbose`, `--config`.

### Phase 2 — Discovery & classification

1. Walk `scan_dir` and identify candidate folders (non-empty folders, common download patterns).
2. Implement type detection heuristics:
   - TV Show: contains season folders like `Season 01`, or files matching `S01E01` patterns.
   - Movie: single-folder with a video file and year in name (e.g., `Title (2021)`), or large single video file.
   - Music: presence of many audio files, typical extensions `.mp3`, `.flac`, or artist/album structure.
   - Audiobook: large number of audio files grouped as a single title with `chapter`/`part` patterns.
3. Return classification with confidence score.

### Phase 3 — Renaming rules (Plex-guided)

1. Implement TV renamer following Plex rules: `Show Name/Season 01/Show.Name.S01E01.ext` (or `Show Name/Season 01/Show Name - S01E01 - Episode Title.ext` optionally).
2. Implement Movie renamer: `Movies/Title (Year)/Title (Year).ext`.
3. Implement fallback rules for Music and Audiobooks (move into `Music/Artist/Album/...` or `Audiobooks/Author/Title/...`).
4. Expose a rule engine so future rules can be added easily.

### Phase 4 — TMDb integration (optional enhancement)

1. Implement small TMDb client that supports searching for movies and TV shows.
2. Use TMDb results to canonicalize titles and extract release year and episode/season metadata where available.
3. Respect rate limits and allow caching (simple local cache file).

### Phase 5 — Move/rename operations and safety

1. Implement `--dry-run` where planned moves/renames are printed but not executed.
2. Implement atomic move strategy: validate destination path, create directories, move using safe operations (avoid overwriting unless `--force`).
3. Add logging and a summary report (moved, skipped, errors).

### Phase 6 — Testing & documentation

1. Unit tests for detection and renaming rules (no filesystem operations if possible: use temporary directories and fixtures).
2. Integration test that runs a small sample directory under `tmp/`.
3. Add `README.md` with setup and usage instructions.

## 6. Files to create (initial set)

- `mediamovarr/__main__.py` — CLI entrypoint
- `mediamovarr/config.py` — config loader and validator
- `mediamovarr/discovery.py` — folder discovery
- `mediamovarr/classify.py` — media type detection
- `mediamovarr/renamer.py` — renaming rules and path builders
- `mediamovarr/tmdb_client.py` — optional TMDb lookup
- `tests/test_classify.py`, `tests/test_renamer.py`
- `pyproject.toml` or `requirements.txt`
- `README.md`

## 7. Acceptance criteria

- CLI reads config and finds at least 3 test folders and classifies them correctly in unit tests.
- Renaming output matches Plex guidelines for at least one TV and one Movie example.
- `--dry-run` shows planned moves without performing filesystem writes.
- TMDb lookup (if enabled) normalizes titles to canonical names and years for movies/TV.

## 8. Risks & mitigations

- False positives in classification: mitigate with confidence scores and require `--force` for destructive moves.
- TMDb mismatches: provide `--no-tmdb` or manual override mapping in config.
- File collisions: default to not overwriting; add `--force` to allow overwrites.

## 9. Timeline & milestones (rough)

- Day 1: Skeleton, config loader, discovery and basic classification.
- Day 2: Renamer for Movies and TV, dry-run and move implementation.
- Day 3: TMDb integration, tests, README, polish.

## 10. Next steps

- Review this plan and request changes, or approve to begin implementation.
- On approval I will generate the AI instructions file and request "Act mode" permission to start creating code files and tests.

---

Please reply with one of:

- "Approve" — I will begin implementation (code changes and tests) and ask for any runtime secrets (e.g., `TMDB_API_KEY`).
- "Suggest changes" — include modifications and I'll update the plan.
- "Cancel" — stop here.
