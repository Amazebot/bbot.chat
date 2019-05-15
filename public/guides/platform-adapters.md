[amazebot-gh]: https://github.com/Amazebot/
[adapters]: https://github.com/Amazebot/bbot/tree/master/src/lib/adapter-classes

## Make a Connection

Adapters allow your bot to integrate with external platforms interchangeably, so
you can apply consistent application logic to a variety of audiences or tech
stacks. This also makes migration much easier, preventing vendor lock-in.

bBot provides an agnostic schema for data models, like messages, NLU results.
Adapters handle connection/login with the platform and parse the incoming and
outgoing data. Current adapter types include:

- **Message** adapter connects the chat platform, parsing messages and events.
- **NLU** adapter processes messages for natural language understanding.
- **Storage** adapter to save, query and persist operational data and states.

We have other adapter types on the roadmap, such as **Webhook** to manage custom
integrations and **Analytics** to report data into external dashboards.

## Supported Platforms

Adapters are published as NPM packages, to be added as a dependency of your bot.
The bBot mono-repo includes some [adapters for common platforms][adapters],
providing a pattern for developers to extend and create their own.

<a href="https://github.com/Amazebot/bbot/tree/master/packages/bbot-message-rocketchat">
  <img class='m-2' width="100px" height="auto" src="/img/adapters/rocketchat.png" />
</a>

<a href="https://github.com/Amazebot/bbot/tree/master/packages/bbot-message-slack">
  <img class='m-2' width="100px" height="auto" src="/img/adapters/slack.png" />
</a>

<a href="https://github.com/Amazebot/bbot/tree/master/packages/bbot-nlu-watson">
  <img class='m-2' width="100px" height="auto" src="/img/adapters/watson.png" />
</a>

<a href="https://github.com/Amazebot/bbot/tree/master/packages/bbot-nlu-rasa">
  <img class='m-2' width="100px" height="auto" src="/img/adapters/rasa.png" />
</a>

<a href="https://github.com/Amazebot/bbot/tree/master/packages/bbot-storage-mongo">
  <img class='m-2' width="100px" height="auto" src="/img/adapters/mongo.png" />
</a>

## Using Adapters

Adapters need to be installed as npm packages, then enabled by configuring the
bot with the package name, e.g. `BOT_MESSAGE_ADAPTER=` or using command line
options as `--message-adapter=`.

> 🎓 [Learn more about configuring bots](/guides/configuration)

## Building Adapters

bBot provides a generic parent class for each adapter type. Developers can
require bBot and extend those classes, or just replicate their structure.

All adapters need the following:
  - Constructor accepts the bot instance, setting the `this.bot` attribute or
    passing it to `super` if extending a parent class.
  - `.start()` method returns a promise, resolving when the adapter is connected
    to the service, carrying out any authentication and use of config settings.
  - `.shutdown()` disconnects and cleans up any internal state management.
  - `.name` attribute for logs, following the convention:
    
    `<SERVICE>-<ADAPTER_TYPE>-adapter` e.g. `rocketchat-message-adapter`
  - The module should export a `.use()` method that accepts the bot, creates and
    returns new instance of the adapter class.

When bBot loads, it will require the package by name as it's set in the bot's
config, calling `.use(bot)` with it's self (bBot main). When `.start()` is
called on the bot, it calls `.start()` on all loaded adapters. The bot's own
start method, will resolve when all the adapters are started.

Each adapter type base class has additional abstract methods that need to be
extended. It's best to [look at the source for those details][adapters].

### Extending bBot Classes

#### Basic

For basic adapters, you can create classes as per the above instructions. e.g:

```js
class BasicMessageAdapter {
  constructor (bot) {
    this.bot = bot
    this.name = 'basic-message-adapter'
  }
  async start () {
    this.bot.logger.info('[basic] connecting to my message platform')
    // run connection logic
    // setup message subscriptions, adding the `.incoming` method as callback
  }
  async shutdown () {
    this.bot.logger.info('[basic] connecting to my message platform')
    // run disconnect logic
  }
  async dispatch (envelope) {
    // parse `envelope.strings` and `envelope.payload` and emit to platform
    // - use `envelope.room` or `envelope.user` to address outgoing message
    // - use `envelope.method` to handle methods other than default 'send'
  }
  incoming (platformMessage) {
    // called by message subscription events in the platform
    const { user, text, id } = platformMessage
    const message = new this.bot.TextMessage(user, text, id)
    return this.bot.receive(message)
  }
}

module.exports = {
  use: (bot) => new BasicMessageAdapter(bot)
}
```

If this were published to npm as 'basic-message-adapter' and installed in your
project, it could be loaded by simply passing the `-m basic-message-adapter`
flag in command line args, or setting
`BBOT_MESSAGE_ADAPTER="basic-message-adapter"` in environment variables.

You could also require it directly in your bot's index.js like so:

```js
const bot = require('bbot')
bot.adapters.message = require('basic-message-adapter').use(bot)
```

#### Advanced

Adapters that need more advanced integration with the bBot core classes,
should require bBot as a peer dependency instead.

Extended classes do not need to add a constructor. Initialising logic should be
put within the start method.

For typescript developers, the exported definitions will provide intellisense
for bBot's attributes.

```js
import * as bBot from 'bbot'

class AdvancedMessageAdapter extends bBot.MessageAdapter {
  async start () {
    // as above
    // here we could access `this.bot` and modify prototypes or add middleware
  }
  async shutdown () {
    // as above
  }
  async dispatch (envelope: bBot.Envelope) {
    // as above, with intellisense for envelope attributes 
  }
  incoming (platformMessage) {
    // as above
  }
}

export const use = (bot: typeof bBot) => new AdvancedMessageAdapter(bot)
```

---

<a href="/guides/nlu" class="btn btn-secondary">Learn how to use NLU ➮</a>
