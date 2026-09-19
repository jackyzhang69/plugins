# Prepare the content

The host prepares structured input for the user; never ask the user to write JSON or run a terminal command. Start from the material the user selected and the purpose they gave. Draft only the reusable knowledge they want to retain and preserve their qualifications, opinions, applicable context, and uncertainty. Show the complete proposed content and hosting scope before confirmation.

Personal thoughts and existing notes may be drafted directly. For a document or web selection, use the host's available reading tools within the user's authorization and select the relevant passage or summary locally. Record the available document title/page or selected URL as provenance; do not invent a source, timestamp or successful availability check. Storing a URL does not authorize a later visit. AnyChat input is a selected host-written summary explicitly confirmed for saving, never a raw archive, attachment, or automatic background sync. Existing chat and industry-note features retain their own records and lifecycle.

A new mutation has this shape (the text below is synthetic):

```json
{
  "action": "create",
  "content": {
    "title": "我的练习复盘方法",
    "body": "这是我的个人经验：每次练习后记录一个需要改进的地方，下次先检查这一点。",
    "sources": [{
      "kind": "personal",
      "label": "本人本次提供的经验",
      "locator": null,
      "original_time": null,
      "availability": "unknown"
    }],
    "topics": ["学习方法"],
    "tags": ["个人经验"]
  }
}
```

`kind` is one of `personal`, `note`, `document`, `web`, or `anychat`. The label identifies the actual source; `locator` is an optional page, URL, or reference supplied by the selected material. `original_time` is an optional known RFC3339 timestamp with a time-zone offset, such as `2026-09-13T10:20:30-07:00`; leave it null when unknown. Availability is `available`, `unavailable`, `not_checked`, or `unknown`, based on observed evidence. The service records confirmation time separately, so it never substitutes for the original content time.

Names are 1–200 Unicode characters after trimming. Body length is 1–131072 characters; each note has 1–32 sources, up to 20 topics and 30 tags. A source locator has at most 1000 characters. Omitted topics receive a default topic based on source kind.

## Organize knowledge pieces

Decide how many notes the selected material should become before you draft JSON. The person confirms the set; they do not design a filing system. You restructure for later reuse; you do not twist their meaning. Keep their qualifications, numbers, and distinctive wording.

1. One reusable idea or method per note. Title plus body must still make sense months later without the original chat or document. Fill in missing subjects. Do not write “as above” or “first point” that only works inside a sequence.
2. Split long or mixed material into a clearly labeled set and show that whole set in one confirmation. Never silently drop a section. Never save the raw file or the entire page as one note.
3. Keep one coherent checklist, matrix, or method together when the person will reuse it as a whole. Do not shatter it into dozens of scraps.
4. Reuse existing topic names when they fit. Topics are the main way related notes stay together. Do not invent a folder tree.
5. Use few tags, and only ones that help find or group the note. Do not turn every noun in the body into a tag.
6. After you have read current matching notes, you may suggest a related or replacement link to those exact items. Do not search the whole library just to invent connections. Suggestions stay unconfirmed until the person says yes.
7. Put the same source locator on every note split from one document, page, or chat excerpt. Do not invent a source, timestamp, or availability check.

For an existing asset, read its current version before preparing a revision; retain its stable ID and add the expected version. A theme or label change must not create a duplicate note. The exact preview, target and version are bound by the prepare/commit workflow.
