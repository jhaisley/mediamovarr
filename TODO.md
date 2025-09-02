# MediaMovarr TODO

## Completed ✅

### Phase 1 - Project Setup

- [x] Created package structure with `mediamovarr/` module
- [x] Implemented configuration loading with JSON validation  
- [x] Added CLI entry point with Click framework
- [x] Created `pyproject.toml` and `requirements.txt`

### Phase 2 - Discovery & Classification  

- [x] Implemented media folder discovery with depth limiting
- [x] Created media type classification (TV, Movie, Music, Audiobook)
- [x] Added confidence scoring for classifications
- [x] Pattern matching for TV shows (Season, S01E01, etc.)
- [x] Pattern matching for movies (year detection, file analysis)
- [x] Heuristics for music and audiobook detection

### Phase 3 - Renaming & Organization

- [x] Plex-compliant TV show naming: `Show Name/Season 01/Show.Name.S01E01.ext`
- [x] Plex-compliant movie naming: `Movies/Title (Year)/Title (Year).ext`
- [x] Music organization: `Music/Artist/Album/`
- [x] Audiobook organization: `Audiobooks/Author/Title/`
- [x] Title cleaning and sanitization

### Phase 4 - TMDb Integration

- [x] TMDb API client with rate limiting
- [x] Movie and TV show search functionality
- [x] Caching to avoid repeated API calls
- [x] Title normalization using TMDb data
- [x] Support for both API key and read access token

### Phase 5 - File Operations

- [x] Safe file moving with conflict detection
- [x] Dry-run mode for preview
- [x] Force overwrite option
- [x] Comprehensive error handling and logging
- [x] Atomic operations to prevent data loss

### Phase 6 - Testing & Documentation

- [x] Unit tests for classification logic
- [x] Unit tests for renaming rules
- [x] Integration test for end-to-end functionality
- [x] Comprehensive README with usage examples
- [x] Sample configuration file
- [x] Setup and test script

## Ready for Use 🚀

The MediaMovarr CLI is now fully functional with all planned features implemented:

- **Multi-directory scanning** with environment variable support
- **Intelligent media detection** using filename patterns and file analysis
- **Plex-compatible organization** following official guidelines
- **TMDb metadata lookup** for accurate titles and years
- **Safe operations** with dry-run mode and comprehensive logging
- **Cross-platform support** for Windows, macOS, and Linux

## Future Enhancements (Optional)

### Nice-to-Have Features

- [ ] Web interface for configuration and monitoring
- [ ] Database logging of all operations
- [ ] Undo functionality for moves
- [ ] Custom naming rule configuration
- [ ] Batch operations on multiple folders
- [ ] Support for additional metadata sources (TVDB, IMDB)
- [ ] Automatic subtitle and metadata file handling
- [ ] Integration with media servers (Plex, Jellyfin, Emby)
- [ ] Duplicate detection and handling
- [ ] Network drive optimization

### Advanced Features  

- [ ] Machine learning for improved classification
- [ ] Fuzzy string matching for title normalization
- [ ] Support for multi-part movies
- [ ] Advanced TV show handling (specials, extras)
- [ ] Music metadata from MusicBrainz
- [ ] Image/poster downloading
- [ ] NFO file generation

## Usage Instructions

1. **Install**: `pip install -e .` or `pip install -r requirements.txt`
2. **Configure**: Edit `config.json` with your directories and TMDb credentials
3. **Test**: Run `mediamovarr --dry-run --verbose` to preview
4. **Execute**: Run `mediamovarr` to organize your media

The implementation is complete and ready for production use! 🎉
