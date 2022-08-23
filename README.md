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
- [lists_ui.sfile](#lists_uisfile)
- [lists.sfile](#listssfile)
- [mythicv2_ui.sfile](#mythicv2_uisfile)
- [mythicv2.sfile](#mythicv2sfile)
- [mythicgme_ui.sfile](#mythicgme_uisfile)
- [mythicgme.sfile](#mythicgmesfile)
- [une_ui.sfile](#une_uisfile)
- [une.sfile](#unesfile)
- [adventurecrafter.sfile](#adventurecraftersfile)
- [clips_ui.sfile](#clips_uisfile)
- [clips.sfile](#clipssfile)
- [rpgtools.sfile](#rpgtoolssfile)
- [arrows.sfile](#arrowssfile)
- [lipsum.sfile](#lipsumsfile)

***

### state.sfile
This shortcut-file includes shortcuts to manage a session state.  This allows one to save and restore the state of other shortcut-files across Obsidian sessions.

This shortcut file can be used by other shortcut-files to let them create data that can persist across sessions of Obsidian.

- __reset state__ - Clear all session state.
- __state get__ - Expands to a state-string - a string containing all data for the current session state.
- __state restore__ - Load the session state from the last "state get" shortcut expansion in the current note.
- __state set {state: text}__ - Loads the session state from {state}, a state-string created with the "state get" shortcut.

***

### lists_ui.sfile
An extension to __lists.sfile__ that provides graphical ui versions of shortcuts.

- __lists list__ - Asks user to choose from all lists.
Show all items in that list.
- __lists add__ - Asks user to choose from all lists.
Asks the user to enter an item to add to that list.
Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.
- __lists pick__ - Asks user to choose from all lists.
Gets a random item from that list.
- __lists remove__ - Asks user to choose from all lists.
Asks the user to choose an item from that list.
Removes the last instance of that item from that list.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
- __lists replace__ - Asks user to choose from all lists.
Asks the user to choose an item from that list.
Asks user to enter a new value for that item.
Replaces all instances of the item within the list with the replacement.
- __lists removelist__ - Asks user to choose from all lists.
Removes that list.
- __lists addfolder__ - Asks user to type a name for the new folder-list.
Asks user to choose a folder.
The folder-list is created, attached to the folder.
- __lists addcombo__ - Ask user to type a name for the new combo-list.
Asks user to choose different lists.
The combo-list is created, attached to the different lists.

***

### lists.sfile
Shortcuts for working with lists.

- __reset lists__ - Clear all lists.
- __lists__ - Show all list and all items for each list.
- __lists list {list name: name text}__ - Show all items in the list {list name}.
- __lists add {list name: name text} {item: text}__ - Add {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.
- __lists pick {list name: name text} {item index: >0, default: ""}__ - Get a random item from the list {list name}.  If {item index} is specified, then the item at that index is picked, instead of random.
- __lists remove {list name: name text} {item: text}__ - Remove an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.
- __lists replace {list name: name text} {item: non-space text} {replacement: text}__ - Replace all instances of {item} with {replacement}.  Any space characters in {item} should be replaced by a tab character to reserve the space character for the end of {item}.
- __lists removelist {list name: name text}__ - Remove the entire list {list name}.
- __lists addfolder {list name: name text} {folder: text}__ - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.
- __lists addcombo {list name: name text} {sub list 1: name text, default: ""} {sub list 2: name text, default: ""}...__ - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.

***

### mythicv2_ui.sfile
_(disabled by default, due to incompatibility with mythicgme.sfile)_

An extension to __mythicv2.sfile__ that provides graphical ui versions of shortcuts.

- __mythicv2 details__ - Ask user whether to enable or disable 'details' mode.
Display whether 'details' mode is enabled.
- __fate__ - Ask user to choose the odds for this fate check.
Ask user what answer is best for the pc(s).
Display the answer to a yes/no question, possibly with a random event attached.
- __scene__ - Ask user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.
- __disposition__ - Ask user for the total descriptor modifier for an npc (+1 for descriptors that make the npc more active in the scene, -1 for descriptors that make the npc less active).
If the npc already has a disposition for this scene, select it.  If NOT, select "random" to choose one.
Roll and shows a disposition for the npc for the current scene.
- __action__ - Ask user for the npc's disposition modifier.
Roll and display an action for the npc to take.

***

### mythicv2.sfile
_(disabled by default, due to incompatibility with mythicgme.sfile)_

Shortcuts for Mythic Variations 2.  Mythic GME, along with it's "Variations 2" supplement, is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic Variations 2 at [wordmill games](http://wordmillgames.com/mythic-variations-2.html).

Incompatible with __mythicgme.sfile__.  If __mythicgme.sfile__ comes before __mythicv2.sfile__ in the shortcut-list, then __mythicv2.sfile__ will be disabled.

- __reset mythicv2__ - Reset mythic state to defaults and displays scene heading.
- __mythicv2 details {state: y or n, default: ""}__ - If {state} is given, assigns it to the mythicv2 "details" mode.  Otherwise, displays the current "details" mode.
- __fate {odds: -4 to 4 ("impossible" to "has to be"), default: 0 ("50/50")} {wanted: y or n, default: y}__ - Make a fate check based on {odds}.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
- __fate {odds: text, default: "50/50"} {wanted: y or n, default: y}__ - Make a fate check based on {odds}: a specific text such as "impossible", "sure thing", etc.
This fate check is also based on {wanted}: the desired outcome.  Used for the direction of the chaos modifier.
- __detail__ - Make a detail check.
- __event__ - Make an event check.
- __meaning action__ - Roll on the action meaning tables.
	- Alternative: __meaning__
- __meaning description__ - Roll on the description meaning tables.
- __scene__ - Show the current scene.
- __scene {chaos adjust: -1 or 1}__ - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.
- __chaos__ - Show the current chaos value.
- __chaos--__ - Decrease the chaos value by 1 (minimum of 3).
- __chaos++__ - Increase the chaos value by 1 (maximum of 6).
- __chaos={value: 3 to 6}__ - Set the chaos value to {value}, an integer from 3 to 6.
- __descriptors__ - Generates a personality and activity descriptor for an NPC.
- __disposition {descriptor count: -3 to 3}__ - Rolls for an NPC's disposition, modified by {descriptor count}, which represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __disposition {descriptorCount: -3 to 3} {base: 2 to 20}__ - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.  {descriptor count} represents the total of the NPC's activated descriptors.
    - Example: 2 positively activated descriptors and 1 negatively activated descriptor would make a {descriptor count} of __1+1+(-1) = 1__.
- __action {dispositionAdjust: -2 or 0 or 2 or 4}__ - Makes an NPC behavior check, modified by {dispositionAdjust}: the modifier of the NPC's disposition.

***

### mythicgme_ui.sfile
An extension to __mythicgme.sfile__ that provides graphical ui versions of shortcuts.

- __fate__ - Ask user to choose the odds for this fate check.
Display the answer to a yes/no question, possibly with a random event attached.
- __scene__ - Ask user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.

***

### mythicgme.sfile
Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

Incompatible with __mythicv2.sfile__.  If __mythicv2.sfile__ comes before __mythicgme.sfile__ in the shortcut-list, then __mythicgme.sfile__ will be disabled.

- __reset mythicgme__ - Reset mythic state to defaults and displays scene heading.
- __fate {odds: -4 to 6 ("impossible" to "has to be"), default: 0 ("50/50")}__ - Make a fate check based on {odds}.
- __fate {odds: text, default: "50/50"}__ - Make a fate check based on {odds}: a specific text, such as "impossible", "sure thing", etc.
- __scene__ - Show the current scene.
- __scene {chaosAdjust: -1 or 1}__ - Shift the chaos value by {chaosAdjust}, then increment the current scene and run a scene check.
- __event__ - Make an event check.
- __meaning__ - Roll on the meaning tables.
- __chaos__ - Show the current chaos value.
- __chaos--__ - Decrease the chaos value by 1 (minimum of 1).
- __chaos++__ - Increase the chaos value by 1 (maximum of 9).
- __chaos={value: 1 to 9}__ - Set the chaos value to {value}.

***

### une_ui.sfile
An extension to __une.sfile__ that provides graphical ui versions of shortcuts.

- __une__ - Ask user to choose the scene's randomness (for picking the npc's power).
Ask user to choose the npc's felings towards the pc(s) (for the npc's mood).
Ask user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Display the character of the npc (identity, power, motive) and the interaction with the npc for this scene (mood, bearing, focus).
- __une character__ - Ask user to choose the scene's randomness (for picking the npc's power).
Display the character of the npc (identity, power, motive).
- __une interact__ - Ask user to choose the npc's felings towards the pc(s) (for the npc's mood).
Ask user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Display the interaction with the npc for this scene (mood, bearing, focus).
- __une power__ - Ask user to choose the scene's randomness.
Display the power of the npc relative to the pc(s) power.
- __une mood__ - Ask user to choose the npc's felings towards the pc(s).
Display the mood of the npc for this scene.
- __une bearing__ - Ask user to choose the npc's demeanor, or pick it randomly.
Display a bearing for the npc.

***

### une.sfile
Shortcuts for UNE: The Universal NPC Emulator.  UNE is an excellent character generation system for tabletop role playing and general storytelling.  It was designed by Zach Best. 
 You can find more info about UNE at its [drivethrurpg page](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

- __une {randomness: 1 to 5 (order to chaos), default: 3 (standard)} {relationship to pc: 1 to 7 (love to hate), default: 4 (neutral)} {demeanor: 1 to 8 (scheming to prejudiced), default: random}__ - Runs "une character" and "une interact" together.  {randomness} is a value for "une character".  {relationship to pc} and {demeanor} are values for "une interact".
- __une character {randomness: 1 to 5 (order to chaos), default: 3 (standard)}__ - Runs "identity", "power" and "motive" together.  {randomness} is a value for "power".
- __une interact {relationship to pc: 1 to 7 (love to hate), default: 4 (neutral)} {demeanor: 1 to 8 (scheming to prejudiced), default: random}__ - Runs "mood", "bearing" and "focus" together.  {relationship to pc} is a value for "mood".  {demeanor} is a value for "bearing".
- __une identity__ - Generates a 2-word description for a character.
- __une power {randomness: 1 to 5 (order to chaos), default: 3 (standard)}__ - Generates a character's power level relative to pc's power level, based on {randomness}.
- __une motive__ - Generates three 2-word descriptions for a character's motivations.
- __une mood {relationship to pc: 1 to 7 (love to hate), default: 4 (neutral)}__ - Generates a character's willingness to socialize for this interaction, based on {relationship to pc}.
- __une bearing {demeanor: 1 to 8 (scheming to prejudiced), default: random}__ - Generate a character's attitude for this interaction, based on {demeanor}: a number defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced
- __une focus__ - Generate a character's primary interest for this interaction.

***

### adventurecrafter.sfile
Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  It was designed by Tana Pigeon.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

- __reset adventurecrafter__ - Reset adventurecrafter state to defaults.
- __turning point__ - Get a list of five plot points to represent a major milestone in a plotline.
- __plot point__ - Get a single plot point, generated from the plot points table.
- __themes pick__ - Pick a random theme from the chosen themes, as per the Adventure Crafter rules.
- __themes__ - Show the list of chosen themes.
- __themes add__ - Choose a theme for the next unchosen theme-slot.
- __themes roll__ - Pick a random theme for the next unchosen theme-slot.
- __themes fill__ - Pick random themes for the remaining unchosen theme-slots.
- __themes clear__ - Clear all chosen themes.
- __ac chars gen__ - Generate a new character description, as per the Adventure Crafter rules.
- __ac chars pick__ - Pick a random character, as per the Adventure Crafter rules.
- __ac chars__ - List all character entries.
- __ac chars add {character: text}__ - Add {character} to the list of character entries.
- __ac chars dupe__ - Choose a character from the characters list and add a new entry for it.
- __ac chars reduce__ - Choose a character from the characters list and remove one entry for it.
- __ac chars rename__ - Choose a character from the characters list and change it's text to something else.
- __ac plots pick__ - Pick a random plotline, as per the Adventure Crafter rules.
- __ac plots__ - List all plotline entries.
- __ac plots add {plotline: text}__ - Add {plotline} to the list of plotline entries.
- __ac plots dupe__ - Choose a plotline from the plotlines list and add a new entry for it.
- __ac plots reduce__ - Choose a plotline from the plotlines list and remove one entry for it.
- __ac plots rename__ - Choose a plotline from the plotlines list and change it's text to something else.

***

### clips_ui.sfile
An extension to __clips.sfile__ that provides graphical ui versions of shortcuts.

- __reset clips__ - Remove all clips.
- __clips__ - Lists all stored clips.
- __clips set {name: name text} {value: text}__ - Creates / Sets a clip named {name} to the string {value}.
- __clips get {name: name text}__ - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: name text}__
- __clips expansion {name: name text}__ - Creates a clip named {name} that stores the previous expansion.
- __clips remove {name: name text}__ - Removes the clip named {name}.

***

### clips.sfile
Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

- __reset clips__ - Remove all clips.
- __clips__ - Lists all stored clips.
- __clips set {name: name text} {value: text}__ - Creates / Sets a clip named {name} to the string {value}.
- __clips get {name: name text}__ - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: name text}__
- __clips expansion {name: name text}__ - Creates a clip named {name} that stores the previous expansion.
- __clips remove {name: name text}__ - Removes the clip named {name}.

***

### rpgtools.sfile
Shortcuts to help in playing tabletop rpgs, either group or solo.

- __tbl twist__ - Random table: a plot twist.  <a href='https://jvhouse.xyz/plot-twist-situations'>Source</a>.

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

- __lipsum {paragraph count: >0, default: 1}__ - Generates a lorem ipsum text with {paragraph count} paragraphs.  If {paragraph count} is omitted, it defaults to 1.

***
