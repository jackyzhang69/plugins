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

Names are 1–200 Unicode characters after trimming. Body length is 1–131072 characters; each note has 1–32 sources, up to 20 topics and 30 tags. A source locator has at most 1000 characters. Omitted topics receive a default topic based on source kind. If selected material does not fit one note, prepare a clearly labeled set of notes and confirm the full selection; never silently omit a section or upload the raw source instead.

For an existing asset, read its current version before preparing a revision; retain its stable ID and add the expected version. A theme or label change must not create a duplicate note. The exact preview, target and version are bound by the prepare/commit workflow.
