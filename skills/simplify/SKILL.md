---
name: simplify
description: "Reduce the number of colors in an SVG using quantization. Triggers on: simplify svg, reduce svg colors, fewer colors, quantize svg, minimize svg colors."
---

# Simplify SVG Colors

Reduce the number of distinct colors in an SVG with median-cut quantization.

## Before you start

Use the svg.new MCP tools from this plugin (named `mcp__plugin_svg-vectorizer_svg-new__*`, or `mcp__svgnew__*` if the user connected the server directly). They sign in with the user's svg.new account, so no API key is involved. If the tools are missing or say sign-in is needed, ask the user to run `/mcp`, pick the svg.new server and choose **Authenticate**.

## Steps

1. Read the SVG named in the request or `$ARGUMENTS`.
2. Decide `max_colors`. Ask the user if they didn't say; 2, 4, 8 and 16 are common.
3. Call `simplify_svg` with `max_colors` and the SVG, as `svg` (the file's text) or `svg_url` (a public link, such as a download link from an earlier result).
4. Report the color count before and after.
5. Write the simplified SVG to the file. If it came back as a download link, save it with `curl -sL -o <file> "<link>"`.

## Notes

- Free: no credits are used.
- `max_colors` can be 1 to 256.
- Similar colors merge; distant colors stay distinct. Gradient stops are reduced too.
- Shapes with `fill="none"` are not affected.
