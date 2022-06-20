# Text Expander JS (Obsidian Plugin) - Shortcut-file library

These are sample shortcut-files for the Obsidian plugin [Text Expander JS](https://github.com/jon-heard/obsidian-text-expander-js).

To use these in your Obsidian vault, you can either clone or download this repo into a folder in your vault, or copy the raw text of one or more of these shortcut-files into notes in your vault.  Then:
1. Make sure that [Text Expander JS](https://github.com/jon-heard/obsidian-text-expander-js) is installed in your vault.
2. Open The Text Expander JS settings.
3. Go to the first setting: "Shortcut-files".
4. Click the "Add file reference" button.  This adds a new textfield below the setting.
5. Type the address of one of the shortcut-files in your vault into the new textfield.  The textfield will be red until the address is recognized.
6. Repeat steps 4 and 5 for each shortcut-file you want to use.
7. Close your settings.

## Reference - All shortcut-files in this repo

- [tejs_state](#tejs_state)
- [tejs_lists](#tejs_lists)
- [tejs_mythicv2](#tejs_mythicv2)
- [tejs_une](#tejs_une)
- [tejs_rpgtools](#tejs_rpgtools)
- [tejs_arrows](#tejs_arrows)
- [tejs_support](#tejs_support)
- [tejs_lipsum](#tejs_lipsum)

***

### tejs_state
This shortcut-file includes shortcuts that let the user manage a session state. Effectively allows saving and restoring data between sessions.

This shortcut-file also includes shortcuts that let the user manage "clips" of text.

- __help state__ - Display the help text.
- __reset state__ - Clear all state.
- __state__ - Expands to the current state (for session saving).
- __state {state}__ - Sets the current state based on {state}: a string created previously with the "state" shortcut.
- __clip__ - List all stored clips.
- __clip {name}__ - Expands to the value stored in clip {name}.
- __clipadd {name} {value}__ - Creates a clip {name} that stores {value}.
- __clipremove {name}__ - Removes the clip {name}.

***

### tejs_lists
Shortcuts for working with lists.

- __help lists__ - Display the help text.
- __reset lists__ - Clear all lists.
- __lists__ - Show all list and all items for each list.
- __lists list {listName}__ - Show all items in the list {listName}.
- __lists add {listName} {item}__ - Add {item} to the basic-list {listName}.  Allows duplicate items.  Cannot add to folder-lists or combo-lists.
- __lists pick {listName}__ - Get a random item from the list {listName}.
- __lists remove {listName} {item}__ - Remove an instance of {item} from the basic-list {listName}.  Cannot remove from folder-lists or combo-lists.
- __lists removelist {listName}__ - Remove the entire list {listName}.
- __lists addfolder {listName} {folder}__ - Create a folder-list named {listName} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the files in the linked folder.
- __lists addcombo {listName} {subList1} {subList2}...__ - Create a combo-list named {listName} that is linked to the sublists given as {subList1}, {subList2}, etc.  A "combo-list" is a list who's items are all the items of its linked sublists.

***

### tejs_mythicv2
Shortcuts for Mythic Variations 2.  This is an excellent "GM emulator" system for solo and GM'less gaming.  You can find more info about Mythic Variations 2 [here](http://wordmillgames.com/mythic-variations-2.html).

Uses __tejs_state__ shortcut-list (optional) to save & load this shortcut file's state.

Uses __tejs_lists__ shortcut-list (optional) for pcs, npcs and threads lists.

- __help mythic__ - Display the help text.
- __reset mythic__ - Reset mythic state to defaults and displays scene heading.
- __mythic details__ - Displays "details" mode state.  If "details" mode is enabled, expansions include the rolls behind the results.
- __mythic details {state}__ - Sets "details" mode based on {state}.
    - If {state} is 'y' then "details" mode is enabled
	- if {state} is 'n' then "details" mode is disabled.
- __detail__ - Make a detail check.
- __event__ - Make an event check.
- __fate {odds} {wanted}__ - Make a fate check based on {odds}: an optional number from -4 (impossible) to 4 (has to be), defaulting to 0 (50/50).  This is also based on {wanted}: an optional value of either 'n' or 'y', defaulting to 'y'.  The value {wanted} specifies the direction of the chaos modifier.
- __f {odds} {wanted}__ - Shorthand for "fate  {odds} {wanted}".
- __meaning action__ - Roll on the action meaning table.
- __meaning description__ - Roll on the description meaning table.
- __meaning__ - Shorthand for "meaning action".
- __listsget {listName}__ - A stub in case tejs_lists shortcut-file isn't available.
- __scene__ - Show the current scene.
- __scene {chaosAdjust}__ - Shift the chaos value by {chaosAdjust} (1, 0 or -1), then increment the current scene.
- __chaos__ - Show the current chaos value.
- __chaos++__ - Increase the chaos value by 1 (maximum of 6).
- __chaos--__ - Decrease the chaos value by 1 (minimum of 3).
- __chaos={value}__ - Set the chaos value to {value}.
- __descriptor__ - Generates a personality and activity descriptor for an NPC.
- __disposition {descriptorCount}__ - Rolls for an NPC's disposition, modified by {descriptorCount}.
- __disposition {base} {descriptorCount}__ - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.
- __action {dispositionAdjust}__ - Makes an NPC behavior check, modified by {dispositionAdjust}, the NPC's disposition.

***

### tejs_une
Shortcuts for UNE (The Universal NPC Emulator).  This is an excellent character generation system for tabletop role playing and general storytelling.  You can find more info about UNE [here](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

Zach Best, the writer and publisher of this product has, sadly, passed away.  Proceeds from buying UNE, and other [conjecture games](https://www.drivethrurpg.com/browse/pub/7251/Conjecture-Games) products go to supporting his family.

- __help une__ - Display the help text.
- __une {randomness} {relationship to pc} {demeanor}__ - Runs "une character" and "une interact" together.  {randomness} is an optional value for "une character".  {relationship to pc} and {demeanor} are optional values for "une interact".
- __une character {randomness}__ - Runs "identity", "power" and "motive" together.  {randomness} is an optional value for "power".
- __une interact {relationship to pc} {demeanor}__ - Runs "mood", "bearing" and "focus" together.  {relationship to pc} is an optional value for "mood".  {demeanor} is an optional value for "bearing".
- __une identity__ - Generates a 2-word description for a character.
- __une power {randomness}__ - Generates a character's power level relative to pc's power level, based on {randomness}: an optional number from 1 (order), to 5 (chaos), defaulting to 3 (standard).
- __une motive__ - Generates three 2-word descriptions for a character's motivations.
- __une mood {relationship to pc}__ - Generates a character's willingness to socialize for this interaction, based on {relationship to pc}: a optional number from 1 (love), to 7 (hate), defaulting to 4 (neutral).
- __une bearing {demeanor}__ - Generate a character's attitude for this interaction, based on {demeanor}: an optional number from 1-8, defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced.
- __une focus__ - Generate a character's primary interest for this interaction.

***

### tejs_rpgtools
Shortcuts to help in playing tabletop rpgs, either group or solo.

- __help rpgtools__ - Display the help text.
- __tbl twist__ - Random table: a plot twist.  [Source](https://jvhouse.xyz/plot-twist-situations).

***

### tejs_arrows
Shortcuts for writing various arrows:
→←↑↓
▶◀▲▼
⇒⇐⇑⇓
↔↕⇔⇕⇄⇅
↗↘↖↙
↪↩↻↺↝↜

- __help arrows__ - Display the help text.

***

### tejs_lipsum
A decent Lorum Ipsum generator.

- __help lipsum__ - Display the help text.
- __lipsum {paragraph count}__ - Generates a lorem ipsum text with {paragraph count} paragraphs.  If {paragraph count} is omitted, it defaults to 1.

***

### tejs_support
Various helper shortcuts of minimal category.

- __help support__ - Display the help text.
- __rngseed {seed}__ - Sets Math.random to a custom random number generator with a seed of {seed} (a non-zero integer).  Useful for testing.  If {seed} is omitted, random number generator is returned to javascript default.

***
