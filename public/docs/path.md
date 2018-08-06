
## Built for Branching

> bBot builds better bots because it's built for branching.
> Bleep bloop.
> 
> -- bBot ©️ 2018

A **branch** is essentially a matching function and a callback. When messages are
received, each branch is processed and its callback called if matched.

Normally processing ends when a matching branch is found, but branches can have
a `force` attribute, so they will always be processed, regardless if another
branch has already matched. They can also overrule further processing.

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

Simple

```js
import * as bot from 'bbot'

// Respond when someone says hi to the bot
bot.global.textDirect(/hi/i, (b) => b.respond('Hello!'))

// React with a wave emoji when anyone says hi
bot.global.text(/hi/i, (b) => b.respondVia('react', '👋'))

bot.start()
```

Advanced

```js
const bot = require('bbot')
const { get } = require('https')

// Respond using an attribute from the match
bot.global.text(/door number ([1-3]{1})/, async (b) => {
  switch (b.match[1]) {
    case '1': await b.respond(`You win nothing 💔`); break
    case '2': await b.respond(`You win a monkey 🐒`); break
    case '3': await b.respond(`It's a new car!! 🚗`); break
  }
})

// Respond after some processing, e.g. requesting data
bot.global.text(/corporate/i, async (b) => {
  const bs = await new Promise((resolve, reject) => {
    get('https://corporatebs-generator.sameerkumar.website/', (res) => {
      let data = ''
      res.on('data', (chunk) => { data += chunk })
      res.on('end', () => resolve(JSON.parse(data).phrase))
      res.on('error', (err) => {
        b.bot.logger.error(`Failed getting corporate bs ${err.message}`)
        resolve()
      })
    })
  })
  if (bs) await b.respond(bs)
})

// Let them know the bot is ALWAYS watching (even if already responded)
bot.global.text(/anyone/i, (b) => b.respondVia('react', '👀'), {
  force: true
})

// Take decisive action when all else fails
bot.global.catchAll((b) => b.respondVia('react', '🤷‍'))

bot.start()
```

Test the above with inputs like:
  - "what does corporate say?"
  - "what's behind door number 2?"
  - "is anyone out there?"
  - "fhwdgads!"

---

<a href="/docs/thought" class="btn btn-secondary">Learn more about `b` ➮</a>