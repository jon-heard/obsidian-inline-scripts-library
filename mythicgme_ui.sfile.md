---
obsidianUIMode: preview
---

An extension to __mythicgme.sfile__ that provides graphical ui versions of shortcuts.


__
```
^sfile setup$
```
__
```js
if (_inlineScripts.inlineScripts.sfileIndices["mythicv2"])
{
	return true;
}
```
__
Disables mythicgme_ui if mythicv2 is already registered.


__
```
^fate$
```
__
```js
const odds =
[
	"Impossible", "No way", "Very unlikely",
	"Unlikely", "50 / 50", "Somewhat likely",
	"Likely", "Very likely", "Near sure thing",
	"Sure thing", "Has to be"
];
const pick = popups.pick("Choose the odds", odds, 4);
if (pick === null) { return null; }

return expand("fate " + (pick - 4));
```
__
fate - Asks the user to choose the odds for this fate check.
Displays the answer to a yes/no question, possibly with a random event attached.


__
```
^scene$
```
__
```js
const pick =
	popups.pick("How was the previous scene?",
	[ "More controlled", "More chaotic" ], 1);
if (pick === null) { return null; }

return expand("scene " + (pick * 2 - 1));
```
__
scene - Asks the user to choose whether chaos increased or decreased last scene.
Starts a new scene with the chosen chaos value adjustment.
