---
name: batch
description: "Batch vectorize multiple images to SVG at once. Triggers on: batch vectorize, convert all images, bulk svg, vectorize folder, batch convert."
---

# Batch Vectorize Images

Submit multiple images for vectorization in a single batch job.

## Prerequisites

Use the svg.new MCP tools (`mcp__svgnew__*`) if they are available in this session; they need no API key. Otherwise the `SVG_NEW_API_KEY` environment variable must be set. If neither is available, tell the user:
> Connect svg.new with `claude mcp add --transport http --scope user svgnew https://svg.new/api/mcp` and `/mcp` → Authenticate, or generate an API key at https://svg.new/account and set `export SVG_NEW_API_KEY="svk_..."`.

Do not fall back to a local tracer (vtracer, potrace) unless the user asks for one.
## Steps

1. Identify the images to process — glob for files if the user specifies a directory or pattern
2. For each image, base64-encode it with the appropriate data URL prefix
3. Submit the batch (max 50 images):

```bash
curl -s -X POST "${SVG_NEW_API_URL:-https://svg.new}/api/agent/batch" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"images": [{"image": "data:image/png;base64,..."}, ...]}'
```

4. Extract the `batch_id` from the 202 response
5. Poll for completion:

```bash
curl -s "${SVG_NEW_API_URL:-https://svg.new}/api/agent/batch/$BATCH_ID" \
  -H "Authorization: Bearer $SVG_NEW_API_KEY"
```

6. When `status` is `completed`, download each item's SVG from its `svg_url`
7. Save each SVG alongside the original image (same name, `.svg` extension)
8. Report results: total processed, succeeded, failed

## Notes

- Maximum 50 images per batch
- Each image costs 1 credit
- Check credits before submitting — the API returns 402 if insufficient
- Poll every 5 seconds until `status` is `completed` or `failed`
- Individual items can fail without failing the whole batch — check each item's `status`
