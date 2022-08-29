---
obsidianUIMode: preview
---

An extension to __mythicv2__ shortcut-file that provides graphical ui versions of shortcuts.


__
__
```js
function clearDetailsIfUserTriggered()
{
	if (expansionInfo.isUserTriggered)
	{
		_inlineScripts.mythicv2.details = [];
	}
}
```
__
Some useful functions.


__
```
^mythicv2 details$
```
__
```js
clearDetailsIfUserTriggered();
const stateString =
	_inlineScripts.state.mythicv2.showDetails
	? "enabled" : "disabled";
const pick = popups.pick(
	"Set 'details' mode? (currently <b>" +
	stateString + "</b>)",
	[ "Disable", "Enable" ]);
if (pick === null) { return null; }

if (!!pick === (stateString === "enabled"))
{
	return "Details mode unchanged.  " +
	"Details are __" + stateString + "__.\n\n";
}

return expand(
	"mythicv2 details " +
	(pick ? "y" : "n"));
```
__
mythicv2 details - Ask user whether to enable or disable 'details' mode.
Display whether 'details' mode is enabled.


__
```
^fate$
```
__
```js
clearDetailsIfUserTriggered();
const odds =
[
	"Impossible", "No way", "Very unlikely",
	"Unlikely", "50 / 50", "likely",
	"Very likely", "Sure thing", "Has to be"
];
const pick = popups.pick("Choose the odds", odds, 4);
if (pick === null) { return null; }

const pick2 = popups.pick("What answer is best for the pc(s)?", [ "YES", "NO" ]);
if (pick2 === null) { return null; }

return expand( "fate " + (pick - 4) + " " + (pick2 ? "n" : "y") );
```
__
fate - Ask user to choose the odds for this fate check.
Ask user what answer is best for the pc(s).
Display the answer to a yes/no question, possibly with a random event attached.


__
```
^scene$
```
__
```js
clearDetailsIfUserTriggered();
const pick = popups.pick("How was the previous scene?", [ "More controlled", "More chaotic" ], 1);
if (pick === null) { return null; }

return expand("scene " + (pick * 2 - 1));
```
__
scene - Ask user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.


__
```
^disposition$
```
__
```js
clearDetailsIfUserTriggered();

const choices =
[
	"-3 (ALL descriptors make the npc LESS ACTIVE)", "-2", "-1",
	"0", "+1", "+2",
	"+3 (ALL descriptors make the npc MORE ACTIVE)"
];
const pick = popups.pick("Consider the npc's three descriptors in the current situation.\n\nDescriptors that make the npc MORE active are +1.\nDescriptors that make the npc LESS active are -1.\nDescriptors that don't affect npc activity level are 0.\n\nWhat is the total descriptor modifier for the npc?", choices, 3);
if (pick === null) { return null; }

const choices2 =
[
	"Random","2","3","4","5","6","7","8","9",
	"10","11","12","13","14","15","16","17",
	"18","19","20"
];
const pick2 = popups.pick("If the npc already has a dispositon for this scene, select the base disposition.\n\nIf the npc does NOT yet have a disposition for this scene, select 'Random' to choose one.", choices2);
if (pick2 === null) { return null; }

if (!pick2)
{
	return expand("disposition " + (pick - 3));
}
else
{
	return expand("disposition " + (pick - 3) + " " + choices2[pick2]);
}
```
__
disposition - Ask user for the total descriptor modifier for an npc (+1 for descriptors that make the npc more active in the scene, -1 for descriptors that make the npc less active).
If the npc already has a disposition for this scene, select it.  If NOT, select "random" to choose one.
Roll and shows a disposition for the npc for the current scene.


__
```
^action$
```
__
```js
clearDetailsIfUserTriggered();

const dispositionMods =
[
	"-2 (Passive)", "0 (Moderate)", "+2 (Active)",
	"+4 (Aggressive)"
];
const pick = popups.pick("Enter the npc's current disposition modifier.", dispositionMods, 1);
if (pick === null) { return null; }

return expand("action " + (pick * 2 - 2));
```
__
action - Ask user for the npc's disposition modifier.
Roll and display an action for the npc to take.
