# Editor Config Skill

## Purpose
Generate the `.editorconfig` file for consistent editor settings across different IDEs.

## Output
Create the file: `.editorconfig`

## Template

```ini
# EditorConfig is awesome: https://EditorConfig.org

# top-most EditorConfig file
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
indent_style = space
indent_size = 2
trim_trailing_whitespace = true

[*.md]
trim_trailing_whitespace = false
```

## Notes
- Ensures consistent coding style across all editors
- Uses 2-space indentation for all files
- LF line endings for cross-platform compatibility
- Preserves trailing whitespace in markdown files
