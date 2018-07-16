The bBot thought process describes how this clever and purposeful bot
elegantly handles discourse.

It involves a series of processing steps, each invoking middleware for adding
your own processes. Middleware pieces receive all data in the current state.

It all starts when the message adapter gives an incoming message to the bot via
a `.receive` call.

## Hear

bBbot hears all messages to determine if the context requires attention.
It gather information for better listening, or ignores if it shouldn't listen.

Add a middleware piece via `.hearMiddleware` to interrupt the process or modify
the state for further processing.

## Listen

bBot takes in the message and if recognised, runs the scenario, furthering a
conversation or a simple exchange. It may not be immediately understood.

Listeners provide a matching function to evaluate the message and fire callbacks
on match. They are added with the following methods:

- `.listenText` adds regular expression matching on message text
- `.listenDirect` adds regular expressions prepended with the bot's name
- `.listenCustom` adds a custom matching method, e.g. match on an attribute

Add a middleware piece via `.listenMiddleware` to fire on every matching
listener, to interrupt or modify the state.

If no listeners fire yet, we include listeners from the next (Understand) stage.

## Understand

bBot can use natural language services to listen for the intent rather than the
exact command. The nature of the message is collected from external providers.

A special type of `NaturalLanguageListener` is used for this stage, that
evaluates the intent, entities and/or sentiment of the message, by sending the
message to the NLU adapter.

- `.understandText` adds natural language matching criteria for NLU data
<!-- - `.understandDirect` adds natural language that must be addressed to the bot -->
- `.understandCustom` adds custom natural language matching on NLU data

Add a middleware piece via `.understandMiddleware` to execute on every matching
language listener, to interrupt or modify the state.

## Act

bBot takes action when all else fails, locally or through external integrations.

This is an outcome of `listen` and `understand` stages passing without any
listener matching, bBot creates a special `CatchAllMessage` message to receive
with a new set of listeners that can take action when nothing else did.

- `.listenCatchAll` adds a callback for any unmatched message

Add a middleware piece via `.actMiddleware` to execute on every matching
catch-all listener, to interrupt or modify the state.

## Respond

bBot replies to the people it's engaged with appropriately. Canned responses
are mixed with context and may include rich UI elements.

Add a middleware piece via `.respondMiddleware` to execute on any sends, if
matched callbacks or bits prompted messages to be sent.

## Remember

bBot remembers everything, the person, context and content. Important details
are kept for quick access. Everything else is stored for safekeeping.

That all might take a few milliseconds, then we're back at the beginning.
