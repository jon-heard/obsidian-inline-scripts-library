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
- [rpgtools.sfile](#rpgtoolssfile)
- [plugin_diceroller.sfile](#plugin_dicerollersfile)
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

***

### lists.sfile
Shortcuts for working with lists.

This shortcut-file has a tutorial video available:
[Using the "list" shortcut-file to manage lists](https://www.youtube.com/watch?v=xIYpnBKdYRg) (runtime 5:34)

- __lists reset__ - Clears all lists.
***
- __lists__ - Shows all lists and all items for each list.
- __lists list {list name: name text}__ - Shows all items in the list {list name}.
- __lists add {list name: name text} {item: text}__ - Adds {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.
- __lists pick {list name: name text}__ - Gets a random item from the list {list name}.
***
- __lists remove {list name: name text} {item: text}__ - Removes an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
- __lists replace {list name: name text} {item: text} {replacement: text}__ - Replaces all instances of {item} with {replacement}.  {item} can only have spaces if it's surrounded by quotes.
- __lists removelist {list name: name text}__ - Removes the entire list {list name}.
***
- __lists addfolder {list name: name text} {folder: path text}__ - Creates a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.
- __lists addcombo {list name: name text} {sub list 1: name text, default: ""} {sub list 2: name text, default: ""}...__ - Creates a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.
***
- __lists fromfile lines {list name: name text} {file: path text}__ - Takes the file {file} and breaks it up into individual lines.  Adds each of those lines to list {list name} as an item.

***

### notevars.sfile
This shortcut-file includes shortcuts to get and set note-variables.  Note-variables are variables set in the YAML frontmatter.  They can be used by many plugins, including DataView and TTRPG StatBlocks.

This shortcut file can be used by other shortcut-files to let them read and manipulate data in notes for many uses, including TTRPG character sheets.

This shortcut-file has a tutorial video available:
[Using the "notevars" shortcut-file to work with note variables](https://www.youtube.com/watch?v=EsV4WcMwhbA) (runtime 6:59)

- __notevars reset__ - Clears the isMarkdownRefreshed flag.
***
- __notevars isMarkdownRefreshed {state: y OR n, default: ""}__ - If {state} is given, assigns it to the notevars "isMarkdownRefreshed" flag.  Otherwise, displays the current "isMarkdownRefreshed" flag.
If isMarkdownRefreshed flag is set then a note's markdown is refreshed each time one of it's variables is set.
***
- __notevars get {note name: path text} {variable name: name text}__ - Expands to the value of variable {variable name} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars getArray {note name: path text} {array name: name text} {index: >=0}__ - Expands to the value of item {index} of array {array name} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars set {note name: path text} {variable name: name text} {value: text}__ - Sets the value of variable {variable name} to {value} in note {note name}.  If {note name} is "." then it represents the current note.
- __notevars setArray {note name: path text} {array name: name text} {value1: text},{value2: text},...__ - Sets the values of array {array name} to {value1}, {value2}, etc. in note {note name}.  If {note name} is "." then it represents the current note.

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

- __une {randomness: 1 TO 5 (order to chaos), default: 3 (standard)} {relationship TO pc: 1 to 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Runs "une character" and "une interact" together.  {randomness} is a value for "une character".  {relationship to pc} and {demeanor} are values for "une interact".
***
- __une character {randomness: 1 TO 5 (order to chaos), default: 3 (standard)}__ - Runs "identity", "power" and "motive" together.  {randomness} is a value for "power".
- __une interact {relationship to pc: 1 TO 7 (love to hate), default: 4 (neutral)} {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Runs "mood", "bearing" and "focus" together.  {relationship to pc} is a value for "mood".  {demeanor} is a value for "bearing".
***
- __une identity__ - Generates a 2-word description for a character.
- __une power {randomness: 1 TO 5 (order to chaos), default: 3 (standard)}__ - Generates a character's power level relative to pc's power level, based on {randomness}.
- __une motive__ - Generates three 2-word descriptions for a character's motivations.
***
- __une mood {relationship to pc: 1 TO 7 (love to hate), default: 4 (neutral)}__ - Generates a character's willingness to socialize for this interaction, based on {relationship to pc}.
- __une bearing {demeanor: 1 TO 8 (scheming to prejudiced), default: random}__ - Generates a character's attitude for this interaction, based on {demeanor}: a number defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced
- __une focus__ - Generates a character's primary interest for this interaction.

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
- __themes__ - Shows the list of chosen themes.
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
- __cards backimage {file name: path text}__ - Sets the image to use for the back-side of all cards to {file name}.
- __cards size {card size: >0}__ - Sets the width for all cards in pixels.  Card height follows.
- __cards__ - Lists all card-piles.
***
- __cards pile {pile id: name text}__ - Creates an empty pile {pile id}.
- __cards fromfolder {pile id: name text, default: table} {folder: path text} {facing: up OR down, default: up}__ - Creates cards based on images in {folder} and puts them into the {pile id} pile facing {facing}.
- __cards draw {source pile id: name text, default: table} {destination pile id: name text, default: table} {count: >0 OR "all", default: 1} {facing: up OR down, default: current} {pick: y OR n, default: n}__ - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.  If {facing} is given, all moved cards are set to {facing}.  If {pick} is "y", then the user chooses which cards to draw.
	- If a card has "allowDuplicate" turned on, then it is not moved, but copied.  The newly created card has its origin set to {destination pile id}.
- __cards show {pile id: name text, default: table}__ - Displays all cards in the {pile id} card-pile.
- __cards showreverse {pile id: name text, default: table}__ - Displays all cards in the {pile id} card-pile, but in reverse order.
***
- __cards properties {pile id: name text, default: table} {facing: up OR down, default: current} {allow rotated: y OR n, default: current} {allow duplicate: y OR n, default: current} {set origin: y OR n, default: n}__ - Changes the entered properties for all cards in the {pile id} card-pile.
	- facing - Set all cards to be face-up or face-down.
	- allow rotated -If allowed, each card has a 50/50 chance of being upside-down.
	- allow duplicate - If allowed, cards are copied, instead of moved when drawn.
	- set origin - If true, the origin of each card is set to THIS card-pile.  This is used in the "cards recall" shortcut.
***
- __cards shuffle {pile id: name text, default: table}__ - Randomizes the card order and rotation for the {pile id} card-pile.
- __cards reverse {pile id: name text, default: table}__ - Reverses the order of the cards in card-pile {pile id}.
- __cards flip {pile id: name text, default: table} {count: >0 OR "all", default: 1} {facing: up OR down, default: up} {pick: y OR n, default: n}__ - Flips {count} face-down cards to face-up in the {pile id} card-pile.  If {facing} is "down", then flipping is from face-up to face-down.  If {pick} is "y", then the user chooses which cards to flip.
- __cards recall {pile id: name text, default: table} {facing: up OR down, default: current}__ - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.  If {facing} is specified, all cards in {pile id} are then put to face {facing}.
- __cards destroy {pile id: name text, default: table}__ - Removes the {pile id} card-pile, including all cards within it.
***
- __cards import {pile id: name text, default: table} {data: text}__ - Makes a card-pile from the data string {data} and remembers it as {pile id}.
- __cards export {pile id: name text, default: table}__ - Expands to a data string containing all date for the {pile id} card-pile.

***

### rpgtools.sfile
Shortcuts to help in playing tabletop rpgs, either group or solo.

- __tbl twist__ - Rolls a plot twist from random tables.  <a href='https://jvhouse.xyz/plot-twist-situations'>Source</a>.

***

### plugin_diceroller.sfile
A shortcut to run commands through the Dice Roller plugin.

- __diceroller {command: text}__ - Runs {command} through the Dice Roller plugin and expands to the result.

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

- __ui lists list__ - Asks the user to choose from all lists.
Show all items in that list.
- __ui lists add__ - Asks the user to choose from all lists.
Asks the user to enter an item to add to that list.
Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.
- __ui lists pick__ - Asks the user to choose from all lists.
Gets a random item from that list.
- __ui lists remove__ - Asks the user to choose from all lists.
Asks the user to choose an item from that list.
Removes the last instance of that item from that list.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
- __ui lists replace__ - Asks the user to choose from all lists.
Asks the user to choose an item from that list.
Asks the user to enter a new value for that item.
Replaces all instances of the item within the list with the replacement.
- __ui lists removelist__ - Asks the user to choose from all lists.
Removes that list.
- __ui lists addfolder__ - Asks the user to type a name for the new folder-list.
Asks the user to choose a folder.
Creates the folder-list, attached to the folder.
- __ui lists addcombo__ - Asks the user to type a name for the new combo-list.
Asks the user to choose different lists.
Creates the combo-list, attached to the different lists.

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

- __fate__ - Ask user to choose the odds for this fate check.
Display the answer to a yes/no question, possibly with a random event attached.
- __scene__ - Ask user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.

***

### une_ui.sfile
An extension to __une.sfile__ that provides graphical ui versions of shortcuts.

- __ui une__ - Asks the user to choose the scene's randomness (for picking the npc's power).
Asks the user to choose the npc's felings towards the pc(s) (for the npc's mood).
Asks the user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Displays the npc's character (identity, power, motive) and the npc's interaction for this scene (mood, bearing, focus).
- __ui une character__ - Asks the user to choose the scene's randomness (for picking the npc's power).
Displays the npc's character (identity, power, motive).
- __ui une interact__ - Asks the user to choose the npc's felings towards the pc(s) (for the npc's mood).
Asks the user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Displays the npc's interaction for this scene (mood, bearing, focus).
- __ui une power__ - Asks the user to choose the scene's randomness.
Displays the npc's power relative to the pc(s) power.
- __ui une mood__ - Asks the user to choose the npc's felings towards the pc(s).
Displays the npc's mood for this scene.
- __ui une bearing__ - Asks the user to choose the npc's demeanor, or pick it randomly.
Displays a the npc's bearing.

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

- __ui cards size__ - Lets you pick the card size.
***
- __ui cards fromfolder__ - Asks the user to choose one of the non-empty folders.
Asks the user to enter a name to give the new card-pile.
Creates a card-pile based on images in the given folder and remembers it with the given name.
- __ui cards draw__ - Asks the user to choose a source card-pile.
Asks the user to enter a destination card-pile.
Asks the user if they want to pick the drawn cards.    If they want to, lets them pick.  Otherwise...
Asks the user if they want to flip, and which way.
Asks the user how many cards to draw.
Draws the cards.
- __ui cards show__ - Asks the user to choose a card-pile.
Adds images of the cards in the chosen card-pile to the note.
***
- __ui cards properties__ - Asks the user which card-pile to set properties for.
Shows a popup with all properties, each defaulting to "unchanged".  User can change the wanted properties.
When the user is finished, all chosen properties are changed on all cards in the card-pile.
***
- __ui cards shuffle__ - Asks the user to choose a card-pile.
Randomizes the card-pile's card order and rotation.
- __ui cards flip__ - Asks the user to choose a card-pile.
Asks the user if they want to pick the cards to flip.  If they want to, lets them pick.  Otherwise...
Asks the user which way they want to flip.
Asks the user how many cards to flip.
Flips the cards.
- __ui cards recall__ - Asks the user to enter a card-pile.
Moves all cards that have the chosen card-pile as their origin, from their current card-pile back into the chosen card-pile.
- __ui cards destroy__ - Asks the user to choose a card-pile.
Destroys the chosen card-pile and all cards within it.
***
- __ui cards import__ - Asks the user to enter a card-pile.
Asks the user to enter the data string to import.
Imports the data string and assigns the new card-pile  to the entered one.
- __ui cards export__ - Asks the user to choose a card-pile.
Expands to a data string containing all data for the chosen card-pile.

***

### clips_ui.sfile
An extension to __clips.sfile__ that provides graphical ui versions of shortcuts.

- __ui clips get__ - Asks the user to choose from list of clips.
Expands to the value of that clip.
	- Alternative: __cg__
- __ui clips remove__ - Asks the user to choose from list of clips.
Removes that clip.

***
