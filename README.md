# Gabby - A Dialogue Language for Games
<img align="left" style="padding-right:16px" src="images/GabbyBold64.png" alt="Gabby Icon">

<span style="font-size:18px">

[**Play the sample in your browser on itch.io here!**](https://potassium-k.itch.io/gabby)

Gabby is a language made for game dialogue. It makes it easy to build games and visual novels with dialogue at their core.

</span>

<br>

This is Gabby version **0.2**. This specification is still in development.

## [**View the specification here.**](gabbySpecification.md)

<br>

## What is Gabby?

Gabby is a dialogue language. It's essentially something in between a scripting language and a data format. It makes it easy to write rich, branching dialogue for games, without requiring any programming knowledge - so whether you're a writer or a programmer, you should feel at home working with Gabby.

Gabby works by writing dialogue scripts that get parsed by an engine. These scripts tell the game what text to show, and can trigger events, branch with player input, check game conditions, and much more.

The main goals of Gabby are to:
- Provide a simple and concise syntax for writing dialogue, reading like a manuscript instead of like code
- Be readable without any knowledge of the language
- Be easy and comfortable for writers to use, regardless of programming experience
- Allow the dialogue scripts themselves to drive the flow of dialogue, instead of having to drive it from code
- Make localization simple and flexible
- Be compatible with any desired game engine (given that an interpreter is written for it)

Working with Gabby is easy:

```
[Gabby.HelloWorld]

    (Gabby) Hello, world!
    -       ...
    -       Hello?

    (Kay)   Hey Gabby.

    (Gabby) Hi!
```

You can get started with Gabby by checking out the [sample game.](https://github.com/zdawson/gabby-dialogue-sample) Documentation for working with the language will be added here in the future.

<br>

## Detailed Example

```
gabby 0.2
language EN

// A simple dialogue between Gabby and Kay demonstrating some common features of the language.

[Gabby.SimpleDialogueExample]

    // You can use tags like sad or nervous to give your game more data about the line - useful to set portrait sprites, for example.

    (Gabby, sad)    Kay, I'm tired of having to write all our dialogue in a Word document, and give it to you to integrate into the game.
    -               Should I use a visual dialogue editor instead?

    // '+' can be used to append a line without clearing the dialogue.

    <nervous>
    +               Or should I just learn to write code?


    (Kay, shouting) No way!~
    <disappointed>
    +               There must be a better way...
    (Kay, thinking) Something that looks just like the scripts you send me, but just works...
    (Kay, excited)  Aha! I've got it!

    // Here we let the player make a choice, either to have Kay name it after Gabby or after herself.
    // These options (started with ':') are presented at the same time as a single choice to be made.

    // Option 1: Kay names the language after Gabby
    : I'll make you a new language, and call it Gabby!~

        // These lines are indented to include them in this option's dialogue block. They're shown when this option is selected.

        (Gabby, excited) Yay!~ That sounds great!
        -                ...
        +                ......
        <happy>
        -                Is it ready yet?

    // Option 2: Kay names the language after herself
    : I'll make you a new language, and call it Kay!~

        (Gabby, surprised) Oh! ...Yeah, that would be great!
        (Gabby, thinking)  ...
        +                  Umm...
        <happy>
        -                  Can you call it Gabby instead?

        (Kay, excited)     Sure! That sounds way better.

    // This is shown after running either of the above options

    // '*' can be used for narration

    * A long time passes...

    (Kay, excited)   It's ready! It's done! Let's make games!~

    // Using a tag to tell the game how to animate the text
    <style: shaking>
    (Gabby, excited) FINALLY!~

    (Kay, tired)     Yeah, finally...

    (Gabby, excited) Now we can make games!

    * Kay and Gabby get to work on their next project, excited to use their new language.
```

<br>

## License

This project is released under the MIT license. [You can read about this license here.](https://choosealicense.com/licenses/mit/)

<br>

## Links

- [Gabby](https://github.com/zdawson/gabby) - This repository. Learn how to write Gabby and check out the language spec.

- [Gabby Dialogue Engine](https://github.com/zdawson/gabby-dialogue-engine) - A Gabby Dialogue Engine implementation for Unity. Clone this directly if you don't want the sample as well.

- [Gabby Dialogue Sample](https://github.com/zdawson/gabby-dialogue-sample) - An interactive sample for the Gabby Dialogue Language made with Unity.

- [VSCode Plugin](https://marketplace.visualstudio.com/items?itemName=PotassiumK.gabby-lang) - Syntax highlighting for Visual Studio Code.

- [itch.io](https://potassium-k.itch.io/gabby) - The game sample, hosted on itch.io.

<p align="center">
  <img style="padding-top:64px" src="images/GabbyLogo.png" alt="Gabby Logo"/>
</p>
