[thought]: ./thought
[bbot]: '//bBot.chat'
[issues]: 'https://github.com/amazebot/bbot.chat/issues'

---
> This document is out of date, it's been removed from the menu until it can be
> updated with accurate references.
---

## The Real MVP
This project was spawned from a collection of extensions to GitHub's Hubot
framework that outgrew their repo and begged for refactoring as a coherent
product. We are immensely thankful for the work of our predecessors.

bBot's first release reproduced most of Hubot's functionality, with its platform
adaptor model extended to storage and language processing. bBot packs in some
unique features and concepts, but the architecture will be familiar to Hubot
users.

Please [report an issue][issues] if you find any details incorrect or if we've
missed something important.

## Hearing and Listening

`listen`, `hear` and `respond` methods have counterparts within bBot but they
work slightly differently and we've attempted to find more semantic naming.

The most important difference is that bBot is [built for branching](/guides/matching-and-branching).
So message matching happens on the type and content of the message, just as with
Hubot, but also on the _scope_. We refer to all "listeners" as branches.

Hubot's listeners had no scope, they were all what we refer to as _global_. In
bBot, we can create global branches from the `global` attribute, or on a
specific `path` instance. The methods are the same in either case.

### Hear ➮ Text

In **Hubot** `hear` adds a text pattern listener.

In **bBot** `text` does the same, called on a path like `bot.global.text`.

`hear` refers to the [process][thought] which determines if incoming messages
will be processed against branches.

### Respond ➮ ListenDirect

In **Hubot** `respond` adds a text pattern listener that will only match if
prefixed with the bot's name.

In **bBot** `direct` does the same, called on a path like `bot.global.direct`.

`respond` is used to actually initiate the outgoing response and the
[process][thought] to handle it.

### Listen ➮ ListenCustom
  
In **Hubot** `listen` is a sort of abstract for both `hear` and `respond`.

In **bBot** `custom` can be used to create a branch with a custom matcher.

`listen` is the [process][thought] which provides messages to each branch.

#### Usage

Hubot
```js
  robot.hear(/.*/, () => console.log('I listen!'))
```

bBot
```js
  bot.global.text(/.*/, () => console.log('I branch!'))
```

### Response and Context == State

The semantics of Hubot's `Response` were sometimes misleading, as the "response"
did not always comprise an actual response and may never be used to make one. It
was more like a "context", though there was also a `context` in use by
middleware (which response was a property of) but the full context was not
available to higher level functions, like listeners. 🤯

In bBot, we've merged `context` and `response` into an all seeing state class
called `B`. The same state flows through middleware and branches, allowing
callbacks to be informed by the full processing history.

A state instance, `b` takes the place of `res` as the primary argument passed to
branch callbacks and also provides methods to respond to a received message.

#### Hubot
```js
robot.middleware.receive((context, next, done) => {
  console.log('Received: ' + context.response.message.toString())
  next()
})
robot.hear(/.*/, (response) => {
  console.log('Matched on: ' + JSON.stringify(response.match))
}
```

#### bBot
```js
bot.middleware.hear((b, next, done) => {
  console.log(`Received: ${b.message.toString()}`)
  next()
})
bot.global.text(/.*/, (b) => {
  console.log(`Matched on: ${JSON.stringify(b.match)}`)
}
```

## Responding to Messages

In Hubot, the `robot` and `response` both attempted to define a set of common
methods for handling outgoings, like `reply` and `send`, however the semantics
were a bit off.

Sometimes `reply` was used unprompted by an incoming message, so it wasn't
really replying. Sometimes `send` would be used to "reply". Sometimes a platform
adapter did not support all the defined methods or needed extending with custom
methods of it's own, used inconsistently with Hubot's.

bBot takes a different approach, using an envelope's `method` attribute to
define how the adapter should handle it, e.g as a send (default), an emoji
reaction, a topic change, whatever. This provides message adapters more
flexibility to handle as many or few methods as needed.

There are only two ways to initiate outgoings:
- `respond` is called on a state with an incoming message to respond to.
- `dispatch` can be called from the bot when there's no originating state.

Message adapters only implement `dispatch` to encompass all outgoing content as
an envelope addressed back to the source or to start a new interaction.

### Messages and Envelopes

bBot and Hubot have the same concept of messages and envelopes. Messages are an
incoming object from a messaging platform, parsed by the adapter. Envelopes
address outgoing content, can be unprompted or created to respond to a received
message. The message adapter parses those back into the messaging platform.

In Hubot, envelopes were often plain objects, but bBot adds some helpers to
set attributes. The `room` attribute of an envelope in bBot explicitly contains
`name` or `id` attributes, so adapters can perform better as they don't need
multiple lookups to determine which was given.

### Forming a Response

Hubot's outgoing methods accepted arguments for composing **and** dispatching
the envelope, sometimes in different positions. Some methods accepted strings,
some took the whole envelope object. This often led to tricky argument slicing
and conditionals.

bBot arguments are strictly typed and it aims to give methods clear purpose,
consistent patterns and separation of concerns. Composing and dispatching can
be distinct actions to simplify advanced callbacks, so the *how* is not confused
with the *what*.

Envelope helpers are as follows (and can be chained together):
- `toRoomId` sets the room ID
- `toRoomName` sets the room name
- `write` adds string content
- `attach` adds payload content
- `compose` add strings and/or payloads
- `via` sets the method for the adapter to implement

States can create and dispatch an envelope responding to an incoming message,
inheriting the properties to address it back to the source. Using:
- `respond` to compose and dispatch in one
- `reply` to compose and dispatch in one, with the username prefixed
- `respondVia` to override the default dispatch method, compose and dispatch
- `envelope` to get the envelope first, if it needs to be re-addressed to 
  a different room or user

### Unprompted Outgoing

#### Hubot
```js
const room = { name: 'general' }
robot.messageRoom(room.name, 'hello #' + room.name)
```

#### bBot
```js
const room = { name: 'general' }
bot.dispatch(new bot.Envelope({ room }).compose('hello #' + room.name))
```

### Responding to Incoming

#### Hubot
```js
robot.hear(/say hello to (.*)/i, (res) => {
  res.send('Hello ' + res.match[1])
})
robot.hear(/can anyone hear me/i, (res) => {
robot.adapter.react(res.envelope, ':raising-hand:')
})
```

#### bBot
```js
bot.global.text(/say hello to (.*)/i, (b) => {
  b.respond('Hello ' + b.match[1]
})
bot.global.text(/can anyone hear me/i, (b) => {
  b.respondVia('react', ':raising-hand:')
})
```

### Adapter Class

#### Hubot
```js
const { Adapter } = require('hubot')
class Campfire extends Adapter {
  send (envelope/* , ...strings */) {
    const strings = [].slice.call(arguments, 1)
    const string = strings.shift()
    // 'speak' is a Campfire method, recursive callback sends multiple strings
    this.platform.room(envelope.room).speak(string, (error, data) => {
      this.send.apply(this, [envelope].concat(strings))
    })
  }
  topic (envelope, topic) {
    // < handle custom outgoing method >
  }
}
exports.use = robot => new Campfire(robot)
```

#### bBot
```js
const { MessageAdapter } = require('bBot')
class Campfire extends MessageAdapter {
  async dispatch (envelope) {
    switch (envelope.method) {
      case 'topic': 
        // < handle custom outgoing method >
        break;
      default: // `speak` or `send` would be handled as default method
        for (let string of b.envelope.strings) {
          await this.platform.room(b.envelope.room).speak(string)
        }
        break;
    }
  }
}
exports.use = bot => new Campfire(bot)
```

Alternatively, adapters could define platform methods, to relay from dispatch:

```js
const { MessageAdapter } = require('bBot')
class Campfire extends MessageAdapter {
  dispatch (envelope) {
    return this[envelope.method](envelope)
  }
  send (envelope) {
    return this.speak(envelope) // proxy to re-direct default method
  }
  async speak (envelope) {
    for (let string of b.envelope.strings) {
      await this.platform.room(b.envelope.room).speak(string)
    }
  }
  async topic (envelope) {
    // < handle custom outgoing method >
  }
}
```
