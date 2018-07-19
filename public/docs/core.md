## Installing and Running bBot

bBot is used as a dependency of a Node.js application, imported by either:

`import * as bot from 'bbot'` (JavaScript ES6) or

`const bot = require('bbot')` (JavaScript ES5)

When the bot is first imported, it has a status of `waiting` before any adapter
or middleware is loaded. At this point, its module prototypes could be modified
for specific behaviour, before their instances are created.

The main bBot module exposes all of its sub-module's methods and constructors.
Modules include, but are not limited to:
  - `config`      takes settings from command line or environment
  - `events`      emits and attaches event listeners
  - `brain`       sets and retrieves operational and user data
  - `logger`      writes logs to console and files
  - `thought`     processes incoming and outgoing messages
  - `middlewares` conduct logic for each stage of processing
  - `adapters`    parse information to and from external services

#### bBot Core <img src="/img/play.svg" />

The "core" module's methods change and query the bot's running status. All the
following methods are asynchronous, returning promises that can be used to add
behaviour when the status resolves.

- `bot.load()` initialises middleware and adapter classes
- `bot.start()` connects adapters to external services and waits for input
- `bot.shutdown()` will disconnect from services, keeping module instances
- `bot.pause()` does shutdown then returns to `loaded` for resuming as is
- `bot.reset()` is used for testing, to clear and return to `waiting` status

`start` can be called without first calling `load`, to do both in one.

`bot.getStatus()` returns the current status, being `waiting`, `loading`, 
`loaded`, `starting`, `started` or `shutdown`.

All status method emit events allowing apps hook into bBot startup/shutdown:
`bot.events.on('loaded|started|shutdown|paused|waiting', () => /* callback */)`

### Usage <img src="/img/code.svg" />

Simple

```js
import * as bot from 'bbot'

// ...add listeners, middleware

bot.start()
```

Advanced

```js
import * as bot from 'bbot'

class App {
  constructor () {
    this.bot = bot
    
    // ...overwrite bot configuration
    // ...extend bBot prototypes with custom features
  }

  async start() {
    await this.bot.load()

    // ...connect events to custom callbacks
    // ...modify logger with custom transports
    // ...add listeners, middleware
    
    this.bot.start()
  }
}

new App().start()
```