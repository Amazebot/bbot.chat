[examples]: https://github.com/Amazebot/bbot/tree/master/packages/bbot-examples/basic-middleware

## Controlling Bot Thoughts

bBot uses a common middleware pattern to let developers inject state handling
logic into thought processes. After all middleware executes, if uninterrupted,
the thought process continues with the final state.

Similar to Express middleware, every middleware piece applies the same argument
signature of `state`, `next`, and `done`. Each piece can modify the state and
either continue (by calling next) or interrupt (by calling done). Pieces can
also define logic to execute after the stack completes, by calling `next` with
a replacement `done` function argument, that calls the original `done`.

Middleware functions can be asynchronous, returning promises to await before
the next piece is executed.

> 🧪 [Here's some examples of basic middleware][examples]

## Middleware Stacks

Middleware stacks are named according to the thought process they precede. Each
process either executes its middleware once before everything, or multiple times
to iterate over a collection (e.g. when processing branches).

Here's how middleware pieces from each stack apply to the thought process:
- `hear` - Execute once before proceeding, can interrupt all processing
- `listen` - Execute before every matching branch, can interrupt branch callback
- `understand` - As above, with NLU branches
- `respond` - Executes before every response, can interrupt to silence
- `act` - Execute before every catch-all branch (usually only one)
- `remember` - Execute once before storing state, interrupt to force forget

Register a middleware function against the stack name. Each piece will execute
in the order defined. e.g.

```js
bot.middleware.register('hear', (b, next, done) => {
  next() // I go first and continue the stack
})

bot.middleware.register('hear', (b, next, done) => {
  done() // I go second and interrupt the stack
})

bot.middleware.register('hear', (b, next, done) => {
  console.log('See you never') // I don't get a go
})
```

<a class="btn btn-primary" href="/guides/platform-adapters">
  ▶️ Continue to learn about platform adapters
</a>
