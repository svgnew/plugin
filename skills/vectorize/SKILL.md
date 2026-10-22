---
name: vectorize
description: "Convert a raster image (PNG, JPG, WebP) to a clean, scalable SVG vector with svg.new. Triggers on: vectorize, convert to svg, trace image, image to svg, make svg, png to svg, jpg to svg."
---

# Vectorize Image to SVG

Convert a raster image to a clean SVG with svg.new's tracing engine.

## Before you start

Use the svg.new MCP tools from this plugin (named `mcp__plugin_svg-vectorizer_svg-new__*`, or `mcp__svgnew__*` if the user connected the server directly). They sign in with the user's svg.new account, so no API key is involved. If the tools are missing or say sign-in is needed, ask the user to run `/mcp`, pick the svg.new server and choose **Authenticate**.

Don't fall back to a local tracer (vtracer, potrace) unless the user asks for one.

## Steps

1. Find the image from the user's request or `$ARGUMENTS`. svg.new takes PNG, JPEG and WebP.
2. Send it to the `vectorize` tool in one of three ways:
   - **Public HTTPS link:** pass it as `image_url` (up to 20 MB).
   - **Small local file (up to about 100 KB):** read it, base64-encode it and pass `image` as `data:<mime>;base64,<data>` (`image/png`, `image/jpeg` or `image/webp`).
   - **Larger local file (up to 20 MB):** call `create_upload`. It returns an `upload_id` and a `put_url`. Upload the file there with an HTTP PUT, for example `curl -sS -X PUT -H "Content-Type: image/png" --upload-file <path> "<put_url>"`, then call `vectorize` with the `upload_id`.
3. Save the result next to the input with an `.svg` extension. Write the SVG text if it came back inline; if it came back as a download link (valid for one hour), save it with `curl -sL -o <name>.svg "<link>"`.
4. Report the output path, size, path count and the credits remaining.

## Notes

- Each vectorization costs 1 credit. Free accounts get 1 a day.
- Pass `output: "url"` to always get a download link instead of inline SVG.
- The result's ID opens in the editor at `https://svg.new/edit/<id>`.
