# Documentation automation agent guidelines

This file governs automated documentation updates triggered by code changes. All automation phases must comply with these rules.

## Documentation system

This documentation uses **mdBook** (https://rust-lang.github.io/mdBook/).

### Key files

- **`docs/src/SUMMARY.md`**: Table of contents following mdBook format (https://rust-lang.github.io/mdBook/format/summary.html)
- **`docs/book.toml`**: mdBook configuration
- **`docs/.prettierrc`**: Prettier config (80 char line width)

### SUMMARY.md format

The `SUMMARY.md` file defines the book structure. Format rules:

- Chapter titles are links: `[Title](./path/to/file.md)`
- Nesting via indentation (2 spaces per level)
- Separators: `---` for horizontal rules between sections
- Draft chapters: `[Title]()` (empty parens, not yet written)

Example:

```markdown
# Section Title

- [Chapter](./chapter.md)
  - [Nested Chapter](./nested.md)

---

# Another Section
```

### Custom preprocessor

The docs use a custom preprocessor (`docs_preprocessor`) that expands special commands:

| Syntax                      | Purpose                               | Example                       |
| --------------------------- | ------------------------------------- | ----------------------------- |
| {#kb action::ActionName}    | Keybinding for action                 | {#kb agent::ToggleFocus}      |
| {#action agent::ActionName} | Action reference (renders as command) | {#action agent::OpenSettings} |

**Rules:**

- Always use preprocessor syntax for keybindings instead of hardcoding
- Action names use `snake_case` in the namespace, `PascalCase` for the action
- Common namespaces: `agent::`, `editor::`, `assistant::`, `vim::`

### Formatting requirements

All documentation must pass **Prettier** formatting:

```sh
cd docs && npx prettier --check src/
```

Before any documentation change is considered complete:

1. Run Prettier to format: `cd docs && npx prettier --write src/`
2. Verify it passes: `cd docs && npx prettier --check src/`

Prettier config: 80 character line width (`docs/.prettierrc`)

### Section anchors

Use `{#anchor-id}` syntax for linkable section headers:

```markdown
## Getting Started {#getting-started}

### Custom Models {#anthropic-custom-models}
```

Anchor IDs should be:

- Lowercase with hyphens
- Unique within the page
- Descriptive (can include parent context like `anthropic-custom-models`)

### Code block annotations

Use annotations after the language identifier to indicate file context:

```markdown
\`\`\`json [settings]
{
"agent": { ... }
}
\`\`\`

\`\`\`json [keymap]
[
{ "bindings": { ... } }
]
\`\`\`
```

Valid annotations: `[settings]` (for settings.json), `[keymap]` (for keymap.json)

### Blockquote formatting

Use bold labels for callouts:

```markdown
> **Note:** Important information the user should know.

> **Tip:** Helpful advice that saves time or improves workflow.

> **Warn:** Caution about potential issues or gotchas.
```

### Image references

Images are hosted externally. Reference format:

```markdown
![Alt text description](https://zed.dev/img/path/to/image.webp)
```

### Cross-linking

- Relative links for same-directory: `[Agent Panel](./agent-panel.md)`
- With anchors: `[Custom Models](./llm-providers.md#anthropic-custom-models)`
- Parent directory: `[Telemetry](../telemetry.md)`

## Voice and tone

### Core principles

- **Practical over promotional**: Focus on what users can do, not on selling Zed. Avoid marketing language like "powerful," "revolutionary," or "best-in-class."
- **Honest about limitations**: When Zed lacks a feature or doesn't match another tool's depth, say so directly. Pair limitations with workarounds or alternative workflows.
- **Direct and concise**: Use short sentences. Get to the point. Developers are scanning, not reading novels.
- **Second person**: Address the reader as "you." Avoid "the user" or "one."
- **Present tense**: "Zed opens the file" not "Zed will open the file."

### What to avoid

- Superlatives without substance ("incredibly fast," "seamlessly integrated")
- Hedging language ("simply," "just," "easily"). If something is simple, the instructions will show it
- Apologetic tone for missing features. State the limitation and move on
- Comparisons that disparage other tools. Be factual, not competitive
- Lots of use of em or en dashes.

## Examples of good copy

### Good: Direct and actionable

```
To format on save, open the Settings Editor (`Cmd+,`) and search for `format_on_save`. Set it to `on`.

Or add this to your settings.json:
{
  "format_on_save": "on"
}
```

### Bad: Wordy and promotional

```
Zed provides a powerful and seamless formatting experience. Simply navigate to the settings and you'll find the format_on_save option which enables Zed's incredible auto-formatting capabilities.
```

### Good: Honest about limitations

```
Zed doesn't index your project like IntelliJ does. You open a folder and start working immediately—no waiting. The trade-off: cross-project analysis relies on language servers, which may not go as deep.

**How to adapt:**
- Use `Cmd+Shift+F` for project-wide text search
- Use `Cmd+O` for symbol search (powered by your language server)
```

### Bad: Defensive or dismissive

```
While some users might miss indexing, Zed's approach is actually better because it's faster.
```

## Scope

### In-scope documentation

- All Markdown files in `docs/src/`
- `docs/src/SUMMARY.md` (mdBook table of contents)
- Language-specific docs in `docs/src/languages/`
- Feature docs (AI, extensions, configuration, etc.)

### Out-of-scope (do not modify)

- `CHANGELOG.md`, `CONTRIBUTING.md`, `README.md` at repo root
- Inline code comments and rustdoc
- `CLAUDE.md`, `GEMINI.md`, or other AI instruction files
- Build configuration (`book.toml`, theme files, `docs_preprocessor`)
- Any file outside `docs/src/`

## Page structure patterns

### Standard page layout

Most documentation pages follow this structure:

1. **Title** (H1), a single sentence or phrase
2. **Overview/Introduction**, 1-3 paragraphs explaining what this is
3. **Getting Started** `{#getting-started}`, prerequisites and first steps
4. **Main Content**, feature details, organized by topic
5. **Advanced/Configuration**, power user options
6. **See Also** (optional), related documentation links

### Settings documentation pattern

When documenting settings:

1. Show the Settings Editor (UI) approach first
2. Then show JSON as "Or add this to your settings.json:"
3. Always show complete, valid JSON with surrounding structure:

```json [settings]
{
  "agent": {
    "default_model": {
      "provider": "anthropic",
      "model": "claude-sonnet-4"
    }
  }
}
```

### Provider/feature documentation pattern

For each provider or distinct feature:

1. H3 heading with anchor: `### Provider Name {#provider-name}`
2. Brief description (1-2 sentences)
3. Setup steps (numbered list)
4. Configuration example (JSON code block)
5. Custom models section if applicable: `#### Custom Models {#provider-custom-models}`

## Style rules

Inherit all conventions from `docs/.rules`. Key points:

### Voice

- Second person ("you"), present tense
- Direct and concise, no hedging ("simply", "just", "easily")
- Honest about limitations; no promotional language

### Formatting

- Keybindings: backticks with `+` for simultaneous keys (`Cmd+Shift+P`)
- Show both macOS and Linux/Windows variants when they differ
- Use `sh` code blocks for terminal commands
- Settings: show Settings Editor UI first, JSON as secondary

### Terminology

| Use             | Instead of                                                            |
| --------------- | --------------------------------------------------------------------- |
| folder          | directory                                                             |
| project         | workspace                                                             |
| Settings Editor | settings UI                                                           |
| command palette | command bar                                                           |
| panel           | tool window, sidebar (be specific: "Project Panel," "Terminal Panel") |
| language server | LSP (spell out first use, then LSP is fine)                           |

## Zed-specific conventions

### Recognized rules files

When documenting rules/instructions for AI, note that Zed recognizes these files (in priority order):

- `.rules`
- `.cursorrules`
- `.windsurfrules`
- `.clinerules`
- `.github/copilot-instructions.md`
- `AGENT.md`
- `AGENTS.md`
- `CLAUDE.md`
- `GEMINI.md`

### Settings file locations

- macOS: `~/.config/zed/settings.json`
- Linux: `~/.config/zed/settings.json`
- Windows: `%AppData%\Zed\settings.json`

### Keymap file locations

- macOS: `~/.config/zed/keymap.json`
- Linux: `~/.config/zed/keymap.json`
- Windows: `%AppData%\Zed\keymap.json`

## Safety constraints

### Must not

- Delete existing documentation files
- Remove sections documenting existing functionality
- Change URLs or anchor links without verifying references
- Modify `SUMMARY.md` structure without corresponding content
- Add speculative documentation for unreleased features
- Include internal implementation details not relevant to users

### Must

- Preserve existing structure when updating content
- Maintain backward compatibility of documented settings/commands
- Flag uncertainty explicitly rather than guessing
- Link to related documentation when adding new sections

## Change classification

### Requires documentation update

- New user-facing features or commands
- Changed keybindings or default behaviors
- Modified settings schema or options
- Deprecated or removed functionality
- API changes affecting extensions

### Does not require documentation update

- Internal refactoring without behavioral changes
- Performance optimizations (unless user-visible)
- Bug fixes that restore documented behavior
- Test changes
- CI/CD changes

## Output format

### Phase 4 documentation plan

When generating a documentation plan, use this structure:

```markdown
## Documentation Impact Assessment

### Summary

Brief description of code changes analyzed.

### Documentation Updates Required: [Yes/No]

### Planned Changes

#### 1. [File Path]

- **Section**: [Section name or "New section"]
- **Change Type**: [Update/Add/Deprecate]
- **Reason**: Why this change is needed
- **Description**: What will be added/modified

#### 2. [File Path]

...

### Uncertainty Flags

- [ ] [Description of any assumptions or areas needing confirmation]

### No Changes Needed

- [List files reviewed but not requiring updates, with brief reason]
```

### Phase 6 summary format

```markdown
## Documentation Update Summary

### Changes Made

| File           | Change            | Related Code      |
| -------------- | ----------------- | ----------------- |
| path/to/doc.md | Brief description | link to PR/commit |

### Rationale

Brief explanation of why these updates were made.

### Review Notes

Any items reviewers should pay special attention to.
```

## Behavioral guidelines

### Conservative by default

- When uncertain whether to document something, flag it for human review
- Prefer smaller, focused updates over broad rewrites
- Do not "improve" documentation unrelated to the triggering code change

### Traceability

- Every documentation change should trace to a specific code change
- Include references to relevant commits, PRs, or issues in summaries

### Incremental updates

- Update existing sections rather than creating parallel documentation
- Maintain consistency with surrounding content
- Follow the established patterns in each documentation area
