# svg.new Plugin for Claude Code

Convert images to SVG vectors, edit colors, simplify paths, remove backgrounds, and batch process — directly from Claude Code.

The plugin connects Claude to the hosted svg.new MCP server (`https://svg.new/api/mcp`) and adds skills that tell Claude when and how to use it. You sign in with your svg.new account; there is no API key to copy.

## Install

### Option 1: Plugin marketplace (recommended)

```
/plugin marketplace add svgnew/plugin
/plugin install svg-vectorizer@svg-new-tools
```

Then run `/mcp`, pick the `svg-new` server from the plugin, and choose **Authenticate** to sign in to svg.new in your browser.

### Option 2: Only the MCP server

```bash
claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp
```

Then `/mcp` → `svgnew` → **Authenticate**. The same URL works as a custom connector in the Claude apps and in any MCP client that supports OAuth.

### Option 3: Local MCP server with an API key

For MCP clients without OAuth, run the npm package with an API key from [svg.new/account](https://svg.new/account):

```bash
claude mcp add svg-new -e SVG_NEW_API_KEY=svk_your_key_here -- npx -y @svgnew/mcp
```

### Option 4: Skill only

```bash
mkdir -p ~/.claude/skills/svg-new && curl -s https://svg.new/skill.md -o ~/.claude/skills/svg-new/SKILL.md
```

## Accounts and credits

Sign up at [svg.new](https://svg.new). Free accounts get 3 vectorizations a day; Pro gets 1,000 a month. Vectorizing and background removal use credits; recoloring and simplifying are free.

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

- `vectorize` — Convert a PNG, JPEG or WebP image to SVG (inline, by public URL, or by `upload_id`)
- `create_upload` — Get an upload link for sending a local image up to 20 MB without pasting it into the chat
- `recolor_svg` — Change SVG colors
- `simplify_svg` — Reduce an SVG's color palette
- `remove_background` — Remove an SVG's background using the original image
- `batch_vectorize` / `get_batch_status` — Process many images and poll progress
- `list_vectorizations` — View your recent conversions
- `vectorize_file`, `remove_background_file`, `batch_vectorize_files` — The same, for clients that pass attached files

Large inputs go by reference (`image_url`, `upload_id`, `svg_url`), and large results come back as one-hour download links.

## Privacy

svg.new processes the images and SVGs you send it to produce your results and keeps your conversion history in your account. See the [privacy policy](https://svg.new/privacy) and [terms](https://svg.new/terms).

## Support

[svg.new/support](https://svg.new/support) or contact@svg.new.

## Links

- [svg.new](https://svg.new) — Web app
- [API Documentation](https://svg.new/developers) — REST API reference
- [Pricing](https://svg.new/pricing) — Plans and credits
