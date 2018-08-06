[gh-starter]: https://github.com/Amazebot/bbot-starter
[gl-starter]: https://glitch.com/edit/#!/remix/bbot-starter

## Importing the Package

bBot is a dependency of a Node.js application.
Let's start a new project and install it...

```shell
➜ code $ mkdir mybot
➜ code $ cd mybot
➜ mybot $ npm init -y
➜ mybot $ npm install --save bbot
➜ mybot $ touch index.js
```

Then import the package in **index.js** by either:

`import * as bot from 'bbot'` (JavaScript ES6) or

`const bot = require('bbot')` (JavaScript ES5)

Now without any further setup you can start testing interactions by chatting in
your terminal. Just run `node index.js`.

Or use our starter code on Github or Glitch and start hacking! ✨

<a href="https://github.com/Amazebot/bbot-starter" class="btn btn-secondary">Fork on Github</a>
<a href="https://glitch.com/edit/#!/remix/bbot-starter" class="btn btn-secondary">Remix on Glitch</a>

Bonus points for Typescript devs, bBot is exported with definitions. Yay types!

## Package Modules

The main bBot module exposes all of its sub-module's methods and constructors.
Modules include, but are not limited to:
  - `config`      takes settings from command line or environment
  - `events`      emits events and runs event callbacks
  - `core`        controls status and initialises sub-modules
  - `brain`       sets and retrieves operational and user data
  - `logger`      writes logs to console and files
  - `adapter`     parses information to and from external services
  - `thought`     processes incoming and outgoing messages
  - `middleware`  conducts logic for each stage of processing
  - `branch`      matches incoming messages to a behaviour
  - `path`        defines conversational branches in context

#### bBot Core <img src="/img/play.svg" />

The "core" module's methods change and query the bot's running status. All the
following methods are asynchronous, returning promises that can be used to add
behaviour when the status resolves.

- `bot.load()` initialises middleware and adapter classes
- `bot.start()` connects adapters to external services and waits for input
- `bot.shutdown()` will disconnect from services, keeping module instances
- `bot.pause()` does shutdown then returns to `loaded` for resuming as is
- `bot.reset()` is used for testing, to clear and return to `waiting` status

When bBot is first imported, it has a status of `waiting` before any adapter or
middleware is loaded, so it can customised before `load` or `start` is called.

`start` can be called without first calling `load`, to do both in one.

`bot.getStatus()` returns the current status, being `waiting`, `loading`, 
`loaded`, `starting`, `started` or `shutdown`.

All status method emit events allowing apps hook into bBot startup/shutdown:
`bot.events.on('loaded|started|shutdown|paused|waiting', () => /* callback */)`

## Usage

Simple

```js
import * as bot from 'bbot'

// ...add conversation logic (branches and middleware)

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
    // ...add conversation logic
    
    this.bot.start()
  }
}

new App().start()
```
___

<a href="/docs/path" class="btn btn-secondary">Keep learning ➮</a>
