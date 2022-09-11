---
obsidianUIMode: preview
---

An extension to __mythicv2.sfile__ that provides graphical ui versions of shortcuts.


__
```
^sfile setup$
```
__
```js
if (_inlineScripts.inlineScripts.sfileIndices["mythicgme"])
{
	return true;
}
```
__
Disables mythicv2_ui if mythicgme is already registered.


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
	_inlineScripts.state.sessionState.mythicv2.showDetails ?
	"enabled" : "disabled";
const pick = popups.pick(
	"Set 'details' mode? (currently <b>" + stateString + "</b>)",
	[ "Disable", "Enable" ]);
if (pick === null) { return null; }

if (!!pick === (stateString === "enabled"))
{
	return "Details mode unchanged.  " +
		"Details are __" + stateString + "__.\n\n";
}

return expand("mythicv2 details " + (pick ? "y" : "n"));
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
const pick = popups.pick(
	"How was the previous scene?", [ "More controlled", "More chaotic" ], 1);
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
const pick = popups.pick("Enter the total descriptor modifier for the npc.\n\nConsider the npc's three descriptors in the current situation.\n+1 for descriptors that make the npc MORE active.\n-1 for descriptors that make the npc LESS active.\n0 for descriptors that don't affect npc activity.", choices, 3);
if (pick === null) { return null; }

const choices2 =
[
	"Random","2","3","4","5","6","7","8","9",
	"10","11","12","13","14","15","16","17",
	"18","19","20"
];
const pick2 = popups.pick("Enter the base disposition for the npc for this scene.\n\nIf the npc has a base dispositon for this scene, select it.\nIf the npc does NOT yet have a base disposition for this scene, select 'Random'.", choices2);
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
