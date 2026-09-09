# Capability families

Runtime answers still come from live `commands --json`. This file is the
official-publish expansion: every public command family a human can ask for.

## connect
phrases: connect to anychat / set up anychat; log in to anychat / save my portal token; anychat setup
playbook: connect.md
commands: login, logout, whoami, doctor, update, status, provision

## search
phrases: search my chats; chat with X; cross-platform chat history
playbook: query.md
commands: query, search, friends, groups, contacts, resolve, sessions, alias, recents

## export
phrases: export messages with X; save group transcript; export that PDF
playbook: export.md
commands: export

## media
phrases: download images from group Y; list voice messages; download voice messages; transcribe this voice
playbook: media.md
commands: media

## evidence
phrases: evidence pack; proof of what was said
playbook: command-router.md
commands: evidence

## feedback
phrases: tell Jacky; report this bug / file a bug report
playbook: tell-jacky.md
commands: feedback

## pair
phrases: connect with Jacky; join code from Jacky; request live support
playbook: pair-session.md
commands: pair

## sources
phrases: iMessage; Telegram; other local chats
playbook: command-router.md
commands: sources

## identity
phrases: identity; same person across apps
playbook: command-router.md
commands: identity

## topics
phrases: follow this person; save a topic; write this down
playbook: command-router.md
commands: topic, notes

## discovery
phrases: what can anychat do
playbook: command-router.md
commands: commands
