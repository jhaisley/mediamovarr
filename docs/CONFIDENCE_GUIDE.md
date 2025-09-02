# MediaMovarr Confidence Thresholds

## New Configuration Options

Added to `config.json`:

```json
{
  "confidence_thresholds": {
    "auto_process": 0.9,      // 90% - Process automatically without confirmation
    "require_confirmation": 0.5  // 50% - Require user confirmation
  }
}
```

## Confidence Levels & Behavior

### 🟢 **HIGH Confidence (≥ 90%)**

- **Action**: Process automatically
- **Examples**: "The Office Season 01", "Inception (2010)"
- **No user interaction required**

### 🟡 **MEDIUM Confidence (50% - 89%)**

- **Interactive Mode**: Ask user for confirmation
- **Non-Interactive Mode**: Skip automatically
- **Examples**: Ambiguous folder names, partial matches

### 🔴 **LOW Confidence (< 50%)**

- **Action**: Always skip
- **Examples**: System folders, non-media content
- **Prevents false positives**

## New CLI Options

```bash
# Interactive mode (default) - asks for confirmation on medium confidence
mediamovarr --dry-run --verbose

# Non-interactive mode - skips medium confidence items
mediamovarr --dry-run --verbose --no-interactive

# Shows confidence thresholds in action
python test_confidence.py
```

## Output Examples

### High Confidence (Auto-Process)

```text
Processing: The Office Season 01
  Type: tv (confidence: 0.95)
  [DRY RUN] Would move to: C:\Media\TV Shows\The Office\Season 01
```

### Medium Confidence (Requires Confirmation)

```text
Processing: Some Ambiguous Folder
  Type: movie (confidence: 0.67)
  [DRY RUN] [WOULD REQUIRE CONFIRMATION] Would move to: C:\Media\Movies\Some Ambiguous Folder
```

### Low Confidence (Skipped)

```text
Processing: Random Documents
  Type: unknown (confidence: 0.20)
  Confidence too low (0.20 < 0.50), skipping
```

## Testing

Run the new confidence test:

```bash
python test_confidence.py
```

This will show you exactly which folders would be processed, which would require confirmation, and which would be skipped based on your current Downloads folder contents.

## Benefits

1. **Prevents False Positives**: Won't move system folders or documents
2. **User Control**: You decide on ambiguous cases
3. **Configurable**: Adjust thresholds based on your preferences
4. **Safe Defaults**: Conservative settings protect your files
5. **Batch Processing**: `--no-interactive` for automated runs
