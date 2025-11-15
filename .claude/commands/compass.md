Load and display the COMPASS-MINI.md coding standards with freshness timestamp:

```bash
COMPASS_PATH="/Users/victordelrosal/Library/CloudStorage/Dropbox/Dropbox24/fiveinnolabs/SmallBets/COMPASS/COMPASS-MINI.md"
echo "📋 COMPASS-MINI.md"
echo "Last modified: $(stat -f "%Sm" -t "%Y-%m-%d %H:%M:%S" "$COMPASS_PATH")"
echo "---"
echo ""
cat "$COMPASS_PATH"
```
