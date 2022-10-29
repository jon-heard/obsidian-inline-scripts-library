---
obsidianUIMode: preview
---

An extension to __une.sfile__ that provides graphical ui versions of shortcuts.


__
```
^ui une$
```
__
```js
// Choose a randomness level
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick1 = popups.pick("Choose the randomness of the scene", rLevels, 2, 5);
if (pick1 === null) { return null; }

// Choose the NPC's relationship to the PC(s)
const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick2 =
	popups.pick("Choose the NPC's feelings towards the PC(s)", relationship, 3, 7);
if (pick2 === null) { return null; }

// Choose a demeanor
const demeanors =
[
	"<Random>", "Scheming", "Insane",
	"Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick3 = popups.pick("Choose the NPC's demeanor", demeanors, 0, 9);
if (pick3 === null) { return null; }

// Pass pick1 to the parameter.
// Pass pick2 to the parameter.
// Pass pick3 to the parameter, but if pick3 is "random" pass nothing.
return expand(
	"une " + (pick1 + 1) + " " + (pick2 + 1) + (pick3 ? " " + pick3 : ""));
```
__
ui une - Asks the user to choose the scene's randomness (for picking the NPC's power).
Asks the user to choose the NPC's felings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's character (identity, power, motive) and the NPC's interaction for this scene (mood, bearing, focus).
***


__
```
^ui une character$
```
__
```js
// Choose a randomness level
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick = popups.pick("Choose the randomness of the scene", rLevels, 2, 5);
if (pick === null) { return null; }

// Pass pick to the parameter.
return expand("une character " + (pick+1));
```
__
ui une character - Asks the user to choose the scene's randomness (for picking the NPC's power).
Displays the NPC's character (identity, power, motive).


__
```
^ui une interact$
```
__
```js
// Choose the NPC's relationship to the PC(s)
const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick1 =
	popups.pick("Choose the NPC's feelings towards the PC(s)", relationship, 3, 7);
if (pick1 === null) { return null; }

// Choose a demeanor
const demeanors =
[
	"<Random>", "Scheming", "Insane",
	"Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick2 = popups.pick("Choose the PC's demeanor", demeanors, 0, 9);
if (pick2 === null) { return null; }

// Pass pick1 to the parameter.
// Pass pick2 to the parameter, but if pick2 is "random" pass nothing.
return expand("une interact " + (pick1 + 1) + (pick2 ? " " + pick2 : ""));
```
__
ui une interact - Asks the user to choose the NPC's feelings towards the PC(s) (for the NPC's mood).
Asks the user to choose the NPC's demeanor, or pick it randomly (for the NPC's bearing).
Displays the NPC's interaction for this scene (mood, bearing, focus).
***


__
```
^ui une identity$
```
__
```js
// Just wraps "une identity"
return expand("une identity");
```
__
ui une identity - Generates a 2-word description for an NPC.


__
```
^ui une power$
```
__
```js
// Choose a randomless level
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick = popups.pick("Choose the randomness of the scene", rLevels, 2, 5);
if (pick === null) { return null; }

// Pass pick to the parameter
return expand("une power " + (pick + 1));
```
__
ui une power - Asks the user to choose the scene's randomness.
Displays the NPC's power relative to the PC power(s).


__
```
^ui une motive$
```
__
```js
// Just wraps "une motive"
return expand("une motive");
```
__
ui une motive - Generates three 2-word descriptions for an NPC's motivations.
***


__
```
^ui une mood$
```
__
```js
// Choose the NPC's relationship to the PC(s)
const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick = popups.pick(
	"Choose the NPC's feelings towards the PC(s)", relationship, 3, 7);
if (pick === null) { return null; }

// Pass pick to the parameter
return expand("une mood " + (pick + 1));
```
__
ui une mood - Asks the user to choose the NPC's felings towards the PC(s).
Displays the NPC's mood for this scene.


__
```
^ui une bearing$
```
__
```js
// Choose a demeanor
const demeanors =
[
	"<Random>", "Scheming", "Insane", "Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick = popups.pick("Choose the NPC's demeanor", demeanors, 0, 9);
if (pick === null) { return null; }

// Pass pick to the parameter, but if pick is "random" pass nothing.
return expand("une bearing" + (pick ? " " + pick : ""));
```
__
ui une bearing - Asks the user to choose the NPC's demeanor, or pick it randomly.
Displays a the NPC's bearing.


__
```
^ui une focus$
```
__
```js
// Just wraps "une focus"
return expand("une focus");
```
__
ui une focus - Generates an NPC's primary interest for this interaction.