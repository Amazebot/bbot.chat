[examples]: https://github.com/Amazebot/bbot/tree/feature/rocketchat-message-wip/packages/bbot-examples
[yarn]: https://yarnpkg.com/en/docs/install

# bBot Usage Examples

The bBot repository has examples of different interactions, adapters and
deployment methods. [See the source here][examples].

You can use them as a base for your own projects or experiment by running them
from the main repo. Below we describe how to do that, as well as the usage and
patterns for many common use cases.

## Executable Examples

To execute examples directly from the bBot mono-repo, start by cloning it.

```
git clone https://github.com/amazebot/bbot
```

Move into the project root.

```
cd bbot
```

Use the `setup` script, which will install and link local dependencies, then
compile the bBot source. [Look here][yarn] if you don't have Yarn.

```
yarn setup
```

Now you can run any of the examples using the yarn `example` script followed
by the folder name of the example, e.g.

```
yarn example branches-basic
```

## Common Examples

