Opening a dialogue will route any further input from the user/s in state to
the dialogue branches instead of the "global" bot branches, until closed.
Dialogues are self-closing on timeout, or if a branch handler is processed
without adding more branches.

When accessing branches from the current state (`b.branches` instead of
`bot.branches`) any created branches will be isolated by the state's dialogue
and not accessible to users not engaged in that dialogue. Also, users in that
dialogue will have their incoming messages routed to only match against the
branches in the dialogue branches. i.e. they will not trigger "global" bot
branches (until closing dialogue)

By accessing the state's branches, a dialogue is implicitly created for the
state with default config. However, it can be explicitly created first, to
configure specific behaviour, like custom timeouts or multi-user audiences.

The `audience` property determines which user or users related to the state
are in dialogue (to route their input). It can be configured a few ways:
 - `direct` (default) the user in the current room
 - `user` the user in any room (to continue dialogue across multiple rooms)
 - `room` all users in the current room (allows anyone to continue dialogue)
 