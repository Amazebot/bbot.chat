## I Think, Therefore I Chat

The bBot _thought process_ details how your bot elegantly handles discourse. It
involves sequences of state processing functions, which we call _thoughts_. Each
sequence begins by constructing the state from an incoming or outgoing message
or http/s request.

> 🎓 [Review conversation state attributes](/guides/conversation-state)

Described below is the most common thought sequence, `receive`. It starts when
a messaging platform adapter gives an incoming message to the bot. The following
guide details how to use _middleware_ to add custom logic to each thought.

### Hear

First bBot _hears_ the messages to determine if the context requires attention.
It gathers information for better listening or ignores if it shouldn't listen.

### Listen

bBot evaluates the message against the subset of branches appropriate for the
message's user's current context (global or dialogue). If matched, callbacks can
respond and continue or complete the interaction.

### Understand

If the message wasn't resolved, bBot can evaluate the message using an external
NLU service, then NLU branches can match on the intent, entities and/or
sentiment result.

bBot uses NLU as a fallback to its internal matching, to reduce latency and cost
of unnecessarily processing every message in the cloud. We call this
_artificial stupidity_.

### Respond

bBot replies to the people it's engaged with appropriately. Responses may use
multiple messages, rich UI elements and fill templates with values from context.

Respond is processed after a matching listen, understand or act branch, so it
could come sooner or later, or not at all. A bot can also dispatch a message
unprompted, in which case, respond is the first thought process.

### Act

bBot can take action when all else fails. This is optional and only applies
when both _listen_ and _understand_ complete without any branch matching.

bBot creates a special catch-all message type which will match against a
catch-all branches (if it exists), to take action when nothing else did.

### Remember

bBot can remember everything in the current state, the user, context and
content. Important details are kept in memory for quick access. Everything else
can be persisted with your choice of storage provider, using an adapter.

Remember is always the last step in the sequence, for incoming and outgoings.

<a class="btn btn-primary" href="/guides/middleware">
  ▶️ Continue to learn about using middleware
</a>
