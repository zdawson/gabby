Gabby Dialogue v0.2 <!-- omit in toc -->
===================
<img align="left" style="padding-right:16px" src="images/GabbyLogo.png" alt="Gabby Logo">

<span style="font-size:18px">
Gabby is a dialogue scripting language intended for use in games and visual novels.  
</span>

This specification is intended to cover every supported feature of the language in detail. Dialogue engine implementations should support everything as defined here to be considered a standard implementation. 

This is **version 0.2.0** of the spec, for **Gabby v0.2**. 

## Table of Contents <!-- omit in toc -->
- [Gabby Metadata](#gabby-metadata)
- [Comments](#comments)
- [Dialogue](#dialogue)
- [Lines](#lines)
- [Dialogue Lines](#dialogue-lines)
- [Appending Dialogue](#appending-dialogue)
- [Narrated Dialogue](#narrated-dialogue)
- [Dialogue Blocks](#dialogue-blocks)
- [Options](#options)
- [Ending Dialogue](#ending-dialogue)
- [Actions](#actions)
- [Jumps](#jumps)
- [Conditionals](#conditionals)
- [Tags](#tags)
- [Inline Dialogue Tags](#inline-dialogue-tags)

----------------------
Gabby Metadata
-----------------

The start of a Gabby file may optionally contain the following metadata:  
- version information, specified by the keyword `gabby` followed by the version number.
- language, specified by the keyword `language` followed by any language identifier.  

Valid metadata:
- Must be at the start of a file, ignoring comments and whitespace.
- Must begin with a valid keyword, `gabby` or `language`.
- Must only be declared once per file.

Usage:
```
gabby 0.2
language EN
```

----------------------
Comments
--------

Comments are written with two forward slashes `//`. Any characters can follow this and will be ignored by the dialogue engine.  
There are no multiline comments. Use multiple single-line comments instead.

Comments can be written at any part of a line, and everything up until the comment will be parsed by the dialogue engine.

Usage:
```
// This is a comment.
```

----------------------
Dialogue
--------

A Gabby file defines one or more dialogues.  
Dialogues are conversations between one or more characters. Every dialogue has a unique name, and is associated with a single character that "owns" the dialogue. This character can be an NPC being spoken to, an object being interacted with, a narrator, the player, or anything else the game wants.

Dialogues are written starting with a dialogue definition. Dialogue definitions are designated by square brackets `[]`, containing the character name and dialogue name separated by a period `.`. The dialogue engine can then use the character name and dialogue name to locate dialogues for use by the game.

Valid dialogue definitions:
- Must be surrounded by a single set of square brackets `[]`.
- Must declare a character name followed by a dialogue name, separated by a period `.`.
- May have whitespace around or within the character and dialogue names. Whitespace around the names will be trimmed, but whitespace inside the names will be preserved.
    - ie, `[Character Name.Dialogue Name]` and `[ Character Name . Dialogue Name ]` are equivalent.
- Must not contain newlines.
- Must not contain square brackets `[]`.
- Must not contain period `.`, aside from the separator.

Dialogues continue until:
- A new dialogue definition is reached.
- The end of the file is reached.

Usage:
```
[Character Name.Dialogue Name]
```

Example:
```
gabby 0.2
language EN

[Gabby.ExampleDialogue]
    - Hello!
```

----------------------
Lines
--------------

Dialogues contain any number of lines. This is where you can add content to your conversations.  
Lines can be either player-facing or internal to the dialogue engine. Most lines will be dialogue lines
spoken by an NPC to the player, but could also be options, actions, jumps, etc. as defined elsewhere in this spec.

Lines always begin with a unique designator, used by the dialogue engine to define how to handle a line when executing dialogue.

Valid lines:
- Must begin with a valid line designator.
- Must be ended with a newline character or comment.
- May be indented before the line designator, which will be considered when determining contents of dialogue blocks.
- May have any amount of whitespace after the line designator, which will be trimmed.

----------------------
Dialogue Lines
--------------

Dialogue lines use one of the following designators:
- `(Character Name)` The name of the speaking character, written explicitly and surrounded by parentheses `()`.
- `-` A single dash, implicitly referring to the last character to speak, or to the character who owns the dialogue if no character has been explicitly named.

Following this, the dialogue text is written. The text can also be left blank, changing the current speaker for the following lines. An empty dialogue box will not present a prompt to the player, it will be skipped internally by the dialogue engine.

Valid dialogue lines:
- Must be a valid line.
- May contain whitespace before or after the character name, which will be trimmed by the dialogue engine.
- May contain whitespace before or after the dialogue text, which will be trimmed by the dialogue engine.
- May have an empty dialogue body, in which case the speaking character changes without prompting the player

Usage:
```
(Character) A line of dialogue.
- A line of dialogue.
```

Example:
```
gabby 0.2
language EN

[Gabby.DialogueLineExample]
    - Hello, I'm Gabby! // The character speaking here is Gabby
    (Kay) I'm Kay!
    - It's good to meet you! // The character speaking here is Kay
```

Implementation Notes  
The dialogue engine:
- Must display the dialogue until the game signals the engine to advance to the next line.
- Must provide the game with the correct character for lines with implicitly referenced characters.
- Must properly update the speaking character even with blank lines
- Must not trigger an empty dialogue box for lines containing only whitespace

----------------------
Appending Dialogue
------------------

Additional text can be displayed without clearing the current dialogue line by appending to it.  
Appended lines are designated by `+`.

Usage:
```
+ This line is appended to the previous line after the player advances.
```

Example:
```
gabby 0.2
language EN

[Gabby.AppendingLinesExample]
    - Um...
    + Can I help you? // This text appears as part of the same dialogue line after the player advances.
```

Implementation Notes  
The dialogue engine:
- Must treat this as a regular line when it appears as the first line of dialogue.
- Must treat this as a separate event than dialogue lines, in order to properly allow support for animating text or other game specific behaviour.

----------------------
Narrated Dialogue
----------------------------------

Narrated dialogue can be used in cases where you don't want any character to speak.  
Narrated lines are designated by `*`. 

Usage: 
```
* This line is not spoken by a character.
```

Example:
```
gabby 0.2
language EN

[Gabby.NarrationExample]
    * Gabby looks around, surprised.
    - Wow! I didn't realize writing narrated text was so easy.
```

----------------------
Dialogue Blocks
---------------

Dialogue blocks are logical "chunks" of dialogue, where lines within the block flow from beginning to end.  
In a simple dialogue, there is a single dialogue block. More complex dialogues with options introduce additional dialogue blocks.

Dialogue blocks are defined by indentation. Thus, it's important that all lines within a block are indented the same way, or you will get unexpected results.  
Specifically, any time indentation falls behind the parent block, the inner block ends, so bad indentation may cause you to inadvertently close a block.

You do not create dialogue blocks yourself, only through special lines like options.

When all lines within a dialogue block are executed, the dialogue engine returns to the parent block, or ends the current dialogue if there are no other lines.

----------------------
Options
-------

Options are used to prompt the player to make a choice, creating a branch the dialogue tree.  
They are designated by `:`, with each individual option being given its own line.

After each designated option, a new dialogue block is started. This block must be indented beyond the option line. The block ends
after the indentation returns to the level that the option was defined at.

Valid options:
- Must be indented uniformly so they each fall in the same dialogue block to be considered part of the same selection.
- May contain an arbitrary number of selectable options.
- May contain an arbitrary number of lines in each option's dialogue block.
- May contain nested options.

Usage:
```
: Option A
    - A line of dialogue presented when Option A is selected.
: Option B
    - An alternate line of dialogue presented when Option B is selected.
```

Example:
```
gabby 0.2
language EN

[Gabby.Options Example]
    - Writing branching dialogue should be easy, don't you think?
    : Yes // Option A and Option B are displayed at the same time
        - I think so too!
        - The easier it is, the more people will do it!
    : No
        - ...
        - Really? Whatever...
    - I'm going to add lots of choices to my games. // This is shown after both options

// Give the player a choice with no effect
[Gabby.Fake Options]
    - Gabby's pretty cool, huh?
    : Yes!
    : Totally!
    - I think so too!
```

In the above example, when the player selects `Yes`, the flow will be as follows:
> _Writing branching dialogue should be easy, don't you think?_  
> **\> Yes**  
> _I think so too!_  
> _The easier it is, the more people will do it!_  
> _I'm going to add lots of choices to my games._  

Implementation Notes  
The dialogue engine:
- Must present all options to the game to be displayed for selection.
- Must wait for a selection to be provided from the game before advancing.
- Must not present the options themselves as dialogue, only as entries in an options list to be selected from.
- Must correctly handle dialogue blocks and returning to the parent block when completing a nested block.

----------------------
Ending Dialogue
---------------

The `end` keyword can be used anywhere within a dialogue block to end the dialogue. Lines after it will be ignored, and lines after it in the same dialogue block are inaccessible.

Example:
```
gabby 0.2
language EN

[Gabby.EndExample]
    - Gabby's pretty cool, huh?
    : Yes
    : No
        - What?! Ugh, of course it's cool!
        end
    - It makes writing dialogue so easy!
    end
```

Implementation Notes  
The dialogue engine:
- Must not close a dialogue block when end is reached. Lines after it are included in the dialogue block, even though they're inaccessible.
    - This is to avoid confusion when ending inner blocks early with extra lines following the end keyword.

----------------------
Actions
-------

Actions are callbacks that the game can handle, useful for setting state or triggering events. They are not displayed to the player, just executed by the dialogue engine and then skipped over.  
Actions are designated by `>`, followed by the action name and the parameters to the action surrounded by `()` and separated by `,`.

Valid actions:
- Must be a valid line.
- Must only contain alphanumeric characters.
- May contain 0 or more parameters.
- May contain whitespace around the action name, inside the parentheses, or between parameters, which will be trimmed
- May contain whitespace within parameters, which will be preserved

Usage:
```
> actionName(parameter1, parameter2)
> actionName( parameter 1, parameter 2 ) // The parameters here will be parsed as `parameter 1`, `parameter 2`
```

Example:
```
gabby 0.2
language EN

[Gabby.ActionExample]
    > playSound("introduction.ogg")
    > set("gabby.introduced", true)
    - Oh, hey! I'm Gabby.
    - Take this!
    > giveItem("gold", 50)
```

Implementation Notes  
The dialogue engine:
- Must send the event and parameters to the game to be handled
- Must execute the next line automatically
- Must parse quoted strings as strings, trimming the quotes but preserving all whitespace
- Must parse unquoted strings as if they were quoted, trimming any leading and trailing whitespace

----------------------
Jumps
-----

Jumps end the current dialogue and begin another dialogue immediately. Jumps are designated by two right angle brackets `>>`, followed by the character and dialogue name separated by a period `.`.

Valid jumps:
- Must follow the dialogue definition naming rules.
- Should refer to a valid dialogue.
- May refer to a dialogue defined in another file.

Usage:
```
>> Character.DialogueName
```

Implementation Notes  
The dialogue engine:
- Must automatically execute the jump and continue to the next line, not waiting for a signal to advance.
- Must support jumping to any dialogue in the same file.
- Must gracefully handle missing dialogue references and notify the game to handle it.
- Must support jumping to dialogues in separate loaded files.

----------------------
Conditionals
------------

Conditionals are used for simple branching based on game state. The condition is written similarly to an action, and can be parameterless or take parameters.
`if`, `else if`, and `else` are supported.

Valid conditionals:
- Must have at least one if clause.
- May have a single else clause.
- May have an arbitrary number of else if clauses.
- May have duplicate else if clauses, in which case the first one is used.

Example:
```
gabby 0.2
language EN

[Gabby.ConditionalExample]
    if hasExaminedWall
        - Remembering the pattern on the wall, you enter the code and the door opens.
    else
        - No matter how long you stare at it, you can't figure out how to open the door.

[Gabby.ConditionalExample2]
    if hasItem(boss key)
        - You use the key to unlock the door to the boss room.
    else
        - It won't budge.

[Gabby.ConditionalExample3]
    if character.isFriend
        - Hi!~
    else
        - Oh, hey.
```

----------------------
Tags
----

Tags are metadata that can be associated with lines. They're designated by `<>`, with comma separated values inside. Key/value pairs can be specified with `:`.

Valid tags:
- Must be enclosed with `<>`.
- Must be placed immediately before a line or dialogue definition, with only whitespace, newlines, or comments separating them.
- Must be written on a single line.
- May contain any number of comma separated values, or key-value pairs separated by `:`.
- May have whitespace surrounding tags, which will be trimmed.
- May have whitespace surrounding keys and values, which will be trimmed.
- May have whitespace within a tag, key, or value, which will be preserved.

Usage:
```
<key:value, anotherTag> // Dialogue definitions can be tagged
[Character.Dialogue]
    <tag> // Lines in a dialogue can be tagged
    (Character) Dialogue...
    <tag>
    - Dialogue...
    <tag> // Jumps, actions, and other control lines can be tagged
    >> Another.Dialogue
    <tag> // Individual options can be tagged
    : A
    <anotherTag>
    : B
```

Example:
```
gabby 0.2
language EN

// Dialogue tags can be used to generically find dialogues, ex. for randomly picking a greeting
<greeting>
[Gabby.RandomGreetingExampleA]
    - Hello!

<greeting>
[Gabby.RandomGreetingExampleB]
    - Hey there!

// Line tags can be used to change portraits or other character state
[Gabby.PortraitTagExample]
    <happy>
    - Hello!
    <dizzy, wobble>
    - Feeling dizzy....

// Individual options can be tagged, useful for cases like simple shops
[Gabby.OptionTagExample]
    - What do you want to buy?
    <cost:50> // This tag can be used to make it easy for the game to dynamically display the item's price, mark items that the player can't afford, etc.
    : Cheap Sword
        if canAfford(50)
            - It's yours'!
        else
            - Ah, come back when you can pay...
            end
    <cost:250>
    : Shiny Sword
        if canAfford(250)
            - Here you go, enjoy!
        else
            - Mm, sorry, the price is firm...
    - Come back anytime!
```

----------------------
Inline Dialogue Tags
---------

Tags can be written inline when writing dialogue lines. They are written as comma separated values or key-value pairs, following the same rules for tags but appearing within the parentheses for the dialogue line.

Usage:
```
(Character, tag1, tag2, key:value) A line of dialogue.
```

Example:
```
gabby 0.2
language EN

[Gabby.LineTags]
    (Gabby, very happy)  Guess what?!
    (Gabby, happy)       It's really easy to control portraits in your game.
    -                    Just use dialogue line tags!
    (Gabby, very happy)  It keeps the script nice and tidy!
```
