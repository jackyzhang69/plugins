# AnyPDF — what changed in 0.7.14

This patch makes sign-in more reliable when a Windows computer and the account service differ slightly in their clocks. A valid session can continue instead of appearing disconnected during that small timing difference.

AnyPDF continues the task you were already doing through the same router. Connection and Tell-Jacky use the shared Jacky sign-in flow, and feedback counts as delivered only after the service confirms receipt.

The live catalog remains the source of truth for available forms. Fill and intake still use the user's accepted facts and report verified results; AnyPDF does not guess missing facts or submit an application for the user. Mac and Windows provide the same skill experience.
