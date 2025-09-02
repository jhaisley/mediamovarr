<!--
version: 1.0.0
last_modified: 2025-09-01
last_modified_by: planner-mode
update_reason: initial_creation
related_task: media-organizer-cli
-->

---

## applyTo: "\*\*"

# Media Organizer CLI Implementation Instructions

## Project Context

This is a Python CLI application called `mediamovarr` that organizes downloaded media files by:

1. Scanning configured directories for media folders
2. Classifying content type (TV Show, Movie, Music, Audiobook)
3. Renaming according to Plex guidelines
4. Moving to organized destination folders
5. Optionally enriching metadata via TMDb API

### Tech Stack

- Python 3.8+
- CLI framework: Click or argparse
- HTTP client: requests (for TMDb)
- File operations: pathlib, shutil
- Config: JSON
- Testing: pytest

### Architecture

```
mediamovarr/
├── __main__.py          # CLI entry point
├── config.py            # Configuration loading/validation
├── discovery.py         # Media folder discovery
├── classify.py          # Media type detection
├── renamer.py           # Plex naming rules
├── tmdb_client.py       # TMDb API integration
└── mover.py             # File moving operations
```

## Implementation Tasks

### Phase 1: Project Setup

1. Create `pyproject.toml` with dependencies: click, requests, pathlib
2. Implement `mediamovarr/__main__.py` CLI with flags: `--dry-run`, `--verbose`, `--config`
3. Create `mediamovarr/config.py` to load/validate config.json

### Phase 2: Discovery & Classification

1. Implement `mediamovarr/discovery.py` to scan directories and find media folders
2. Create `mediamovarr/classify.py` with heuristics:
   - TV Show: Season folders, S01E01 patterns
   - Movie: Single video file, year in name
   - Music: Multiple audio files, artist/album structure
   - Audiobook: Audio files with chapter/part patterns

### Phase 3: Renaming Rules

1. Implement `mediamovarr/renamer.py` following Plex guidelines:
   - TV: `Show Name/Season 01/Show.Name.S01E01.ext`
   - Movies: `Title (Year)/Title (Year).ext`
   - Music: `Artist/Album/Track.ext`
   - Audiobooks: `Author/Title/Chapter.ext`

### Phase 4: TMDb Integration

1. Create `mediamovarr/tmdb_client.py` for API calls
2. Search and normalize titles/years
3. Cache results locally

### Phase 5: Move Operations

1. Implement `mediamovarr/mover.py` with dry-run support
2. Safe atomic moves with conflict resolution
3. Logging and summary reporting

## Code Examples & Patterns

### Config Loading

```python
import json
from pathlib import Path
from typing import Dict, List

def load_config(config_path: str) -> Dict:
    with open(config_path) as f:
        config = json.load(f)

    # Validate required fields
    required = ['scan_dirs', 'dest_dir']
    for field in required:
        if field not in config:
            raise ValueError(f"Missing required config: {field}")

    return config
```

### CLI Structure

```python
import click

@click.command()
@click.option('--config', default='config.json', help='Config file path')
@click.option('--dry-run', is_flag=True, help='Show what would be done')
@click.option('--verbose', is_flag=True, help='Verbose output')
def main(config, dry_run, verbose):
    """Media organizer CLI"""
    pass
```

### Media Classification

```python
from pathlib import Path
from typing import Tuple

def classify_media(folder_path: Path) -> Tuple[str, float]:
    """Return (media_type, confidence_score)"""
    files = list(folder_path.rglob('*'))

    # TV Show detection
    if any('season' in f.name.lower() for f in files):
        return 'tv', 0.9

    # Movie detection
    video_files = [f for f in files if f.suffix in ['.mp4', '.mkv', '.avi']]
    if len(video_files) == 1 and any(char.isdigit() for char in folder_path.name):
        return 'movie', 0.8

    return 'unknown', 0.0
```

### Plex Naming

```python
def rename_tv_show(title: str, season: int, episode: int, ext: str) -> str:
    """Generate Plex-compliant TV show filename"""
    clean_title = title.replace(' ', '.')
    return f"{clean_title}.S{season:02d}E{episode:02d}.{ext}"

def rename_movie(title: str, year: int, ext: str) -> str:
    """Generate Plex-compliant movie filename"""
    return f"{title} ({year}).{ext}"
```

## File Structure & Dependencies

### pyproject.toml

```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mediamovarr"
version = "1.0.0"
dependencies = [
    "click>=8.0.0",
    "requests>=2.25.0",
    "pathlib",
]

[project.scripts]
mediamovarr = "mediamovarr.__main__:main"
```

### config.json Structure

```json
{
  "scan_dirs": ["C:\\Downloads", "%USERPROFILE%\\Downloads"],
  "dest_dir": "D:\\Media",
  "tmdb_enabled": true,
  "tmdb_api_key": "your_api_key",
  "tmdb_read_access_token": "your_token",
  "rules": {
    "tv_format": "{title}/Season {season:02d}/{title}.S{season:02d}E{episode:02d}",
    "movie_format": "{title} ({year})"
  }
}
```

## Testing Strategy

### Unit Tests

- `tests/test_classify.py`: Media type detection with sample folders
- `tests/test_renamer.py`: Plex naming rule validation
- `tests/test_tmdb.py`: API client mocking
- `tests/test_config.py`: Config validation

### Integration Tests

- `tests/test_integration.py`: End-to-end with temporary directories

## Next Steps

1. Implement core skeleton and config loading
2. Add discovery and classification logic
3. Create renaming rules following Plex guidelines
4. Integrate TMDb API with caching
5. Add move operations with dry-run support
6. Write comprehensive tests
7. Create README with usage examples

## Development Notes

- Use pathlib for cross-platform path handling
- Implement proper logging with levels
- Handle edge cases (empty folders, permission errors)
- Consider rate limiting for TMDb API
- Use environment variable expansion for config paths
- Validate all user inputs and provide clear error messages
