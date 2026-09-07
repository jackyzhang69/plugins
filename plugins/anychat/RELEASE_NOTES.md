# AnyChat 0.1.62

## User-visible changes

- Support conversations preserve tasks, progress and results across a lost connection. Receiving a message does not silently mark it handled.
- Your assistant can exchange follow-up questions and progress with support while staying in the same conversation, with its support hooks enabled and trusted.
- Interrupting support stops the old receiver from reporting itself online. Explicitly resuming continues the approved session and recovers pending messages.
- Closing or expiring a support session ends unfinished tasks while retaining completed results.
- The client checks the communication protocol before sending work and reports incompatible support endpoints instead of proceeding.
