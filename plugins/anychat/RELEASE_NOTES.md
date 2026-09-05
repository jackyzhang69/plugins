# AnyChat 0.1.53

## User-visible changes

- Verify that exported JPEG, PNG, GIF and WebP images can actually be decoded; file signatures alone no longer count as a usable image.
- Continue to an available thumbnail when the preferred local image is corrupt, and report failure without creating an unusable export when every candidate is corrupt.
- Preserve the decoded original image bytes, including supported wrapped image formats.
- Includes the 0.1.51 and 0.1.52 topic, setup recovery, partial-read and Windows running-version fixes.
