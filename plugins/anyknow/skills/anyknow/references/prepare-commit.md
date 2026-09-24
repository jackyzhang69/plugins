# Prepare and commit

Create a strict mutation JSON file or pipe it on stdin. Run `anyknow prepare --input <path-or-> --output <new-path>`. The command makes no network request. Directory symlinks that resolve to real directories, and an input file symlink that resolves to a regular file, are allowed. The output path must be a new regular file: an existing file or a symlink save path fails with `save_path_symlink`.

To save several related notes under one confirmation, write `schema=anyknow-batch-v1` with up to 20 items. Each item is a mutation and may include a unique `batch_key` so `relation_suggestions` can point at another item in the same set. Prepare still stays local; commit sends the batch to `POST /v1/operations:batch` using the displayed batch digest. The server checks every suggestion (batch key or existing asset) before anything is kept. Suggestions stay unconfirmed.

Show the returned `preview` in full, including title, body, sources, topics, tags, action, target ID, expected version, and the private AnyKnow hosting scope. The local file does not assert confirmation. After the user confirms that exact preview, pass the displayed digest to `anyknow commit --input <prepared-path> --confirm-sha256 <digest>`; commit adds `confirmed=true` only after rechecking the binding. Any change to the operation ID or request bytes invalidates the digest and requires a new preview.

`create` and `revise` require Content. `revise`, `archive`, `delete`, and `rename_topic` require a target UUID and expected version. `rename_topic` uses the topic UUID as `asset_id` and `{ "name": "..." }` as content. Never infer consent from an earlier or similar preview.

An `operation_outcome_unknown` response means the network deadline elapsed and the server may have committed. Keep the exact prepared file and confirmation; first query `anyknow operation --operation-id <same-id>`, then retry that same confirmed operation if no receipt exists. Do not create a new operation or claim the save failed. `knowledge_quota_exceeded` means the complete account history would exceed 64 MiB; nothing from that operation was committed. Never discard old revisions to fit an import; explain the limit and preserve the local export.

For a complete portable restore, keep the JSONL export as the local snapshot and run `anyknow prepare-import --input <snapshot.jsonl> --output <new-prepared.json>`. The command makes no network request, validates every line plus the final counts, and reports the exact file hash, asset/revision counts, private hosting scope, operation ID, and confirmation hash. Show those values to the user. After confirmation, run `anyknow commit-import --input <prepared.json> --confirm-sha256 <displayed-hash>`. The CLI reopens the snapshot without following links, revalidates and rehashes that same file handle before reading configuration or contacting the service, then streams the exact bytes. Retrying the same prepared file keeps the same operation ID; use `operation` to check an uncertain response. A changed snapshot requires a new prepare and confirmation.

For a knowledge relationship, read both current items first. `link` content is
`{ "source_id": "<uuid>", "target_id": "<uuid>", "source_version": 1,
"target_version": 1, "kind": "related_to", "note": "optional explanation" }`.
`related_to` means related in both directions; `supersedes` means the source replaces
the target. Never infer replacement from dates. Show both titles, IDs, versions,
direction and explanation in the exact preview. The same `prepare`/`commit` flow applies.
`reconfirm_link` adds `link_id` and `expected_link_version` to this mutation;
`remove_link` takes those two fields without content. Never silently reconfirm a stale edge.

A create/revise mutation can include up to 20 `links` containing target_id,
target_version, kind and optional note. These share the new content's confirmation
and all save together. Before deleting an item, page through `anyknow links --asset-id <uuid>`
until complete, show all affected relationships, and bind their link_id/version pairs
in `expected_links`. A changed set requires a new preview. A relationship conflict
never authorizes deleting the other endpoint or discarding the content.

Recognizable encoded original-file payloads are refused locally and by the service.
Use the user's authorized local reading tools to prepare selected text; never retry by
encoding an original file, uploading it for temporary parsing, or removing its markers.
