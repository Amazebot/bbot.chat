[examples]: https://github.com/Amazebot/bbot/tree/master/packages/bbot-examples/basic-branches
[conditions]: ./conditions
[nlu]: ./nlu
[dialogue]: ./dialogue
[state]: ./state

## Branching Basics

Branches are a core component of bBot's conversational UI. A branch defines a
piece of logic to execute when specific input is matched. It may trigger a
message response, some processing or possibly even create more branches.

Branch creation methods commonly accept three arguments.
- A **matcher** to define the conditions to look for
- A **callback** to call when the conditions are met
- An **options** object to modify behaviour (optional)

> 🧪 [Here's some examples of basic branches][examples]

There are two ways to create branches. _Global_ branches, that can match any
input without prior interaction are created directly from the bot.

All branch callbacks receive the conversation state `b`, which can be used to
create _dialogue_ branches that are only accessible from this context.

Global branch example:

```js
bBot.branches.text({ is: 'hello bot' }, (b) => b.respond('Hello 👋'))
```

Dialogue branches example:

```js
bBot.branches.text({ is: 'help' }, (b) => {
  b.respond('Need some help?')
  b.branches.text({ is: 'yes' }, (b) => b.respond('OK, I will try...'))
  b.branches.text({ is: 'no' }, (b) => b.respond('OK, never mind.'))
})
```

## Branch Types

Different types of branch match different types of input. The `branches` controller provides methods to create each type:

- `text` - matches a given text pattern
- `direct` - as above if prefixed with the bot's name or in DM
- `custom` - accepts a custom matching function
- `NLU` - matches criteria from an NLU service result
- `directNLU` - as above if prefixed with the bot's name or in DM
- `customNLU` - accepts a custom NLU result matching function
- `enter` - matches on users entering a chat room
- `leave` - matches on users leaving a chat room
- `topic` - matches on users changing the topic of a room
- `server` - matches on requests to a path on the bot's server
- `customServer` - accepts a custom request matching function
- `catchAll` - matches only when nothing else has

Branch types that match specific events don't require a matcher. These
include `enter`, `leave`, `topic`, `catchAll`. e.g.

```js
bBot.branches.enter((b) => b.respond('Welcome to the room!'))
```

## Matching Input

Received messages are processed against all branches, then callbacks for
matching branches are called. Normally only one branch can match, but branch
options can include a `force` attribute, to process the branch even if another
already matched.

For advanced text matching you can use regular expressions, but for simple
matching, branches accept a semantic **conditions** object. e.g.

```js
bBot.branches.text({ starts: 'hello' }, (b) => b.respond('Hello!'))
```

> 🎓 [Learn more about conditions attributes](/guides/conditions)

NLU branches accept a **criteria** object that is compared to results from a
natural language processing (NLP) service.

```js
bBot.branches.nlu({ sentiment: { lte: 0.2 } }, (b) => b.respond('Cheer up!'))
```

> 🎓 [Learn more about NLU criteria](/guides/nlu)

### Dialogue Branches

For simple confirmations or more complex conversations, you may need to limit
branch accessibility to a specific subset of users or paths of dialogue.
A **dialogue** instance controls matching subsets of branches in this context.

> 🎓 [Learn more about isolating dialogues](/guides/dialogue)

### Branch Callbacks

All branch callbacks receive the same single argument, the **state** object,
which provides context and utilities to take action. For brevity we call it `b`.

Callbacks can be asynchronous which is helpful for taking action that might
depend on external requests.

<a class="btn btn-primary" href="/guides/conversation-state">
  ▶️ Continue to learn about conversation states
</a>
