# svg.new Plugin for Claude Code

Convert images to SVG vectors, edit colors, simplify paths, remove backgrounds, and batch process — directly from Claude Code.

## Install

### Option 1: Plugin marketplace (recommended)

```
/plugin marketplace add svgnew/plugin
/plugin install svg-vectorizer
```

### Option 2: One-liner skill install

```bash
mkdir -p ~/.claude/skills/svg-new && curl -s https://svg.new/skill.md -o ~/.claude/skills/svg-new/SKILL.md
```

### Option 3: MCP server

Add to your Claude Code MCP config (`~/.claude/.mcp.json`), or run `claude mcp add svg-new -e SVG_NEW_API_KEY=svk_your_key_here -- npx -y @svgnew/mcp`:

```json
{
  "mcpServers": {
    "svg-new": {
      "command": "npx",
      "args": ["-y", "@svgnew/mcp"],
      "env": {
        "SVG_NEW_API_KEY": "svk_your_key_here"
      }
    }
  }
}
```

## Setup

1. Sign up at [svg.new](https://svg.new). Free accounts get 3 vectorizations a day; Pro gets 1,000 a month.
2. Generate an API key at [svg.new/account](https://svg.new/account)
3. Set your key: `export SVG_NEW_API_KEY="svk_..."`

## Available Skills

| Skill | Command | Cost |
|-------|---------|------|
| **Vectorize** | `/svg-vectorizer:vectorize logo.png` | 1 credit |
| **Recolor** | `/svg-vectorizer:recolor output.svg` | Free |
| **Simplify** | `/svg-vectorizer:simplify output.svg` | Free |
| **Remove Background** | `/svg-vectorizer:remove-background output.svg` | 1 credit |
| **Batch** | `/svg-vectorizer:batch ./images/` | 1 credit/image |

Or just ask naturally — the skills trigger automatically:
- "vectorize this logo"
- "convert all PNGs in this folder to SVG"
- "change the red to blue in icon.svg"
- "reduce this SVG to 4 colors"

## MCP Tools

When using the MCP server, these tools are available to any MCP-compatible client:

- `vectorize` — Convert image to SVG
- `recolor_svg` — Change SVG colors
- `simplify_svg` — Reduce color palette
- `remove_background` — AI background removal
- `batch_vectorize` — Process multiple images
- `get_batch_status` — Poll batch progress
- `list_vectorizations` — View history

## Links

- [svg.new](https://svg.new) — Web app
- [API Documentation](https://svg.new/api) — REST API reference
- [Pricing](https://svg.new/pricing) — Plans and credits
