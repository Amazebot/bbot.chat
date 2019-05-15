[rc]: https://github.com/Amazebot/rocket-control
[dialogue]: https://github.com/Amazebot/bbot/blob/master/packages/bbot/src/components/dialogue.ts
[dialogue-src]: https://github.com/Amazebot/bbot/blob/master/packages/bbot/src/components/dialogue.ts#L12-L74
[lerna]: https://lernajs.io/
[oc]: https://opencollective.com/

# 🥇 v1 Pre-release

## ✨ Release Highlights

*So many features!* zomg. I'll be writing the guides for these for days, but
the main one is DIALOGUE! As _@jonnyshare_ pointed out on the Rocket.Chat #bbot
channel, the project makes a big noise about "context" but in beta that was only
possible through a bunch of hacks. Now the [dialogue][dialogue] module makes it
super easy to create branches that follow on to create multi-stage conversation
pathways that can be constructed dynamically. In lieu of a detailed guide doc,
please review the examples in [source][dialogue-src].

---

*One repo to rule them all*. Putting adapters, boilerplates and examples for
bBot usage each in their own repo was damaging the momentum for the fledgling
community, who were reporting issues and finding their way to different sources.
Along comes [Lerna][lerna] to show me the light, now everything can live snug in
a single repo, yet maintain individual dependencies and build pipelines. My hope
is that now I can gather all the GitHub stargazers together and finally reach
critical mass for launching an [Open Collective][oc].

---

*Bot dot what?* The bot's root namespace got hit with some spring cleaning.
It was exposing pretty much everything, but now they're stowed behind some 16
odd tidy semantic attributes. Naming conventions and module exports for "utils",
"components" and their "controllers" is applied universally too. I'll explain
more later.

---

*Adapters have moved (incomplete)*. Keeping adapters for external systems like
Rocket.Chat or Mongo within the main bot module was a mistake. It required some
hacky loading logic and bloated the dependency footprint for the core bot when
used without them. Now everything is in the same repo but published as
individual modules, with a consistent naming convention.

Currently, there is no support in the pre-release version for any adapters other
than Rocket.Chat. Before a full release, the Slack, Mongo, Rasa and Watson
adapters will be updated.

---

*⚠️ Deprecated `bot.global`!* The "global" attribute was meant to namespace the
creation of branches that weren't within a conversational context. This renamed
the exported `branches` module and also naming anything `global` is just silly.
Now you can uses `branches` as an attribute of the state object, for in-context
branching, or `bot.branches` to create branches out of context.

## Personal update

So, this has been a long time coming, I'm equal parts excited and exhausted 😅

Development stopped on the beta `0.x.x` versions in September 2018, when I
decided it was time to gather learnings and embark on the final set of features
I had on the road map for v1. That also included a major re-architecture and
significant [side-project][rc] that was blocking outcomes for this framework.

I've also made some tough decisions about how to proceed with the venture that
was parent to bBot (Amazebot). As I approached having a second child and was
also struggling with the alignment of the vision of the open source project with
the types of commercial engagements I was finding. I ended up winding down the
business side and finding a new full time role, but after everything settled I
believe that has only sharpened my resolve to follow through with the project as
a pure open source utility, to share and invest in a community I enjoy so much.

So, six months later, I'm proud to announce the first out-of-beta release
candidate. There's a handful of known issues, I aim to resolve in the coming
weeks, but I've kept a few keen user's waiting for far too long and I'm sure
there'll be unknowns too, which I need their (and your) help to find and
discuss.

I started on the concept for bBot when I was on parental leave with my first
child in early 2017 and officially started coding when I took it on full time at
the end of that year. We're about 200 commits and 9000 lines of code later,
she's 2 now and her sister just arrived! I'm happy with the accomplishments and
excited to share them with you, but still have a lot more in mind.

This will be a period of increasing stability however, avoiding any further
refactoring or feature work and focusing on bug fixes, documentation and
producing resources to grow the community.
