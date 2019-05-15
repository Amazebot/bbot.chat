[examples]: https://github.com/Amazebot/bbot/tree/master/packages/bbot-examples
[packages]: https://github.com/Amazebot/bbot/tree/master/packages

## Importing the Package

bBot is as a Node.js package. To create a bot, create a Node app and install
`bbot`.

```shell
➜ mkdir mybot
➜ cd mybot
➜ npm init -y
➜ npm install --save bbot
➜ touch index.js
```

Then import the package and start the bot in **index.js**

```js
const bot = require('bbot')
bot.start()
```

Now you can start testing interactions by chatting in your terminal.

```shell
➜ node index.js
```

> 🧪 [Here's some example projects][examples]

## Package Modules

The main bBot package exposes everything you need to build conversational apps.
These are the modules for most common use cases:

- `bot.branches` - match incoming messages to a callback
- `bot.request` - make http/s requests for external data
- `bot.server` - serve http/s requests with Koa
- `bot.memory` - get/set/persist operational and user data
- `bot.middleware` - add logic for each stage of processing

Other modules define the bot's behaviour for more unique use cases:

- `bot.thoughts` - process incoming and outgoing messages
- `bot.dialogues` - isolate context of conversational branches
- `bot.logger` - write logs to console and files
- `bot.adapters` - connect messaging with external platforms
- `bot.events` - emit events and attach event callbacks
- `bot.config` - configure bot from CLI or environment

Bonus points for Typescript devs, bBot is exported with definitions. Yay types!

## Connecting To Platforms

Adapters are used to log your bot into a message platform and other external
services like NLP and storage providers. Before starting the bot it should be
configured with an adapter for your preferred platform.

Each adapter is it's own package which must first be added as a dependency. e.g.

```shell
npm install --save bbot-message-rocketchat
```

> 🎓 [Learn more about platform adapters](/guides/platform-adapters)

Create an `.env` file to configure the bot to use the adapter and any settings
required by the adapter. This can also be done via command line args or
`package.json` settings.

```sh
BOT_MESSAGE_ADAPTER="bbot-message-rocketchat"
RC_URL="https://my.rocket.chat"
RC_USERNAME="bbot"
RC_PASSWORD="pass"
```

Adapters aren't needed for testing, you can chat with the bot in your terminal.

> 🎓 [Learn more about configuring bots](/guides/configuration)

## Executing Bots

bBot's basic execution methods are:

- `bot.load()` initialises middleware and adapter classes
- `bot.start()` connects adapters to external services and waits for input
- `bot.shutdown()` disconnects from services, keeping module instances
- `bot.pause()` does shutdown then returns to `loaded` for resuming as is
- `bot.reset()` is used for testing, to clear and return to `waiting` status
- `bot.getStatus()` returns the bot's current status

They are all asynchronous, returning promises you can use to add behaviour when
the status resolves. When bBot is first imported, it has a status of `waiting`
before any adapters or middleware are loaded, so it can be customised before
execution.

`start` can be called without first calling `load`, to do both in one. All
status methods emit events allowing apps hook into bBot execution. Status can be
`loaded`, `started`, `shutdown`, `paused` or `waiting`.

```js
bot.events.on('started', () => {
  bot.logger.info('Started with users in memory:', bot.memory.users)
})`
```

<a class="btn btn-primary" href="/guides/matching-and-branching">
  ▶️ Learn to define interactions with matching and branching
</a>
