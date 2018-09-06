
## Built for Branching

> bBot builds better bots because it's built for branching.
> Bleep bloop.
> 
> -- bBot ©️ 2018

A **branch** is a piece of logic that applies when input is matched. It may
trigger a response or data processing, possibly even creating more branches.

Each branch has a matcher and a callback and may have other behaviours
configured by an options object. Branches are declared from a **path**, to be
processed against all incoming messages, or only following another branch.

There are different types of branch for matching different types of input, but
all callbacks follow the same pattern, which receives the state `b` argument
providing access to conversational context and utilities to take action.

When messages are received, branches are processed and callbacks for a matching
branch are called. Normally processing ends when a matching branch is found,
but branch options can set a `force` attribute so they will always be processed,
regardless if another branch has already matched.

A **path** is parent to a collection of branches. Path methods provide helpers
for adding different types of branches:

- [`text`](https://amazebot.github.io/bbot/classes/path.html#text)
  uses basic regular expression matching
- [`direct`](https://amazebot.github.io/bbot/classes/path.html#direct)
  as above, yet only if prefixed with the bot's name
- [`custom`](https://amazebot.github.io/bbot/classes/path.html#custom)
  accepts a custom matching function
- [`enter`](https://amazebot.github.io/bbot/classes/path.html#enter)
  matches on users entering a chat room
- [`leave`](https://amazebot.github.io/bbot/classes/path.html#leave)
  matches on users leaving a chat room
- [`topic`](https://amazebot.github.io/bbot/classes/path.html#topic)
  matches on users changing the topic of a room
- [`NLU`](https://amazebot.github.io/bbot/classes/path.html#nlu)
  uses criteria for natural language understanding
- [`directNLU`](https://amazebot.github.io/bbot/classes/path.html#directnlu)
  as above, yet only if prefixed with the bot's name
- [`customNLU`](https://amazebot.github.io/bbot/classes/path.html#customnlu)
  accepts a custom NLU result matching function
- [`catchAll`](https://amazebot.github.io/bbot/classes/path.html#catchall)
  matches only when nothing else has

### Branch Matching

When processing incoming messages, each branch's matching function is applied.
If the function returns a truthy value (e.g. a regex match object), then the
callback is fired.

Text branches accept semantic attributes handled by our **conditions** module,
such as `{ contains: 'hello' }` or `{ is: 'cancel' }`. For more advanced text
matching you can use regular expressions instead.

Read the Conditions docs for details of the available attributes.

NLU branches accept a standard **criteria** object, defining the conditions that
should be met in the result that is returned from natural language processing.

Read the NLU doc for more details on using NLU criteria.

### Branch Callbacks

All branch callbacks receive the same single argument, the bot **state** object. 
For brevity we call it `b`. From it you can access the bot, the message, it's
match result. The state instance also has helpers for responding.
Basically everything you need to take action.

Callbacks can be asynchronous and processing will await their resolution,
allowing great flexibility for building responses or processing logic that might
depend on external requests or data querying.

### The "Global" Path

Paths give us a way of isolating branches so we don't always process everything.
In some contexts we may need to limit accessibility of branches. However, bBot
has a **global** root path, for adding all branches that should be accessible
without specific context.

## Usage

Matching Pattern

```js
// Respond when someone says hello
bot.global.text(/hello/i, (b) => b.respond('Hello!'))
```

Matching Conditions

```js
// Respond when someone says hello
bot.global.text({ contains: 'hello' }, (b) => b.respond('Hello!'))
```

Custom Matching

```js
// Respond only on Wednesdays (with branch ID)
bot.global.custom((message) => {
  if (new Date().getDay() !== 3) return false
  else return /hello/i.test(message.toString())
}, (b) => b.respond('Hello!'), {
  id: 'wednesday-hello'
})
```

Force Matching

```js
// React when someone says hi or hello
bot.global.text({
  contains: [ 'hi', 'hello' ]
}, (b) => b.respondVia('react', ':wave:'), {
  id: 'hello-react'
})

// Add another emoji to reaction when text contains "baby"
bot.global.text({
  contains: 'baby'
}, (b) => b.respondVia('react', ':baby:'), {
  id: 'baby-react', force: true
})
```
---

<a href="/docs/thought" class="btn btn-secondary">Learn more about `b` ➮</a>