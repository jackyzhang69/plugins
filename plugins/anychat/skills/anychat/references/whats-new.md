# What's new in 0.1.62

Support now keeps track of tasks, progress and results so a lost connection does not silently consume a pending message. With support hooks enabled and trusted, your current assistant can continue exchanging progress and follow-up questions.

Interrupting support stops its old receiver. When you explicitly resume, your assistant can recover pending messages in the same approved session. Closing support ends unfinished tasks and preserves completed results. Your assistant continues to control all local actions.
