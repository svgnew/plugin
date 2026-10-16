---
name: remove-background
description: "Remove the background from an SVG using the original raster image. Triggers on: remove background, remove bg, transparent background, cut out background."
---

# Remove SVG Background

Remove the background from an SVG, using background detection on the original raster image.

## Before you start

Use the svg.new MCP tools from this plugin (named `mcp__plugin_svg-vectorizer_svg-new__*`, or `mcp__svgnew__*` if the user connected the server directly). They sign in with the user's svg.new account, so no API key is involved. If the tools are missing or say sign-in is needed, ask the user to run `/mcp`, pick the svg.new server and choose **Authenticate**.

## Steps

1. Identify the SVG and the original raster image it was made from.
2. Call `remove_background` with:
   - the SVG as `svg` (its text) or `svg_url` (a public link, such as a download link from an earlier result);
   - the original image as `image_url` (a public link), `image` (a base64 data URL, for files up to about 100 KB) or `upload_id` (after uploading the file with `create_upload`, up to 20 MB).
3. Save the background-free SVG. If it came back as a download link, save it with `curl -sL -o <file> "<link>"`.
4. The result also includes a transparent PNG of the original image; mention it, and save it if the user wants it.

## Notes

- Costs 1 credit.
- Needs both the SVG and the original image it was vectorized from.
