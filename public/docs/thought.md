The bBot thought process describes how this clever and purposeful bot
elegantly handles discourse.

It involves a sequence of state processing steps, each with middleware for
adding your own subroutines. Each receives the state object, we call `b`.
We'll come back to that.

The following sequence starts when a messaging platform gives an incoming
message to the bot...

### 1. Hear

bBbot hears all messages to determine if the context requires attention.
It gather information for better listening, or ignores if it shouldn't listen.

Add a middleware piece via `.middleware.hear` to interrupt the process or modify
the state before further processing.

### 2. Listen

bBot evaluates the message against the basic pattern or event matching branches
in the current path. If matched, callbacks can **respond** and further a
conversation or complete a simple exchange.

Add a middleware piece via `.middleware.listen` to fire on every matching
branch, to interrupt or modify the state.

### 3. Understand

bBot can use natural language services to match on the _intent_ rather than the
exact content. The nature of the message is collected from external providers.

NLU branches evaluate the intent, entities and/or sentiment of the message, by
first sending the message to the natural language adapter.

Add a middleware piece via `.middleware.understand` to execute on every matching
language branch, to interrupt or modify the state.

### 4. Act

bBot can take action when all else fails. This is an outcome of `listen` and
`understand` both passing without any branch matching.

bBot creates a special catch-all message type which will match against any
existing catch-all branches (usually you'd make just one, or none), to take
action when nothing else did.

Add a middleware piece via `.middleware.act` to execute on each catch-all branch,
to interrupt or modify the state.

### X. Respond

bBot replies to the people it's engaged with appropriately. Canned responses
are mixed with context and may include rich UI elements.

Add a middleware piece via `.middleware.respond` to execute on any sends, if
matched callbacks prompted a response.

Respond is processed after a matching listen, understand or act branch, so it
could come sooner or later, or not at all. A bot can also dispatch a message
unprompted, in which case _respond_ is the first thought process.

### 5. Remember

bBot can remember everything in the current state, the person, context and
content. Important details are kept for quick access. Everything else is stored
for safekeeping by the storage adapter for your choice of data provider.

Remember is always the last step in the sequence, for incoming and outgoings.

That all might take a few milliseconds, then we're ready to start again.

## The Bot State

State instances (`b`) provide access to every attribute needed to process, take
action and store incoming and outgoing messages and events. Including:

- `bot` is the instance of the bot itself, e.g. `b.bot` 🤔
- `done` flags if branch matching is complete.
- `processed` keeps timestamps of all processed thoughts
- `branches` collects any matched branches
- `match` returns the latest branch's match
- `matched` a boolean if any branch has matched
- `message` is the incoming message object
- `envelopes` collects outgoing dispatches
- `sequence` identifies the origin of processing
- `scope` relates to the path, "global" or otherwise
- `method` is set depending on how respond is handled
- `exit` flags that the state should be ignored

Every middleware piece and branch callback receives the same state instance as
it moves through the thought process. So they can read its history or write its
future.

## Usage

The thought process is internal magic, it can be manipulated to create custom
sequences, but that's for l33t haxxors only, we won't cover that in the basic
guides.

Usage examples for state can be found on the
[Conversation Branching](/docs/path) and [Middleware](/docs/middleware) guides.

---

<a href="/docs/adapters" class="btn btn-secondary">Learn about adapters ➮</a>
