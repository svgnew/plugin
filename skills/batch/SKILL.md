---
name: batch
description: "Batch vectorize multiple images to SVG at once. Triggers on: batch vectorize, convert all images, bulk svg, vectorize folder, batch convert."
---

# Batch Vectorize Images

Vectorize several images in one batch job.

## Before you start

Use the svg.new MCP tools from this plugin (named `mcp__plugin_svg-vectorizer_svg-new__*`, or `mcp__svgnew__*` if the user connected the server directly). They sign in with the user's svg.new account, so no API key is involved. If the tools are missing or say sign-in is needed, ask the user to run `/mcp`, pick the svg.new server and choose **Authenticate**.

Don't fall back to a local tracer (vtracer, potrace) unless the user asks for one.

## Steps

1. Find the images. Glob for files if the user names a folder or pattern. A batch holds up to 10 images; split larger sets into several batches.
2. Call `batch_vectorize` with the images as `image_urls` (public links), `images` (base64 data URLs, for small files) or `upload_ids` (files uploaded with `create_upload`, up to 20 MB each). These can be mixed.
3. Note the batch ID it returns, then call `get_batch_status` every few seconds until the status is `completed` or `failed`.
4. For each completed item, download its SVG from the link `get_batch_status` lists (valid for one hour) with `curl -sL -o <name>.svg "<link>"`, next to the original image.
5. Report how many succeeded and failed. Items can fail on their own without failing the batch.

## Notes

- Each image costs 1 credit. If the account runs short, the batch is refused before it starts.
