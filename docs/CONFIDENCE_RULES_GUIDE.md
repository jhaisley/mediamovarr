# Custom Confidence Rules Guide

MediaMovarr now supports custom confidence rules that can adjust classification scores based on specific criteria. This helps prevent false positives and improves accuracy for your specific use cases.

## How It Works

1. **Base Classification**: MediaMovarr analyzes folder structure and files to get a base confidence score
2. **TMDb Lookup**: If enabled, tries to find matches in The Movie Database for additional validation
3. **Custom Rules**: Applies your custom rules to adjust the confidence score
4. **Final Decision**: Uses the adjusted confidence to decide whether to process, ask confirmation, or skip

## Rule Structure

Each rule in `config.json` has this structure:

```json
{
  "name": "Human-readable rule name",
  "condition": "what_to_check",
  "value": "expected_value",
  "adjustment": 0.3,
  "reason": "Why this rule exists"
}
```

## Available Conditions

### File Type Conditions

```json
{
  "name": "WAV files penalty",
  "condition": "filetype",
  "value": "wav",
  "adjustment": -0.3,
  "reason": "WAV files are often sound effects, not music"
}
```

Checks if the folder contains files with the specified extension.

### TMDb Match Conditions

```json
{
  "name": "TMDb match bonus",
  "condition": "tmdb_match",
  "value": true,
  "adjustment": 0.5,
  "reason": "TMDb match indicates legitimate media"
}
```

Triggers when TMDb finds (or doesn't find) a match for the content.

### File Structure Conditions

```json
{
  "name": "Single large video bonus",
  "condition": "single_large_video",
  "value": true,
  "adjustment": 0.2,
  "reason": "Single large video file likely indicates a movie"
}
```

Available structure conditions:

- `single_large_video`: One video file >500MB
- `many_small_files`: >10 files under 10MB each
- `has_season_structure`: Contains season folders

### File Count Conditions

```json
{
  "name": "Too many audio files",
  "condition": "audio_file_count",
  "value": ">50",
  "adjustment": -0.4,
  "reason": "Too many files might be a sample collection"
}
```

Operators supported: `>`, `<`, `>=`, `<=`, `=`, or exact number

### Folder Name Conditions

```json
{
  "name": "Documentary bonus",
  "condition": "folder_name_contains",
  "value": ["documentary", "nature", "history"],
  "adjustment": 0.1,
  "reason": "Documentary keywords indicate legitimate media"
}
```

Available name conditions:

- `folder_name_contains`: Check if folder name contains specific words
- `folder_name_matches`: Use regex patterns for advanced matching

## Example Custom Rules

### Prevent Processing System Folders

```json
{
  "name": "System folder penalty",
  "condition": "folder_name_contains",
  "value": ["system", "windows", "program", "$recycle"],
  "adjustment": -0.8,
  "reason": "System folders should never be processed as media"
}
```

### Boost Confidence for 4K Content

```json
{
  "name": "4K content boost",
  "condition": "folder_name_contains",
  "value": ["4k", "2160p", "uhd"],
  "adjustment": 0.2,
  "reason": "4K content is usually legitimate media"
}
```

### Penalize Sample Collections

```json
{
  "name": "Sample collection penalty",
  "condition": "folder_name_contains",
  "value": ["sample", "preview", "trailer"],
  "adjustment": -0.4,
  "reason": "Sample collections are not full media"
}
```

### Bonus for Complete Seasons

```json
{
  "name": "Complete season bonus",
  "condition": "folder_name_contains",
  "value": ["complete", "season", "series"],
  "adjustment": 0.15,
  "reason": "Complete seasons are likely legitimate TV shows"
}
```

## Advanced Examples

### Multi-Condition Logic (Simulated)

While each rule is independent, you can create multiple rules that work together:

```json
[
  {
    "name": "High quality video bonus",
    "condition": "folder_name_contains",
    "value": ["1080p", "720p"],
    "adjustment": 0.1
  },
  {
    "name": "BluRay source bonus",
    "condition": "folder_name_contains",
    "value": ["bluray", "blu-ray"],
    "adjustment": 0.15
  },
  {
    "name": "Web source penalty",
    "condition": "folder_name_contains",
    "value": ["webrip", "web-dl"],
    "adjustment": -0.05
  }
]
```

### File Size Based Rules

```json
{
  "name": "Too many tiny files penalty",
  "condition": "many_small_files",
  "value": true,
  "adjustment": -0.3,
  "reason": "Many tiny files suggest samples or incomplete downloads"
}
```

## Testing Your Rules

Use the confidence rules test script:

```bash
python test_confidence_rules.py
```

This will show you:

- Which rules are active
- How they're being applied to your actual folders
- The confidence adjustments in real-time
- Final processing decisions

## Best Practices

### 1. Start Conservative

Begin with small adjustments (±0.1 to ±0.2) and observe the results.

### 2. Use Descriptive Names

Good rule names help you understand what's happening in the logs.

### 3. Document Your Reasoning

The `reason` field helps you remember why you created each rule.

### 4. Test Thoroughly

Always run dry-runs with `--verbose` to see how rules affect your specific content.

### 5. Layer Rules

Use multiple small rules rather than one large adjustment for more nuanced control.

## Troubleshooting

### Rule Not Triggering

- Check the exact condition syntax
- Verify the folder actually matches your criteria
- Use `--verbose` to see rule evaluation

### Unexpected Results

- Rules are cumulative - multiple rules can apply to one folder
- Check for conflicting rules that cancel each other out
- Verify your adjustment values aren't too extreme

### Performance Issues

- Too many complex regex rules can slow processing
- Simple string matching is faster than regex patterns

## Rule Priority

Rules are applied in the order they appear in the config file. All matching rules are applied cumulatively to the base confidence score.

Final confidence is always clamped between 0.0 and 1.0, so extreme adjustments won't break the system.
