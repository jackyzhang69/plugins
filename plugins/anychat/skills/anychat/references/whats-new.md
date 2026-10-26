# AnyChat — what changed in 0.1.102

- Linux 版和其它平台一起发布：同一次发布里包含 Linux 程序，清单并列三个平台。
- 云端助手（例如 Muse）里也能查聊天了：只发送这段时间的纯文本快照，附件、图片、语音只保留类型；我们不保留副本。

- 云端助手（例如 Muse）里也能查聊天了：说一句「在云端助手里查聊天」，助手会准备一份这段
  时间的快照并上传，答完你就能继续追问。附件、图片、语音只保留类型，不发送内容。
- 快照只包含你这次问的时间范围；我们不保留副本，聊完可以让助手把这份快照删掉。

# AnyChat — what changed in 0.1.100

- Setup now states the two things that actually unblock a stuck archive: on macOS, Full Disk Access goes to the app that runs your assistant (not to AnyChat itself); on Windows, use one supported chat-app build, allow the permission prompt, and keep the chat app signed in.
- Failure codes now name the first action, so nothing asks you to reinstall the chat app or repeat an identical scan.
