# Accessibility Plugin Marketplace

Public Claude Code plugin marketplace maintained by Blackbird Agency. It provides accessibility skills for Claude Code, covering ARIA component patterns and RGAA compliance.

- **Marketplace**: `synolia-accessibility`
- **Repo**: `blackbird-agency/rgaa-accessibility-marketplace`

## Available plugins

| Plugin | Content |
|--------|---------|
| `accessibility` | Accessibility skills: ARIA component patterns, RGAA compliance |

## Installation

### Enable plugins per project

In the project's `.claude/settings.json`:

```json
{
  "enabledPlugins": {
    "accessibility@synolia-accessibility": true
  }
}
```
