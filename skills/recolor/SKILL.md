---
name: recolor
description: "Change colors in an SVG file. Map old colors to new colors. Triggers on: recolor svg, change svg color, replace color in svg, swap colors."
---

# Recolor SVG

Replace colors in an SVG by mapping old colors to new ones.

## Before you start

Use the svg.new MCP tools from this plugin (named `mcp__plugin_svg-vectorizer_svg-new__*`, or `mcp__svgnew__*` if the user connected the server directly). They sign in with the user's svg.new account, so no API key is involved. If the tools are missing or say sign-in is needed, ask the user to run `/mcp`, pick the svg.new server and choose **Authenticate**.

## Steps

1. Read the SVG named in the request or `$ARGUMENTS`.
2. Work out which colors to replace. Ask the user if they didn't say.
3. Build a color map such as `{ "#ff0000": "#0000ff" }`.
4. Call `recolor_svg` with `color_map` and the SVG, either as `svg` (the file's text) or as `svg_url` (a public link, such as a download link from an earlier result).
5. Write the result back to the file, or to a new file if the user prefers. If it came back as a download link, save it with `curl -sL -o <file> "<link>"`.

## Notes

- Free: no credits are used.
- Colors match case-insensitively. Hex (#RGB, #RRGGBB), named colors and rgb() values all work.
- Fill colors change; strokes are left alone.
