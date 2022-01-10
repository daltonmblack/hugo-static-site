---
title: "Stardew Valley Mod (SeasonSwitcher)"
description: "My first video game mod"
date: "2022-01-09"
categories:
tags:
  - "stardew"
  - "mod"
  - "csharp"
  - "blog"
---

## Intro

This project has been waiting in the wings for too long. I finished my first video game mod, and it's for the game Stardew Valley.

I've been playing video games since I was a kid, and I know that young me would have slapped myself upside the head for how long it finally took me to apply my programming skills to modding a video game.

### What is a mod?

A "mod" is an piece of software written by a fan of a video game. The mod adds/alters/removes some piece of the game functionality. Mods enable a game's community to enhance and customize their experience. An example of a mod is the [Skyrim HD - 2K Textures](https://www.nexusmods.com/skyrim/mods/607) mod which updates many of the in-game textures of Skyrim to be a higher resolution.

### Why did I start with Stardew Valley?

Stardew Valley is a simpler game in both its gameplay, and its graphics. I also noticed that its creators have published high-quality [modding documentation](https://www.stardewvalleywiki.com/Modding:Modder_Guide/Get_Started).

## SeasonSwitcher

The mod I created for Stardew Valley is called [SeasonSwitcher](https://www.nexusmods.com/stardewvalley/mods/10457). It allows the player to instantly cycle to the next season (Spring -> Summer -> Fall -> Winter) at the press of a button. The.

<!--
![spring](/images/stardew-spring.png)
![summer](/images/stardew-spring.png)
![fall](/images/stardew-spring.png)
![winter](/images/stardew-spring.png)
-->

### Why so simple?

As anyone who builds personal projects knows, it's dangerously easy to bite off too much and ending with a partially completed project. On top of this universal fact, the first time trying something new takes disproportionately long. Given these facts, the mod idea I started with was creating a new item called Hermes Boots. These would have increased the player's speed dramatically, making it less tedious to run around the game.

However, as is already apparent, the mod I created was not Hermes Boots, but a mod about switching seasons. While working on the player speed increase, I was able to successfully update the player's speed, but only temporarily. Some of the game's mechanisms would always reset the player's speed back to normal after an indeterminate amount of time. I spent hours attempting to debug this, and was unsuccessful. This was when I decided to further simplify. I assume that as I become more familiar with Stardew's game mechanics, that I will be able to circle back and finish the Hermes Boots mod.

### How does SeasonSwitcher work?

Stardew mods use the "Stardew Modding API", aka SMAPI. SMAPI is an installable framework that provides hooks for mod code to access game internals, and then actually loads the mods into Stardew when the game is started. The primary entry point for SMAPI mods is `ModEntry.Entry` in a file named `ModEntry.cs`. This is where the mod will register its custom functionality into game hooks. Game hooks include things from user input (e.g., when the user presses the "n" key) to in-game events starting, like holidays.

`SeasonSwitcher` leverages the `ButtonPressed` hook and filters on the `n` key. The entirety of the mod's custom logic is in the section below. The only other logic needed besides filtering on the `n` key is to move the season one step forwards cyclically and then telling the game to update the displayed graphics.

The way I figured out what code I could manipulate was by using `ILSpy.exe` to disassemble the code of Stardew Valley itself. Disassembling is the opposite of compiling a program. For programs written in compiled languages (C, C#, golang, etc), the source code needs to be transformed into machine-readable code for it to be run. This machine-readable code is called assembly. Depending on how secure an application it, it is often possible to mostly reverse the process. I.e., taking the assembly code of an application and transforming it back into source code. Disassembly usually cannot 100% recover the original state of the source code, but it is still a valuable process. By disassembling the Stardew game code (a strategy suggested by the creator himself), it allowed me to dig through the game's code to find what I needed.

### SeasonSwitcher Code

```c#
public class ModEntry : Mod
{
    public override void Entry(IModHelper helper)
    {
        helper.Events.Input.ButtonPressed += this.OnButtonPressed;
    }

    private void OnButtonPressed(object sender, ButtonPressedEventArgs e)
    {
        // ignore if player hasn't loaded a save yet
        if (!Context.IsWorldReady)
        {
            return;
        }

        if (e.Button == SButton.N)
        {
            Monitor.Log("currentSeason: " + Game1.currentSeason, LogLevel.Debug);
            switch (Game1.currentSeason)
            {
                case "spring":
                    Game1.currentSeason = "summer";
                    break;
                case "summer":
                    Game1.currentSeason = "fall";
                    break;
                case "fall":
                    Game1.currentSeason = "winter";
                    break;
                case "winter":
                    Game1.currentSeason = "spring";
                    break;
            }

            Game1.setGraphicsForSeason();
        }
    }
}
```

### Try SeasonSwitcher for yourself

`SeasonSwitcher` is available for public download here: https://www.nexusmods.com/stardewvalley/mods/10457.

For instructions on how to install this mod, and any other mods, see the [Modding Player Guide](https://www.stardewvalleywiki.com/Modding:Player_Guide/Getting_Started). I personally have already tried roughly 20 Stardew Valley mods built by other developers. I personally recommend [NexusMods](https://www.nexusmods.com/stardewvalley/mods) combined with the [Vortex](https://www.nexusmods.com/about/vortex/) mod manager. Setting up mods for the first time will likely be tedious, but it's worth pushing through any frustration. It's uniquely satisfying to apply custom enhancements to a game you already love. 

## Conclusion

In only a few days, I created & finished my first video game mod and published it for others to use. At the time of this blog post, over 70 people have downloaded it. I am excited to not only make bigger and better Stardew Valley mods, but to also start branching out into more complex games. A couple of the games I'm excited to start modding are [Factorio](https://www.youtube.com/watch?v=J8SBp4SyvLc) and [Civ 6](https://www.youtube.com/watch?v=kk_wL-lw0xo).

I would definitely recommend giving modding a shot if you enjoy video games and have even just a little programming experience. **Don't just play your favorite games, leave your mark on them!**
