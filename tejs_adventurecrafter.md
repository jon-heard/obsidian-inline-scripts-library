---
obsidianUIMode: preview
---

Shortcuts for Adventure Crafter.  Adventure Crafter is a system to create coherent story beats through randomization and tables.  You can find more info about Adventure Crafter at [wordmill games](http://wordmillgames.com/the-adventure-crafter.html).

Uses __tejs_adventurecrafter_data__ shortcut-file (required).
This requires that __tejs_lists__ comes before __tejs_adventurecrafter__ in the shortcut-list.
This shortcut-file contains the major tables used by this system.  It is not publicly available for copyright reasons.  Information on how to get it is available [here](https://github.com/jon-heard/obsidian-text-expander-js_shortcutFileLibrary/blob/shortcutAboutString/docs/adventurecrafter_dataSetup.txt).

Uses __tejs_lists__ shortcut-file (required).
This requires that __tejs_lists__ comes before __tejs_adventurecrafter__ in the shortcut-list.
Adds and uses lists for plotlines and characters.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the theme order and final theme switch state.

Uses __tejs_mythicv2__ shortcut file (optional).
The plotlines and characters lists are created to reference the __tejs_mythicv2__ lists.
This requires that __tejs_mythicv2__ comes before __tejs_adventurecrafter__ in the shortcut-list.


~~
```
^tejs setup$
```
~~
```js
window._tejs ||= {};

if (!window._tejs.adventurecrafter?.themes ||
	!window._tejs.adventurecrafter?.character_identities ||
	!window._tejs.adventurecrafter?.character_descriptors ||
	!window._tejs.adventurecrafter?.character_specialTraits ||
	!window._tejs.adventurecrafter?.plot_meta ||
	!window._tejs.adventurecrafter?.plot)
{
	console.log("Adventurecrafter table data not found.\nAdventurecrafter shortcuts disabled.");
	window._tejs.adventurecrafter ||= {};
	window._tejs.adventurecrafter.disabled = true;
}
else
{
	delete window._tejs.adventurecrafter.disabled;
}


window._tejs.state ||= {};
window._tejs.state.adventurecrafter ||= {};
window._tejs.state.adventurecrafter.themes ||= [];
window._tejs.state.lists ||= {};

if (!window._tejs.state.lists.plotlines)
{
	if (!window._tejs.state.lists.threads)
	{
		window._tejs.state.lists.plotlines ||= { type: "basic", content: [] };
		delete window._tejs.state.lists.plotline_dupes;
	}
	else
	{
		window._tejs.state.lists.plotline_dupes ||= { type: "basic", content: [] };
		window._tejs.state.lists.plotlines ||=
			{ type: "combo", content: ["threads", "plotline_dupes"] };
	}
}

if (!window._tejs.state.lists.characters)
{
	if (!window._tejs.state.lists.pcs || !window._tejs.state.lists.npcs)
	{
		window._tejs.state.lists.characters ||= { type: "basic", content: [] };
		delete window._tejs.state.lists.character_dupes;
	}
	else
	{
		window._tejs.state.lists.character_dupes ||= { type: "basic", content: [] };
		window._tejs.state.lists.characters ||=
			{ type: "combo", content: ["pcs", "npcs", "character_dupes"] };
	}
}

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.adventurecrafter ||= expand =>
{
	expand("reset adventurecrafter");
};

```
~~
Sets up a state variable for adventurecrafter.  Sets up callback for state "reset" event to reset itself.


~~
```
^tejs shutdown$
```
~~
```js
delete window._tejs.listeners?.state?.onReset?.adventurecrafter;
```
~~
Unregisters event callbacks.


~~
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
function isAdventurecrafterDisabled()
{
	if (window._tejs.adventurecrafter.disabled)
	{
		return "ADVENTURECRAFTER SHORTCUTS DISABLED - missing table data.\nIf __tejs_adventurecrafter_data__ exists, make sure it is listed _before_ __tejs_adventurecrafter__ in the shortcut-file list.\n\n";
	}
	return null;
}
```
~~
Some useful functions


~~
```
^reset adventurecrafter$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.adventurecrafter ||= {};
window._tejs.state.adventurecrafter.themes = [];
window._tejs.state.lists ||= {};

if (!window._tejs.state.lists.threads)
{
	window._tejs.state.lists.plotlines = { type: "basic", content: [] };
	delete window._tejs.state.lists.plotline_dupes;
}
else
{
	window._tejs.state.lists.plotline_dupes = { type: "basic", content: [] };
	window._tejs.state.lists.plotlines =
		{ type: "combo", content: ["threads", "plotline_dupes"] };
}

if (!window._tejs.state.lists.pcs || !window._tejs.state.lists.npcs)
{
	window._tejs.state.lists.characters = { type: "basic", content: [] };
	delete window._tejs.state.lists.character_dupes;
}
else
{
	window._tejs.state.lists.character_dupes = { type: "basic", content: [] };
	window._tejs.state.lists.characters =
		{ type: "combo", content: ["pcs", "npcs", "character_dupes"] };
}

return "Adventure crafter reset.";
```
~~
reset adventurecrafter - Reset adventurecrafter state to defaults.
***


~~
```
^turning point$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

if (expand("themes pick").length < 4)
{
	return "Turning point not generated.  Not all theme slots filled.\n\n";
}
let result = "Turning point:\n";
let nones = [];
for (let i = 0; i < 5; i++)
{
	const plotPoint = expand("plot point");
	if (plotPoint.length < 4)
	{
		result += "" + plotPoint[1] + "\n";
	}
	else
	{
		if (nones.length < 3)
		{
			nones.push(plotPoint[1]);
		}
		else
		{
			i--;
		}
	}
}
for (const none of nones)
{
	result += "" + none + "\n";
}
return result + "\n";
```
~~
turning point - Get a list of five plot points to represent a major milestone in a plotline.


~~
```
^plot point$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

const theme = expand("themes pick");
if (theme.length < 4)
{
	return "Plot point not generated.  Not all theme slots filled.\n\n";
}
let result = [ "Plot point:\n" ];
const plotPoint = aPickWeight(window._tejs.adventurecrafter.plot, theme[3]);
if (!plotPoint[7])
{
	result.push("    " + plotPoint[0] + "    _(" + theme[1] + ")_\n        " + plotPoint[6]);
}
else
{
	if (plotPoint[7] === 1)
	{
		result.push("    " + plotPoint[0]);
		result.push(""); // Blank entry signifies this as a "none" plotpoint
	}
	else if (plotPoint[7] === 2)
	{
		const metaPoint = aPickWeight(window._tejs.adventurecrafter.plot_meta);
		result.push("    " + metaPoint[0] + "    _(META)_\n        " + metaPoint[2]);
	}
}
result.push("\n\n");
return result;
```
~~
plot point - Get a single plot point, generated from the plot points table.
***


~~
```
^themes? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

if (window._tejs.state.adventurecrafter.themes.length < 5)
{
	return [ "Theme not picked.  Not all theme slots filled.\n\n" ];
}
let pick = roll(10);
if      (pick <  5) { pick = 0; }
else if (pick <  8) { pick = 1; }
else if (pick < 10) { pick = 2; }
else
{
	pick = (window._tejs.state.priorPickWas3rd ? 4 : 3);
	window._tejs.state.priorPickWas3rd = !window._tejs.state.priorPickWas3rd;
}
pick = window._tejs.state.adventurecrafter.themes[pick];
return [ "Theme __", window._tejs.adventurecrafter.themes[pick], "__ picked _(", (pick+1), ")_.\n\n" ];
```
~~
themes pick - Pick a weighted random theme, as per the Adventure Crafter rules.


~~
```
^themes?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

let result = "Current theme set:\n";
for (let i = 0; i < 5; i++)
{
	result += "    " + (i+1) + ". " +
		(window._tejs.adventurecrafter.themes[
			window._tejs.state.adventurecrafter.themes[i] ?? 9] || "") +
		"\n"
}
return result + "\n";
```
~~
themes - Show the ordered list of themes.


~~
```
^themes? fill$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

let result = "";
// Do-whlie causes at least 1 roll.  If already filled, this prodces an error msg.
do
{
	result += expand("themes roll")[0] + "\n";
}
while (window._tejs.state.adventurecrafter.themes.length < 5);
return result + "\n";
```
~~
themes fill - Fills the remaining open theme slots with random themes.


~~
```
^themes? roll$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

const themes = window._tejs.state.adventurecrafter.themes;
let r = roll(5);
// Only seek unused theme if theme slots aren't already filled
while (themes.length < 5 && window._tejs.state.adventurecrafter.themes.contains(r-1))
{
	r = roll(5);
}
return expand("themes add " + r);
```
~~
themes roll - Fills the next open theme slot with a random theme.


~~
```
^themes? add((?: [1-5])?)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

$1 = Number($1);
if (!$1)
{
	return "" +
		"Enter \"__themes add__\" again with one of these indices:\n" +
		"    1. Action\n    2. Tension\n    3. Mystery\n" +
		"    4. Social\n    5. Personal\n\n";
}
else
{
	let themes = window._tejs.state.adventurecrafter.themes;
	if (themes.length >= 5)
	{
		return [ "No theme added.  All five theme slots are already filled.", "\n\n" ];
	}
	themes.push($1-1);
	return [ "Theme slot __" + themes.length + "__ set to __" + window._tejs.adventurecrafter.themes[$1-1] + "__", "\n\n" ];
}
```
~~
themes add {theme id} - If {theme id} (an optional number from 1 to 5) is NOT included, this shortcut shows the options for {theme id}.  If {theme id} IS included, this shortcut fills the next open theme slot with the theme of {theme id}.  {theme id} can be one of these options: 1 (Action), 2 (Tension), 3 (Mystery), 4 (Social), 5 (Personal).


~~
```
^themes? clear$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

window._tejs.state.adventurecrafter.themes = [];
return "All theme slots cleared.\n\n";
```
~~
themes clear - Clear all theme slots.
***


~~
```
^ac chars? gen$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

let special = aPickWeight(window._tejs.adventurecrafter?.character_specialTraits, 1);
special = "    Special trait - " + special[0] + "\n        " + special[2] + "\n";

let identity = aPickWeight(window._tejs.adventurecrafter?.character_identities, 1);
if (identity[2])
{
	identity = [];
	for (let i = 0; i < 2; i++)
	{
		let subIdentity = [];
		do
		{
			subIdentity =
				aPickWeight(window._tejs.adventurecrafter?.character_identities, 1);
		}
		while (subIdentity[2]);
		identity.push(subIdentity[0]);
	}
}
else
{
	identity = [ identity[0] ];
}
identity = "    Identity - " + identity.join(", ") + "\n";

let descriptor = aPickWeight(window._tejs.adventurecrafter?.character_descriptors, 1);
if (descriptor[2])
{
	descriptor = [];
	for (let i = 0; i < 2; i++)
	{
		let subDescriptor = [];
		do
		{
			subDescriptor =
				aPickWeight(window._tejs.adventurecrafter?.character_descriptors, 1);
		}
		while (subDescriptor[2]);
		descriptor.push(subDescriptor[0]);
	}
}
else
{
	descriptor = [ descriptor[0] ];
}
descriptor = "    Descriptor - " + descriptor.join(", ") + "\n";

return "Character:\n" + special + identity + descriptor + "\n";
```
~~
ac chars gen - Generate a new character description, as per the Adventure Crafter rules.
***


~~
~~
```js
function getFormattedList(listName, hideCount, prefixType /* 0-index,1-none,2-bullets*/)
{
	let formattedEntries = {};
	const rawEntries = expand("lists listraw " + listName);
	for (let i = 0; i < rawEntries.length; i++)
	{
		if (formattedEntries[rawEntries[i]])
		{
			formattedEntries[rawEntries[i]].count++;
		}
		else
		{
			formattedEntries[rawEntries[i]] = { index: (i+1), count: 1 };
		}
	}
	let result = [];
	let isEmpty = true;
	for (const entry of rawEntries)
	{
		if (formattedEntries[entry])
		{
			const e = formattedEntries[entry];
			result.push(
				(prefixType===1 ? "" : prefixType===2 ? "- " : (e.index + ". ")) +
				entry +
				((hideCount || e.count === 1) ? "" : " _(x " + e.count + ")_")
			);
			delete formattedEntries[entry];
			isEmpty = false;
		}
	}
	if (isEmpty)
	{
		result.push("NONE");
	}
	return result;
}
```
~~
Used by a lot of "ac chars" and "ac plots" shortcuts.


~~
```
^ac chars? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

const NEW_CHAR_MSG = "Create a new character";
const r = roll(25);
let result = expand("lists pick characters " + r)[1];
if (result)
{
	result = [ "Character __", result, "__ picked." ];
}
if (!result)
{
	if (r < 13)
	{
		result = r%4 ? [ NEW_CHAR_MSG ] : null;
	}
	else
	{
		result = (r-1)%4 ? null : [ NEW_CHAR_MSG ];
	}
}
if (!result)
{
	let list = getFormattedList("characters", true, 1);
	if (list[0] !== "NONE" || list.length > 1)
	{
		result = [ "Pick the most logical character:\n    " + list.join("\n    ") ];
	}
	else
	{
		result = [ NEW_CHAR_MSG ];
	}
}
result.push("\n\n");
return result;
```
~~
ac chars pick - Pick a random char, as per the Adventure Crafter rules.


~~
```
^ac chars?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

return "Characters:\n    " + getFormattedList("characters", false, 1).join("\n    ") + "\n\n";
```
~~
ac chars - List the character entries.


~~
```
^ac chars? add (.*)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

return expand("lists add characters " + $1);
```
~~
ac chars add {character} - Add the given {character} (required text) to the list of character entries.


~~
```
^ac chars? dupe((?: [1-9][0-9]*)?)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

$1 = Number($1);
if (!$1)
{
	return "" +
		"Enter \"__ac chars dupe__\" again with one of these indices:\n    " +
		getFormattedList("characters").join("\n    ") + "\n\n";
}
else
{
	const list = expand("lists listraw characters");
	if ($1 > list.length)
	{
		return "Character not duplicated.  Invalid index given (" + $1 + ").\n\n";
	}
	return expand("lists add characters " + list[$1-1]);
}
```
~~
ac plots dupe {character index} - If {character index} (an optional, positive integer) is NOT included, this shortcut shows the options for {character index}.  If {character index} IS included, this shortcut duplicates the character that is indexed by {character index}.


~~
```
^ac chars? remove((?: [1-9][0-9]*)?)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

$1 = Number($1);
if (!$1)
{
	return "" +
		"Enter \"__ac chars remove__\" again with one of these indices:\n    " +
		getFormattedList("characters").join("\n    ") + "\n\n";
}
else
{
	const list = expand("lists listraw characters");
	if ($1 > list.length)
	{
		return "Character not removed.  Invalid index given (" + $1 + ").\n\n";
	}
	return expand("lists remove characters " + list[$1-1]);
}
```
~~
ac chars remove {character index} - If {character index} (an optional, positive integer) is NOT included, this shortcut shows the options for {character index}.  If {character index} IS included, this shortcut removes one entry of the character that is indexed by {character index}.
***


~~
```
^ac plots? pick$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

const r = roll(25);
let result = expand("lists pick plotlines " + r)[1];
if (result)
{
	result = [ "Plotline __", result, "__ picked." ];
}
if (!result)
{
	result = (r+2)%4 ? null : [ "Create a new plotline" ];
}
if (!result)
{
	let list = getFormattedList("plotlines", true, 1);
	result = [ "Pick the most logical plotline:\n    " + list.join("\n    ") ];

}
result.push("\n\n");
return result;
```
~~
ac plots pick - Pick a random plotline, as per the Adventure Crafter rules.


~~
```
^ac plots?$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

return "Plotlines:\n    " + getFormattedList("plotlines", false, 1).join("\n    ") + "\n\n";
```
~~
ac plots - List the plotline entries.


~~
```
^ac plots? add (.*)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

return expand("lists add plotlines " + $1);
```
~~
ac plots add {plotline} - Add the given {plotline} (required text) to the list of plotline entries.


~~
```
^ac plots? dupe((?: [1-9][0-9]*)?)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

$1 = Number($1);
if (!$1)
{
	return "" +
		"Enter \"__ac plots dupe__\" again with one of these indices:\n    " +
		getFormattedList("plotlines").join("\n    ") + "\n\n";
}
else
{
	const list = expand("lists listraw plotlines");
	if ($1 > list.length)
	{
		return "Plotline not duplicated.  Invalid index given (" + $1 + ").\n\n";
	}
	return expand("lists add plotlines " + list[$1-1]);
}
```
~~
ac plots dupe {plotline index} - If {plotline index} (an optional, positive integer) is NOT included, this shortcut shows the options for {plotline index}.  If {plotline index} IS included, this shortcut duplicates the plotline that is indexed by {plotline index}.


~~
```
^ac plots? remove((?: [1-9][0-9]*)?)$
```
~~
```js
if (isDisabled = isAdventurecrafterDisabled()) { return isDisabled; }

$1 = Number($1);
if (!$1)
{
	return "" +
		"Enter \"__ac plots remove__\" again with one of these indices:\n    " +
		getFormattedList("plotlines").join("\n    ") + "\n\n";
}
else
{
	const list = expand("lists listraw plotlines");
	if ($1 > list.length)
	{
		return "Plotline not removed.  Invalid index given (" + $1 + ").\n\n";
	}
	return expand("lists remove plotlines " + list[$1-1]);
}
```
~~
ac plots remove {plotline index} - If {plotline index} (an optional, positive integer) is NOT included, this shortcut shows the options for {plotline index}.  If {plotline index} IS included, this shortcut removes one entry of the plotline that is indexed by {plotline index}.
