## The All Knowing `b`

State instances are the glue that binds all the pieces of a bot's interactions
together. They're created when the bot receives or dispatches a message,
to contain the message and other attributes describing the context of an
interaction as it moves through the bot's internal _thought process_.

Every branch callback and middleware piece receiving the state can read its
history or write its future. For brevity in bBot source and examples, we declare
the state argument as just `b`.

> 🎓 [Learn more about using state in middleware](/guides/middleware)

The context attributes of `b` include:

- `bot` - is the instance of the bot itself, e.g. `b.bot` 🤔
- `matched` - flags if message matched any branches
- `matchingBranches` - all branches that matched
- `matchingBranch` - the last matched branch
- `match` - last matched branch's match result
- `conditions` - last matched branch's conditions
- `message` - the incoming or outgoing message
- `user` - user details from the message
- `processed` - timestamps of all processed thoughts
- `envelope` - get/create an envelope for response
- `branches` - get/create branches for current dialogue

Some utilities can also be accessed from `b`:

- `respond` - dispatch envelope to the message source
- `clone` - create a deep copy of current state
- `finish` - flag that branch matching is complete
- `ignore` - flag to avoid any further processing
- `inspect` - print all attributes for debugging

Use the inspect method to see the full set of attributes. e.g.

```js
bot.branches.text(/debug state/, (b) => b.respond(b.inspect()))
```

<a class="btn btn-primary" href="/guides/thought-process">
  ▶️ Continue to learn about the bBot thought process
</a>
