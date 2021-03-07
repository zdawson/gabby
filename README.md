Gabby Dialogue Language Specification
=====================================
<img align="left" style="padding-right:16px" src="images/GabbyLogo.png" alt="Gabby Logo">

<span style="font-size:18px">
Gabby is a dialogue scripting language intended for use in games and visual novels.  
</span>

This is Gabby version **0.2**. This specification is still in development.

<br>

## [View the specification here.](gabbySpecification.md)

### Also see:
- [gabby-dialogue-engine](https://github.com/zdawson/gabby-dialogue-engine): A Unity C# implementation of the language.
- [gabby-dialogue](https://github.com/zdawson/gabby-dialogue): A Unity project demonstrating usage of the dialogue engine.
- [gabby-dialogue-vscode](https://github.com/zdawson/gabby-dialogue-vscode): Syntax highlighting for VSCode.

Overview & Goals
----------------

Gabby was created to make it easy to write rich, branching dialogue for games. Writing dialogue with Gabby should feel like you're writing a story, allowing you to focus on the content of your dialogue. The intent is to provide a natural way of writing so that any markup is minimal and unobstructive, and the barrier to entry for learning the language is as small as possible.

The key goals of Gabby are:
- To have a simple and concise syntax with a minimal footprint or boilerplate
- To look more like a script or story than code or a data format
- To be quickly understood by writers who don't program, and comfortable to use
- To allow the dialogue scripts to drive the flow of dialogue, including player choices, triggering game events, and checking conditions
- To make it easy to localize and offer more flexibility in changing dialogue structure for other languages

Gabby is inspired by [Ren'Py](https://www.renpy.org/), [Yarn Spinner](https://yarnspinner.dev/), and the many visual node-based dialogue editors available online.  
Gabby takes a different approach to how it combines game scripting and dialogue scripting, offloading most of the logic to the game implementation. This is a tradeoff between power and readability, treating the dialogue files more like data to be interpreted than scripts to be executed. This allows Gabby to stay concise and highly readable.

Example
--------
```
gabby 0.2
language EN

// A simple dialogue between Gabby and Kay demonstrating some common features of the language.
// Note that the usage of specific tags for portraits and text animation relies on game-side implementation and is not part of the language spec by default.
// (The spec is designed with these use cases in mind, though)
[Gabby.Simple Dialogue Example]

    (Gabby, sad)    Kay, I'm tired of writing dialogue in Word and giving it to you to put into the game.
    -               Should I learn to code? Should I write everything in this node-based editor?
    -               I just want to write dialogue and see it in game...

    (Kay, shouting) No way!
    <thinking> // Using a tag to change portraits, avoiding re-stating the character name over and over
    +               There must be a better way... // This line appears in the same dialogue box, but after an additional input
    -               Something that looks just like the scripts you send me, but just works...
    <excited>
    -               I've got it!

    // Option 1: Kay names the language after their friend, Gabby
    : I'll make you a new language, and call it Gabby!~

        // These lines are indented to include them in this option's dialogue block. They're shown when this option is selected.

        (Gabby, excited) That sounds great!
        <style:slow> // Show each character of the next line with a long delay
        -                ...
        -                Is it ready yet?

    // Option 2: Kay names the language after themself
    : I'll make you a new language, and call it Kay!~

        (Gabby, surprised) Oh! ...Yeah, that would be great!
        <style:slow, thinking> // Multiple tags can be included, separated by commas
        -                  ...
        -                  Umm...
        <smiling>
        -                  Can you call it Gabby instead?

        (Kay, excited)     Yeah, I like that way more!~

    // This is shown after either option
    * A long time passes...
    (Kay, excited) It's ready! It's done! Let's make games!~
    <shake>
    (Gabby, excited) FINALLY!~

    end
```
