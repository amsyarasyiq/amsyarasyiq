---
title: "Reclaiming Wintry's Distinctiveness"
publishDate: "20 July 2025"
description: "A reflection on Wintry's evolving goals, its influence on other client mods, and the steps being taken to restore its distinctiveness in the modding community."
---

Being 'distinctive' has always been a part of Wintry's goal. Wintry's current project highlights some differences from other existing projects:
- Simplistic and easy to understand
- First-party plugin experience
- Native interoperability

Although Wintry doesn't really focus on performance, it is much less modular than other client mods. Modularity costs performance, and optimizations can be done easily when everything is first-party.

You won't get most of these benefits with other client mods before, back when Wintry started. Even Revenge Rewrite had some really fancy plugin system that involved key-signing, which was a tad bit complicated (it's not wrong, just complex), which gave me another reason to build my own client mod.

Plus, Wintry is an experimental client mod with a vastly different philosophy. If you were thinking: "why can't everyone just work together and focus on one client mod?", the answer would be: we have different problems to solve. Wintry was never made to compete, neither were Bunny or Pyoncord. It's my fun little playground to work on a mod with a different philosophy.

## Loss of distinction
The major part of the 'distinctiveness' that I mentioned above has already been adopted by Revenge Next. Palm, the lead developer at Revenge, has shown constant commitment to their newest rewrite, where their goals clearly align with Wintry's. For instance, from an announcement in Revenge's Discord server:

> We've all experienced the issue of plugins becoming abandoned over time. To address this, Revenge Next will be driven by a community-curated repository where anyone can make contributions to be reviewed by the @Ecosystem Team. The aim is to reduce the problem of "dead" plugins and make plugin discovery much easier.

Although this is not exactly first-party plugins like Wintry (plugins being in the main repository), a centralized repository would still bring a similar experience for the users and developers, assuming they thoroughly review the quality of the code.

From the internal side, based on my inspection, a lot of Revenge Next's implementations also seem to be heavily inspired by Wintry at the fundamental level, particularly the Metro part. For example:

revenge-bundle-next/lib/discord/src/actions.ts#L83-L103:

```ts
export let Constants: DiscordModules.Constants = proxify(
    () => {
        const [module] = lookupModule(
            preferExports(
                byProps<DiscordModules.Constants>('ME'),

                // ID:   1236
                // Deps: 26, 1237, 1238, 1239, 1240, ...
                // Every module has only one dependency, which is the import tracker
                byDependencies(
                    loose([
                        undefined,
                        relative.withDependencies(OnlyImportTrackerDep, 1),
                        relative.withDependencies(OnlyImportTrackerDep, 2),
                        relative.withDependencies(OnlyImportTrackerDep, 3),
                        relative.withDependencies(OnlyImportTrackerDep, 4),
                    ]),
                ),
            ),
        )

        if (module) return (Constants = module)
    },
    { hint: {} },
)!

```

As for Wintry, a lookup typically looks like this:

```ts
export let constants = lookupByProps("Fonts", "Permissions").asLazy(m => (constants = m));
```

Of course, the API design is vastly different from Wintry. It is more verbose and explicit for each lookup definition. Wintry also lacks quite a number of things, including filtering by dependencies, which allows filtering modules without relying on the exports.

But the core idea is still there: return a lazy proxy object, and when the factory is initialized, immediately replace the proxied object with the actual object (which should be more performant than Proxy, addressing the problem with lazy modules). Even the `lookup` prefix traces back to Wintry.

<!--

Which actually achieves the same thing but is done quite differently, and much more simply, as lookupByProps and asLazy do similar things as Revenge's under the hood. It is not a complete equivalent, though. Wintry lacks quite a number of things, including filtering by dependencies, which allows filtering modules without relying on the exports.

> As for the byDependencies implementation, I had a similar concept before, but I just decided that it would not be worth it because the filter is pretty much useless when the lookup is cached. I also assumed that it would be very fragile, but Palm probably has investigated the fragility before implementing it. As you can see, the declaration became too verbose for a 'simplistic' client. It is not necessarily bad though, nor do I dislike it.

-->

That said, Revenge has shifted their goals and philosophy closer to Wintry now, and I have mixed feelings about this. One thing is clear though: Wintry is no longer as 'distinctive', so it felt pointless to keep working on it since we're basically solving the same problem the same way now.

## Reinstating the distinctiveness
It's time to make Wintry distinctive again, by redefining Wintry's goals. This time, it might make Wintry more technical for a 'simple' client, but it is better than being another mod that does the same thing.

The implementation details won't be revealed for now, but the great news is, some parts of it would not only be beneficial for Wintry but the entire DiscordRN modding community (including Revenge, of course)! Still, I am uncertain if I will have enough time to pull this off, but at least this will maintain Wintry's distinctiveness.

If I excite you, I should let you know in advance that distinctiveness doesn't necessarily mean it is better. In fact, most of the 'distinctiveness' won't be felt by the user, but rather in the way we do things to achieve the same goal. I'll re-emphasize that Wintry is a fun experimental project and is likely to stay that way in the long term.
