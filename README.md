# Text Expander JS (Obsidian Plugin) - Shortcut-file library

These are sample shortcut-files for the Obsidian plugin [Inline Scripts](https://github.com/jon-heard/obsidian-inline-scripts).

The easiest way to use these shortcut-files in your Obsidian vault is to click the "Import Full Library" button, near the top of the plugin settings, beside "Shortcut-files".  This will download the full library into your vault, after which point you can disable or remove shortcut-files as you wish.

To use an indiviual shortcut-file from this library in your Obsidian vault, you can copy the raw text of the shortcut-file and paste it into a note in your vault.  Then:
1. Make sure that the plugin [Inline scripts](https://github.com/jon-heard/obsidian-inline-scripts) is installed in your vault.
2. Open The Text Expander JS settings.
3. Go to the first setting: "Shortcut-files".
4. Click the "Add file reference" button.  This adds a new textfield below the setting.
5. Type the address of the vault-note into the new textfield.  The textfield will be red until the address is recognized.
6. Close the settings and type `;;help;` (!!help! on mobile) to review the new shortcuts.

## Reference - All shortcut-files in this repo

- [state.sfile](#statesfile)
- [lists.sfile](#listssfile)
- [varnotes.sfile](#varnotessfile)
- [mythicv2.sfile](#mythicv2sfile)
- [mythicgme.sfile](#mythicgmesfile)
- [une.sfile](#unesfile)
- [adventurecrafter.sfile](#adventurecraftersfile)
- [rpgtools.sfile](#rpgtoolssfile)
- [clips.sfile](#clipssfile)
- [arrows.sfile](#arrowssfile)
- [lipsum.sfile](#lipsumsfile)
- [support.sfile](#supportsfile)

***

### state.sfile
This shortcut-file includes shortcuts that let the user manage a session state.  Effectively allows saving and restoring data between sessions.

This shortcut file can be used by other shortcut-files to help them save/restore their states.

- __reset state__ - Clear all state.
- __state__ - Expands to a string representing the current state (for session saving).
- __state {state: required, text}__ - Sets the current state based on {state}: a string created with the parameterless "state" shortcut.

***

### lists.sfile
Shortcuts for working with lists.

- __reset lists__ - Clear all lists.
- __lists__ - Show all list and all items for each list.
- __lists list {list name: required, name}__ - Show all items in the list {list name}.
- __lists pick {list name: required, name}__ - Get a random item from the list {list name}.
- __lists add {list name: required, name} {item: required, text}__ - Add {item} to the list {list name}.  Allows duplicate items.
    - Can only add to basic lists and combo lists that contain basic lists.
- __lists remove {list name: required, name} {item: required, text}__ - Remove an instance of {item} from the list {list name}.
    - Can only remove from basic lists and combo lists that contain basic lists.
- __lists removelist {list name: required, name}__ - Remove the entire list {list name}.
- __lists addfolder {list name: required, name} {folder: required, text}__ - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.
- __lists addcombo {list name: required, name} {sub list 1: optional, name} {sub list 2: optional, name}...__ - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.

***

### varnotes.sfile
Setup and manage variables within notes.  Can be used by other shortcut-files for working with data in notes.

- __reset varnotes__ - Removes all stored varnotes from the session state.
- __varnotes__ - Lists all varnotes and the files they are connected to.
- __varnotes vars {varnote name: required, name}__ - Lists all the variables for the varnote named {varnote name}.
- __varnotes add {varnote name: required, name} {file address: required, text}__ - Adds a varnote named {varnote name} that is attached to the file at {file address}.
- __varnotes remove {varnote name: required, name}__ - Removes the varnote {varnote name}.
- __varnotes get {varnote name: required, name} {variable name: required name}__ - Gets the value of the variable named {variable name} in the varnote named {varnote name}.
        Alternative shortcut: __vn {varnote name} {variable name}__
- __varnotes set {varnote name: required, name} {variable name: required name} {value: required text}__ - Sets the variable named {variable name} to {value}, in the varnote named {varnote name}.
        Alternative shortcut: __vnset {varnote name} {variable name} {value}__
- __varnotes refresh__ - Get the latest variables from all varnotes.
- __varnotes refresh {varnote name: required, name}__ - Pull the variables from varnote {varnote name}.

***

### mythicv2.sfile
_(disabled by default, due to incompatibility with mythicgme.sfile)_

Shortcuts for Mythic Variations 2.  This is an excellent "GM emulator" system for solo and GM'less gaming.  You can find more info about Mythic Variations 2 at [wordmill games](http://wordmillgames.com/mythic-variations-2.html).

Incompatible with __mythicgme.sfile__.  If __mythicgme.sfile__ comes before __mythicv2.sfile__ in the shortcut-list, then __mythicv2.sfile__ will be disabled.

- __reset mythicv2__ - Reset mythic state to defaults and displays scene heading.
        Alternative shortcut: __reset mythic__.
- __mythicv2 details {state: optional, y or n}__ - If {state} is given, assigns it to the mythicv2 "details" mode.  Otherwise, displays the current "details" mode.
- __fate {odds: optional, -4 to 4} {wanted: optional, y or n}__ - Make a fate check based on {odds}: a value from -4 (impossible) to 4 (has to be), defaulting in 0 {50/50}.
 {odds} can also be the specific text of the odds, such as "impossible", "sure thing", etc.
 This is also based on {wanted}: a y/n, defaulting in y.  {wanted} specifies the direction of the chaos modifier.
        Alternative shortcut: __f {odds} {wanted}__.
- __detail__ - Make a detail check.
- __event__ - Make an event check.
- __meaning action__ - Roll on the action meaning tables.
        Alternative shortcut: __meaning__.
- __meaning description__ - Roll on the description meaning tables.
- __scene__ - Show the current scene.
- __scene {chaos adjust: required, -1 or 1}__ - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.
- __chaos__ - Show the current chaos value.
- __chaos--__ - Decrease the chaos value by 1 (minimum of 3).
- __chaos++__ - Increase the chaos value by 1 (maximum of 6).
- __chaos={value: required, 3 to 6}__ - Set the chaos value to {value}, an integer from 3 to 6.
- __descriptors__ - Generates a personality and activity descriptor for an NPC.
        Alternative shortcut: __descriptor__.
- __disposition {descriptor count: required, -3 to 3}__ - Rolls for an NPC's disposition, modified by {descriptor count}, which represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __disposition {descriptorCount: required, -3 to 3} {base: required, 2 to 20}__ - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.  {descriptor count} represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __action {dispositionAdjust: required, -2, 0, 2, 4}__ - Makes an NPC behavior check, modified by {dispositionAdjust}: the modifier of the NPC's disposition.

***

### mythicgme.sfile
Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

Incompatible with __mythicv2.sfile__.  If __mythicv2.sfile__ comes before __mythicgme.sfile__ in the shortcut-list, then __mythicgme.sfile__ will be disabled.

- __reset mythicgme__ - Reset mythic state to defaults and displays scene heading.
        Alternative shortcut: __reset mythic__.
- __fate {odds: optional, text}__ - Make a fate check based on {odds}.  Odds can be from -4 (impossible) to 6 (has to be) and defaults 0 (50/50).  It can also be the specific text of the odds, such as "impossible", "sure thing", etc.
        Alternative shortcut: __f {odds}__.
- __scene__ - Show the current scene.
- __scene {chaosAdjust: required, -1 or 1}__ - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.
- __event__ - Make an event check.
- __meaning__ - Roll on the meaning tables.
- __chaos__ - Show the current chaos value.
- __chaos--__ - Decrease the chaos value by 1 (minimum of 1).
- __chaos++__ - Increase the chaos value by 1 (maximum of 9).
- __chaos={value: required, 1 to 9}__ - Set the chaos value to {value}.

***

### une.sfile
Shortcuts for UNE: The Universal NPC Emulator.  UNE is an excellent character generation system for tabletop role playing and general storytelling.  It was designed by Zach Best. 
 You can find more info about UNE at its [drivethrurpg page](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

- __une {randomness: optional, 1 to 5} {relationship to pc: optional, 1 to 7} {demeanor: optional, 1 to 8}__ - Runs "une character" and "une interact" together.  {randomness} is a value for "une character".  {relationship to pc} and {demeanor} are values for "une interact".
- __une character {randomness: optional, 1 to 5}__ - Runs "identity", "power" and "motive" together.  {randomness} is a value for "power".
- __une interact {relationship to pc: optional, 1 to 7} {demeanor: optional, 1 to 8}__ - Runs "mood", "bearing" and "focus" together.  {relationship to pc} is a value for "mood".  {demeanor} is a value for "bearing".
- __une identity__ - Generates a 2-word description for a character.
- __une power {randomness: optional, 1 to 5}__ - Generates a character's power level relative to pc's power level, based on {randomness}: a number from 1 (order), to 5 (chaos), defaulting at 3 (standard).
- __une motive__ - Generates three 2-word descriptions for a character's motivations.
- __une mood {relationship to pc: optional, 1 to 7}__ - Generates a character's willingness to socialize for this interaction, based on {relationship to pc}: a number from 1 (love), to 7 (hate), defaulting at 4 (neutral).
- __une bearing {demeanor: optional, 1 to 8}__ - Generate a character's attitude for this interaction, based on {demeanor}: a number defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced
- __une focus__ - Generate a character's primary interest for this interaction.

***

### adventurecrafter.sfile
Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

- __reset adventurecrafter__ - Reset adventurecrafter state to defaults.
- __turning point__ - Get a list of five plot points to represent a major milestone in a plotline.
- __plot point__ - Get a single plot point, generated from the plot points table.
- __themes pick__ - Pick a weighted random theme, as per the Adventure Crafter rules.
- __themes__ - Show the ordered list of themes.
- __themes fill__ - Fills the remaining open theme slots with random themes.
- __themes roll__ - Fills the next open theme slot with a random theme.
- __themes add {theme id: optional, 1 to 5}__ - If {theme id} is NOT included, this shortcut shows the options for {theme id}.  If {theme id} IS included, this shortcut fills the next open theme slot with the theme of {theme id}.  {theme id} can be one of these options:
1 (Action), 2 (Tension), 3 (Mystery), 4 (Social), 5 (Personal).
- __themes clear__ - Clear all theme slots.
- __ac chars gen__ - Generate a new character description, as per the Adventure Crafter rules.
- __ac chars pick__ - Pick a random char, as per the Adventure Crafter rules.
- __ac chars__ - List the character entries.
- __ac chars add {character: required, text}__ - Add {character} to the list of character entries.
- __ac plots dupe {character index: optional, >0}__ - If {character index} is NOT included, this shortcut shows the options for {character index}.  If {character index} IS included, this shortcut duplicates the character that is indexed by {character index}.
- __ac chars remove {character index: optional, >0}__ - If {character index} is NOT included, this shortcut shows the options for {character index}.  If {character index} IS included, this shortcut removes one entry of the character that is indexed by {character index}.
- __ac plots pick__ - Pick a random plotline, as per the Adventure Crafter rules.
- __ac plots__ - List the plotline entries.
- __ac plots add {plotline: required, text}__ - Add {plotline} to the list of plotline entries.
- __ac plots dupe {plotline index: optional, >0}__ - If {plotline index} is NOT included, this shortcut shows the options for {plotline index}.  If {plotline index} IS included, this shortcut duplicates the plotline that is indexed by {plotline index}.
- __ac plots remove {plotline index: optional, >0}__ - If {plotline index} is NOT included, this shortcut shows the options for {plotline index}.  If {plotline index} IS included, this shortcut removes one entry of the plotline that is indexed by {plotline index}.

***

### rpgtools.sfile
Shortcuts to help in playing tabletop rpgs, either group or solo.

- __tbl twist__ - Random table: a plot twist.  [Source](https://jvhouse.xyz/plot-twist-situations).

***

### clips.sfile
Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

- __reset clips__ - Remove all clips.
- __clips__ - Lists all stored clips.
- __clips get {name: required, name}__ - Expands to the value stored in clip {name}.
        Alternative shortcut: __cg {name}__.
- __clips add {name: required, name} {value, required, text}__ - Creates a clip named {name} that stores the string {value}.
- __clips expansion {name: required name}__ - Creates a clip named {name} that stores the previous expansion.
- __clips remove {name: required, name}__ - Removes the clip named {name}.

***

### arrows.sfile
Shortcuts for writing various arrows:
- →←↑↓
- ▶◀▲▼
- ⇒⇐⇑⇓
- ↔↕⇔⇕⇄⇅
- ↗↘↖↙
- ↪↩↻↺↝↜

***

### lipsum.sfile
A decent Lorum Ipsum generator.

- __lipsum {paragraph count: optional, >0}__ - Generates a lorem ipsum text with {paragraph count} paragraphs.  If {paragraph count} is omitted, it defaults to 1.

***

### support.sfile
Various helper shortcuts of minimal category.

- __rngseed {seed: optional, >0}__ - Sets Math.random to a custom random number generator with a seed of {seed}.  Useful for testing.  If {seed} is omitted, the random number generator is returned to javascript default.

***
