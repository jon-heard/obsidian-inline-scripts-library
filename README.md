# Inline Scripts (Obsidian Plugin) - Shortcut-file library

These are sample shortcut-files for the Obsidian plugin [Inline Scripts](https://github.com/jon-heard/obsidian-inline-scripts).

The easiest way to use these shortcut-files in your Obsidian vault is to click the "Import Full Library" button, near the top of the plugin settings, beside "Shortcut-files".  This will download the full library into your vault, after which point you can disable or remove shortcut-files from the library as you wish.

To use an indiviual shortcut-file from this library in your Obsidian vault, you can copy the raw text of the shortcut-file and paste it into a note in your vault.  Then:
1. Make sure that the plugin [Inline scripts](https://github.com/jon-heard/obsidian-inline-scripts) is installed in your vault.
2. Open The Inline Scripts plugin settings.
3. Go to the first setting: "Shortcut-files".
4. Click the "Add file reference" button.  This adds a new textfield below the setting.
5. Type the address of the vault-note into the new textfield.  The textfield will be red until the address is recognized.
6. Close the settings and type `;;help;` (!!help! on mobile) to review the new shortcuts.

## Reference - All shortcut-files in this repo

- [state.sfile](#statesfile)
- [lists.sfile](#listssfile)
- [notevars.sfile](#notevarssfile)
- [notepick.sfile](#notepicksfile)
- [mythicv2.sfile](#mythicv2sfile)
- [mythicgme.sfile](#mythicgmesfile)
- [une.sfile](#unesfile)
- [adventurecrafter.sfile](#adventurecraftersfile)
- [cards.sfile](#cardssfile)
- [tablefiles.sfile](#tablefilessfile)
- [rpgtools.sfile](#rpgtoolssfile)
- [plugin_diceroller.sfile](#plugin_dicerollersfile)
- [files.sfile](#filessfile)
- [clips.sfile](#clipssfile)
- [arrows.sfile](#arrowssfile)
- [lipsum.sfile](#lipsumsfile)
- [lists_ui.sfile](#lists_uisfile)
- [mythicv2_ui.sfile](#mythicv2_uisfile)
- [mythicgme_ui.sfile](#mythicgme_uisfile)
- [une_ui.sfile](#une_uisfile)
- [cards_pileviewer.sfile](#cards_pileviewersfile)
- [cards_ui.sfile](#cards_uisfile)
- [clips_ui.sfile](#clips_uisfile)


***

### state.sfile
This shortcut-file includes shortcuts to manage a session state.  This allows one to save and restore the state of other shortcut-files across Obsidian sessions.

This shortcut file can be used by other shortcut-files to let them create data that can persist across sessions of Obsidian.

This shortcut-file has 2 tutorial videos available.  The first one is right for most users, but if you are making your own shortcut-file, the second one shows you how to connect it to the state system:
[Using the "state" shortcut-file to save and load session state](https://www.youtube.com/watch?v=WHQuQm3RieY) (runtime 2:23)
[Setup a shortcut-file for saving & loading with the "state" shortcut-file](https://www.youtube.com/watch?v=K-6Xy3YLuh4) (runtime 6:12)

- __state reset__ - Clears all session state.
- __state get__ - Expands to a state-string - a string containing all data for the current session state.
- __state restore__ - Loads the session state from the last "state get" shortcut expansion in the current note.
- __state set {state: text}__ - Loads the session state from {state}, a state-string created with the "state get" shortcut.
- __state save__ - Store the state to file.  This is called automatically after each shortcut expansion that triggers a state change.

***

### lists.sfile
Shortcuts for working with lists.

This shortcut-file has a tutorial video available:
[Using the "list" shortcut-file to manage lists](https://www.youtube.com/watch?v=xIYpnBKdYRg) (runtime 5:34)

- __lists reset__ - Clears all lists.
***
- __lists__ - Shows all lists and their info: type, associations and items.
- __lists {list name: name text}__ - Shows all info for the single list {list name}: type, associations and items.
- __lists rename {original list name: name text} {new list name: name text}__ - Changes the name of list {original list name} to {new list name}.
- __lists removelist {list name: name text}__ - Removes the entire list {list name}.
- __lists pick {list name: name text}__ - Gets a random item from the list {list name}.
- __lists add {list name: name text} {item: text}__ - Adds {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists which contain basic lists.
- __lists replace {list name: name text} {item: text} {replacement: text}__ - Replaces all instances of {item} with {replacement}.  {item} must be quoted if it has any space.
- __lists remove {list name: name text} {item: text}__ - Removes an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists which contain basic lists.
***
- __lists addfolder {list name: name text} {folder: path text}__ - Creates a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.
- __lists addcombo {list name: name text} {sub-list: name text separated by spaces}__ - Creates a combo-list named {list name} that is linked to the lists given in {sub-list}.  A "combo-list" is a list who's items are all of the items of its linked sub-lists.
***
- __lists shortcutbatch {list name: name text} {shortcut: text}__ - Runs shortcut {shortcut} once for each item in list {list name}, replacing "%1" in {shortcut} with the item.
- __lists fromfile lines {list name: name text} {file: path text}__ - Takes the file {file}, breaks it up into individual lines and adds each of those lines, as items, to list {list name}.

***

### notevars.sfile
This shortcut-file includes shortcuts to get and set note-variables.  Note-variables are variables set in the YAML frontmatter.  They can be used by many plugins, including DataView and TTRPG StatBlocks.

This shortcut file can be used by other shortcut-files to let them read and manipulate data in notes for many uses, including TTRPG character sheets.

This shortcut-file has a tutorial video available:
[Using the "notevars" shortcut-file to work with note variables](https://www.youtube.com/watch?v=EsV4WcMwhbA) (runtime 6:59)

- __notevars reset__ - Clears the isMarkdownRefreshed flag.
***
- __notevars isMarkdownRefreshed {state: y OR n, default: ""}__ - If {state} is given, assigns it to the notevars "isMarkdownRefreshed" flag.  Otherwise, displays the current "isMarkdownRefreshed" flag.
    - If isMarkdownRefreshed flag is set then a note's markdown is refreshed each time one of it's variables is set.
***
- __notevars get {note name: path text} {variable name: name text}__ - Expands to the value of variable {variable name} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars getArray {note name: path text} {array name: name text} {index: >=0}__ - Expands to the value of item {index} of array {array name} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars set {note name: path text} {variable name: name text} {value: text}__ - Sets the value of variable {variable name} to {value} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars setArray {note name: path text} {array name: name text} {values: comma separated text}__ - Sets the values of array {array name} to {value1}, {value2}, etc. in note {note name}.  If {note name} is "." then it represents the current note.

***

### notepick.sfile
Shortcuts to pick X random notes from a folder of notes and to get their front matter data.

This shortcut-file has a tutorial video available:
[Using the "notepick" shortcut-file to randomly pick notes & get variables](https://www.youtube.com/watch?v=G1mvl-VwbIQ) (runtime 3:22)

- __notepick reset__ - Clears all picks.
***
- __notepick pickFromFolderAndGetPick {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} {to ignore: | separated filenames}__ - Combines the shortcuts "notepick pickFromFolder" and "notepick getPick".
- __notepick pickFromFolderAndGetFrontmatter {folder name: path text} {pick count: >0, default: 1} {pick id: text, default: ""} {to ignore: | separated filenames}__ - Combines the shortcuts "notepick pickFromFolder" and "notepick frontmatter".
***
- __notepick pickFromFolder {folder name: path text} {count: >0, default: 1} {pick id: name text, default: ""} {to ignore: | separated filenames}__ - Picks {count} random notes from folder {folder name} and remembers them as {pick id}.  Any files in {to ignore} are never picked.
- __notepick getPick {pick id: name text, default: ""}__ - Gets a list of the files last picked for {pick id}.
- __notepick frontmatter {pick id: name text, default: ""}__ - Gets the frontmatter from the notes that are remembered in {pick id}.

***

### mythicv2.sfile
_(disabled by default, due to incompatibility with mythicgme.sfile)_

Shortcuts for Mythic Variations 2.  Mythic GME, along with it's "Variations 2" supplement, is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic Variations 2 at [wordmill games](http://wordmillgames.com/mythic-variations-2.html).

This shortcut-file has a tutorial video available:
[Using the "mythicv2" shortcut-file to play Mythic Variations 2](https://www.youtube.com/watch?v=fvrxAg22lYg) (runtime 7:27)

Incompatible with __mythicgme.sfile__.  If __mythicgme.sfile__ comes before __mythicv2.sfile__ in the shortcut-list, then __mythicv2.sfile__ will be disabled.

- __mythicv2 reset__ - Resets mythic state to defaults and displays scene heading.
- __mythicv2 details {state: y OR n, default: ""}__ - If {state} is given, assigns it to the mythicv2 "details" mode.  Otherwise, displays the current "details" mode.
***
- __fate {odds: -4 TO 4 ("impossible" to "has to be"), default: 0 ("50/50")} {wanted: y OR n, default: y}__ - Makes a fate check based on {odds}.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
- __fate {odds: text, default: "50/50"} {wanted: y OR n, default: y}__ - Makes a fate check based on {odds}: a specific text such as "impossible", "sure thing", etc.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
- __detail__ - Makes a detail check.
- __event__ - Makes an event check.
***
- __meaning action__ - Rolls on the action meaning tables.
	- Alternative: __meaning__
- __meaning description__ - Rolls on the description meaning tables.
***
- __scene get__ - Shows the current scene.
- __scene {chaos adjust: -1 OR 1}__ - Shifts the chaos value by {chaosAdjust}, then increments the current scene and run a scene check.
- __chaos__ - Shows the current chaos value.
- __chaos--__ - Decreases the chaos value by 1 (minimum of 3).
- __chaos++__ - Increases the chaos value by 1 (maximum of 6).
- __chaos={value: 3 TO 6}__ - Sets the chaos value to {value}, an integer from 3 to 6.
***
- __descriptors__ - Generates a personality and activity descriptor for an NPC.
- __disposition {descriptor count: -3 TO 3}__ - Rolls for an NPC's disposition, modified by {descriptor count}, which represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __disposition {descriptorCount: -3 TO 3} {base: 2 TO 20}__ - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.  {descriptor count} represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __action {dispositionAdjust: -2 OR 0 OR 2 OR 4}__ - Makes an NPC behavior check, modified by {dispositionAdjust}: the modifier of the NPC's disposition.

***

### mythicgme.sfile
Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

This shortcut-file has a tutorial video available:
[Using the "mythicgme" shortcut-file to play Mythic GME](https://www.youtube.com/watch?v=NwHwctDp_vM) (runtime 5:00)

Incompatible with __mythicv2.sfile__.  If __mythicv2.sfile__ comes before __mythicgme.sfile__ in the shortcut-list, then __mythicgme.sfile__ will be disabled.

- __mythicgme reset__ - Resets mythic state to defaults and displays scene heading.
***
- __fate {odds: -4 TO 6 ("impossible" to "has to be"), default: 0 ("50/50")}__ - Makes a fate check based on {odds}.
- __fate {odds: text, default: "50/50"}__ - Makes a fate check based on {odds}: a specific text, such as "impossible", "sure thing", etc.
- __scene get__ - Shows the current scene.
- __scene {chaosAdjust: -1 OR 1}__ - Shifts the chaos value by {chaosAdjust}, then increments the current scene and runs a scene check.
***
- __event__ - Makes an event check.
- __meaning__ - Rolls on the meaning tables.
***
- __chaos__ - Shows the current chaos value.
- __chaos--__ - Decreases the chaos value by 1 (minimum of 1).
- __chaos++__ - Increases the chaos value by 1 (maximum of 9).
- __chaos={value: 1 TO 9}__ - Sets the chaos value to {value}.

***

### une.sfile
Shortcuts for UNE: The Universal NPC Emulator.  UNE is an excellent character generation system for tabletop role playing and general storytelling.  It was designed by Zach Best. 
 You can find more info about UNE at its [drivethrurpg page](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

- __une {randomness: 1 TO 5 (order to chaos), default: 3 (standard)} {relationship TO PC: 1 to 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Runs "une character" and "une interact" together.  {randomness} is a value for "une character".  {relationship to PC} and {demeanor} are values for "une interact".
***
- __une character {randomness: 1 TO 5 (order to chaos), default: 3 (standard)}__ - Runs "identity", "power" and "motive" together.  {randomness} is a value for "power".
- __une interact {relationship to PC: 1 TO 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Runs "mood", "bearing" and "focus" together.  {relationship to PC} is a value for "mood".  {demeanor} is a value for "bearing".
***
- __une identity__ - Generates a 2-word description for an NPC.
- __une power {randomness: 1 TO 5 (order to chaos), default: 3 (standard)}__ - Generates an NPC's power level relative to PC's power level, based on {randomness}.
- __une motive__ - Generates three 2-word descriptions for an NPC's motivations.
***
- __une mood {relationship to PC: 1 TO 7 (love to hate), default: 4 (neutral)}__ - Generates an NPC's willingness to socialize for this interaction, based on {relationship to PC}.
- __une bearing {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Generates an NPC's attitude for this interaction, based on {demeanor}: a number defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced
- __une focus__ - Generates an NPC's primary interest for this interaction.

***

### adventurecrafter.sfile
Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  It was designed by Tana Pigeon.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

This shortcut-file has a tutorial video available:
[Using the "adventurecrafter" shortcut-file to play with AdventureCrafter](https://www.youtube.com/watch?v=-z922-50K-o) (runtime 7:29)

- __adventurecrafter reset__ - Resets adventurecrafter state to defaults.
***
- __turning point__ - Gets a list of five plot points to represent a major milestone in a plotline.
- __plot point__ - Gets a single plot point, generated from the plot points table.
***
- __themes pick__ - Picks a random theme from the chosen themes, as per the Adventure Crafter rules.
- __themes__ - Shows the list of picked themes.
- __themes add__ - Chooses a theme for the next unchosen theme-slot.
- __themes roll__ - Picks a random theme for the next unchosen theme-slot.
- __themes fill__ - Picks random themes for the remaining unchosen theme-slots.
- __themes clear__ - Clears all chosen themes.
***
- __ac chars gen__ - Generates a new character description, as per the Adventure Crafter rules.
***
- __ac chars pick__ - Picks a random character, as per the Adventure Crafter rules.
- __ac chars__ - Lists all character entries.
- __ac chars add {character: text}__ - Adds {character} to the list of character entries.
- __ac chars dupe__ - Asks the user for a character from the characters list and adds a new entry for that character.
- __ac chars reduce__ - Asks the user for a character from the characters list and removes one entry for that character.
- __ac chars rename__ - Asks the user for a character from the characters list and what to change it's text to, then changes the character's text.
***
- __ac plots pick__ - Picks a random plotline, as per the Adventure Crafter rules.
- __ac plots__ - Lists all plotline entries.
- __ac plots add {plotline: text}__ - Adds {plotline} to the list of plotline entries.
- __ac plots dupe__ - Asks the user for a plotline from the plotlines list and adds a new entry for that plotline.
- __ac plots reduce__ - Asks the user for a plotline from the plotlines list and removes one entry for that plotline.
- __ac plots rename__ - Asks the user for a plotline from the plotlines list and what to change it's text to, then changes the plotline's text.

***

### cards.sfile
Shortcuts for working with virtual cards.  Almost all of these shortcuts work on piles of cards, rather than individual cards.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=-m4n7d3aKC8) (runtime 9:01)

- __cards reset__ - Clears all card-piles.
- __cards settings {size: >0, default: ""} {back-image: path text, default: ""}__ - Updates the settings for the cards system.
    - __{size}__ - The width for all cards, in pixels.  Card height scales to match.
    - __{back-image}__ - The path to an image file to represent face-down cards, or "default" to reset to the default back-image.
***
- __cards pile {pile id: name text} {facing: up OR down, default: down} {show moved: y OR n, default: prior}__ - Creates an empty card-pile {pile id} with all cards facing {facing} (face-up or face-down).  If {show moved}, cards that are drawn or picked into the {pile id} card-pile are also printed to the note.
- __cards remove {pile id: name text} {recall: y OR n, default: n}__ - Removes the {pile id} card-pile, including all cards within it.  If {recall}, all cards in {pile id} are recalled before the {pile id} card-pile is removed.  If the {pile id} card-pile is the origin for any cards, then they are orphaned, and immediately re-origined to their current card-pile.
- __cards pilesettings {pile id: name text} {facing: up OR down, default: current} {show moved: y OR n, default: current}__ - Updates the settings for the {pile id} card-pile.
    - __{facing}__ - Are the {pile id} card-pile's cards shown face-up or face-down?
    - __{show moved}__ - Are cards that are drawn or picked into the {pile id} card-pile also printed to the note?
***
- __cards fromfolder {pile id: name text} {folder: path text}__ - Creates cards based on images in {folder} and puts them into the {pile id} card-pile.
	- Note that this does not randomize the new cards.  Call the "cards shuffle" shortcut to do that.
***
- __cards list__ - Lists all card-piles.
- __cards peek {count: >0 OR "all", default: 1} {pile id: name text, default: prior} {from the bottom: y OR n, default: prior OR n}__ - Displays the first {count} cards in the {pile id} card-pile, or ALL cards if {count} is "all".  If {from the bottom}, displays the LAST {count} cards instead.
***
- __cards draw {count: >0 OR "all", default: 1} {destination pile id: name text, default: prior} {source pile id: name text, default: prior}__ - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.
- __cards pick {destination pile id: name text, default: prior} {source pile id: name text, default: prior}__ - Has the user choose cards from the {source pile id} card-pile.  Moves the chosen cards into the {destination pile id} card-pile.
***
- __cards shuffle {pile id: name text} {rotate: y OR n, default: n}__ - Randomizes the card order for the {pile id}.  If {rotate}, then card rotations are also randomized.
	- Rotation typically means 0 or 180 degrees (right-side-up or up-side-down), but can also mean 90 or 270 degrees if the card is square.
- __cards unrotate {pile id: name text}__ - Sets the rotation for all cards in the {pile id} card-pile to 0 (right-side-up).
- __cards reverse {pile id: name text}__ - Reverses the order of the cards in the {pile id} card-pile.
- __cards recall {pile id: name text}__ - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.  If {facing} is specified, all cards in {pile id} are then put to face {facing}.
- __cards reorigin {pile id: name text}__ - Sets the origin of all cards in the {pile id} card-pile to {pile id}.
***
- __cards import {pile id: name text, default: table} {data: text}__ - Imports the {data} into the {pile id} card pile.
- __cards export {pile id: name text}__ - Expands to a data-string containing all date for the {pile id} card-pile.

***

### tablefiles.sfile
Shortcut file to load tables from files and roll them.

- __tbl reset__ - Clears registered table paths and table path configurations.
- __tbl add {path: path string}__ - Adds {path} to the list of registered table paths.  {path} is either an individual table file, or a folder filled with table files.
- __tbl addfoldertable {path: path string}__ - Adds the folder {path} to the list of registered table paths as a folder-table.  A folder-table is a virtual table where each item is a file in the {path} folder.
- __tbl list__ - Get a list of the registered table paths.
- __tbl roll__ - Get random results from one of the registered tables.  Shows a popup to allow selecting the table and how to get results from it.
- __tbl roll {table file: path text} {parameters: text, default: ""}__ - Get random results from table {table file}.  If provided, {parameters} can alter the results.  {parameters} is expected to be a comma-separated list of parameters in "key: value" form.  Here are accepted parameters:
	- __count__ - A positive integer defaulting to 1.  Determines number of items picked.
	- __uniquePicks__ - "true" or "false", defaulting to "false".  If true, each item can be picked only once for this roll.
	- __format__ - "commas", "bullets" or "periods", defaulting to "commas".  Determines the format of the output.
	- __isFolderTable__ - "true" or "false", defaulting to "false".  If true, {table file} must be a folder path, and the result is picks from the files within it.
	- __useConfig__ - If true, __startOffset__ and __itemFormat__ are determined by the current configuration for the given table file.
	- __startOffset__ - A positive integer defaulting to 0.  Defines what line the table starts on in {table file}.  This is ignored for folder-tables.
	- __itemFormat__ - A regex string defaulting to `(.*)`.  Determines what part of each item is printed out, as well as what part of each item is used as the weight value.  The default prints out the entire item.
	- __useExpressionFormat__ - If true, the result is outputted in the standard expression format.

***

### rpgtools.sfile
Shortcuts to help in playing tabletop rpgs, either group or solo.

- __tbl twist__ - Rolls a plot twist from random tables.  <a href='https://jvhouse.xyz/plot-twist-situations'>Source</a>.

***

### plugin_diceroller.sfile
A shortcut to run commands through the Dice Roller plugin.

- __diceroller {command: text}__ - Runs {command} through the Dice Roller plugin and expands to the result.

***

### files.sfile
Shortcuts for working with files in the vault.

- __files extensionchange {file name: path text} {extension: nospace text, default: "md"}__ - Changes the extension of file {file name} to {extension}.
- __files shortcutbatch {folder name: path text} {shortcut: text}__ - Runs shortcut {shortcut} once for each file in folder {folder name}, replacing "%1" in {shortcut} with the file's name.

***

### clips.sfile
Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

- __clips reset__ - Removes all clips.
- __clips__ - Lists all stored clips.
- __clips set {name: name text} {value: text}__ - Creates / Sets a clip named {name} to the string {value}.
- __clips get {name: name text}__ - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: name text}__
- __clips expansion {name: name text}__ - Creates a clip named {name} that stores the previous expansion.
- __clips remove {name: name text}__ - Removes the clip named {name}.

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

- __lipsum {paragraph count: >0, default: 1}__ - Generates a lorem ipsum text with {paragraph count} paragraphs.

***

### lists_ui.sfile
An extension to __lists.sfile__ that provides graphical ui versions of shortcuts.

- __ui lists reset__ - Clears all lists.
***
- __ui lists__ - User chooses a list (options include "all lists"), then shows info on the chosen list.
- __ui lists rename__ - User chooses a list, and enters a new name for it.  The list is renamed.
- __ui lists removelist__ - User chooses a list, which is removed.
- __ui lists pick__ - User chooses a non-empty list, a random item is picked from that list and returned.
***
- __ui lists add__ - User chooses a list to add to and an item to add.  The item is added to the list.
    - Identical items within a list are allowed.
    - Can only add to (1) basic lists and (2) combo lists with at least one basic list as a sub-list.
- __ui lists replace__ - User chooses a list, an item from the list and enters text to replace the item with.  All instances of the item are replaced with the text.
    - Can only replace from (1) basic lists and (2) combo lists that contain basic lists.
- __ui lists remove__ - User choses a list and an item from the list.  The last instance of the item is removed from the list.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
***
- __ui lists addfolder__ - Asks the user to type a name for the new folder-list.
Asks the user to choose a folder.
Creates the folder-list, attached to the folder.
- __ui lists addcombo__ - User enters a name for he comb-list and chooses sub-lists.  A combo-list is made from the choices.
***
- __ui lists shortcutbatch__ - User chooses a list and enters a shortcut-text.  The shortcut-text is run for each item in the list, with "%1" being replaced by the list item.
- __ui lists fromfile lines__ - Use chooses a list (or enters a new list) and chooses a file.  The file is broken into lines, which are added to the list as items.

***

### mythicv2_ui.sfile
_(disabled by default, due to incompatibility with mythicgme.sfile)_

An extension to __mythicv2.sfile__ that provides graphical ui versions of shortcuts.

- __ui mythicv2 details__ - Asks the user whether to enable or disable 'details' mode.
Displays whether 'details' mode is enabled.
- __ui fate__ - Asks the user to choose the odds for this fate check.
Asks the user what answer is best for the pc(s).
Displays the answer to a yes/no question, possibly with a random event attached.
- __ui scene__ - Asks the user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.
- __ui disposition__ - Asks the user for the total descriptor modifier for an npc (+1 for descriptors that make the npc more active in the scene, -1 for descriptors that make the npc less active).
If the npc already has a disposition for this scene, select it.  If NOT, select "random" to choose one.
Rolls and shows a disposition for the npc for the current scene.
- __ui action__ - Asks the user for the npc's disposition modifier.
Rolls and shows an action for the npc to take.

***

### mythicgme_ui.sfile
An extension to __mythicgme.sfile__ that provides graphical ui versions of shortcuts.

- __ui fate__ - Asks the user to choose the odds for this fate check.
Displays the answer to a yes/no question, possibly with a random event attached.
- __ui scene__ - Asks the user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.

***

### une_ui.sfile
An extension to __une.sfile__ that provides graphical ui versions of shortcuts.

- __ui une__ - Asks the user to choose the scene's randomness (for picking the NPC's power).
Asks the user to choose the NPC's felings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's character (identity, power, motive) and the NPC's interaction for this scene (mood, bearing, focus).
***
- __ui une character__ - Asks the user to choose the scene's randomness (for picking the NPC's power).
Displays the NPC's character (identity, power, motive).
- __ui une interact__ - Asks the user to choose the NPC's feelings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's interaction for this scene (mood, bearing, focus).
***
- __ui une identity__ - Generates a 2-word description for an NPC.
- __ui une power__ - Asks the user to choose the scene's randomness.
Displays the NPC's power relative to the PC power(s).
- __ui une motive__ - Generates three 2-word descriptions for an NPC's motivations.
***
- __ui une mood__ - Asks the user to choose the NPC's felings towards the PC(s).
Displays the NPC's mood for this scene.
- __ui une bearing__ - Asks the user to choose the NPC's demeanor, or pick it randomly.
Displays a the NPC's bearing.
- __ui une focus__ - Generates an NPC's primary interest for this interaction.

***

### cards_pileviewer.sfile
An extension to __cards.sfile__ that adds an Obsidian panel type that lets you view card-piles.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=-m4n7d3aKC8) (runtime 9:01)

- __cards open viewer__ - Open a panel for viewing card-piles.

***

### cards_ui.sfile
An extension to __cards.sfile__ that provides graphical ui versions of shortcuts.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=-m4n7d3aKC8) (runtime 9:01)

- __ui cards reset__ - Clears all card-piles.
- __ui cards settings__ - User chooses card size and card back image.
***
- __ui cards pile__ - User enters a name, card-facing and show-moved flag for the new card-pile.  Card-pile is created from the choices.
- __ui cards remove__ - User choses a card-pile to remove, then removes that card-pile.
- __ui cards pilesettings__ - User chooses a card-pile, then choses its card-facing and show-moved flag.
***
- __ui cards fromfolder__ - User choses a folder to create new cards from, then choses a card-pile to put them into.  Cards are created and added to the chosen card-pile.
***
- __ui cards list__ - Lists all card-piles.
- __ui cards peek__ - User choses a card-pile, how many cards to peek and what side to peek from (top or bottom).  The peeked cards are printed to the note.
***
- __ui cards draw__ - User choses one card-pile to draw from, one to add to and how many cards to draw.  Cards are moved from the top of one card-pile to the top of the other.
- __ui cards pick__ - User choses one card-pile to pick from and one to add to, then the user picks the cards to move from the top of one to the top of the other.
***
- __ui cards shuffle__ - User choses a card-pile, and whether to rotate the cards while shuffling.  The card-pile is then shuffled.
- __ui cards unrotate__ - User choses a card-pile, then all the card-pile's cards are turned right-side-up.
- __ui cards reverse__ - User choses a card-pile, then the card-pile's order is reversed.
- __ui cards recall__ - User choses a card-pile, then the card-pile is recalled.  Recalling means finding all cards with the card-pile as their origin, then moving them to the card-pile.
- __ui cards reorigin__ - Asks the user to choose a card-pile, then the card-pile is re-origined..  Re-origining means setting the origin of all cards in a card-pile to that card-pile.
***
- __ui cards import__ - User chooses a card-pile and enters a data-string representation of a card-pile. 
 The data-string is turned into a card-pile and assigned to the card-pile the user chose.
- __ui cards export__ - User chooses a card-pile, then the card-pile is exported to a data-string, which is printed to the note.

***

### clips_ui.sfile
An extension to __clips.sfile__ that provides graphical ui versions of shortcuts.

- __ui clips reset__ - Removes all clips.
- __ui clips__ - Lists all stored clips.
- __ui clips set__ - User enters a clip name and a clip value.  The value is then assigned to the clip name.
- __ui clips get__ - User chooses a clip, which is then expanded into the note.
	- Alternative: __ui cg__
- __ui clips expansion__ - User enters a clip name.  The prior expansion's text is then assigned to the clip name.
- __ui clips remove__ - User chooses a clip, which is then removed.

***
