---
obsidianUIMode: preview
---

An extension to __une.sfile__ that provides graphical ui versions of shortcuts.


__
```
^une$
```
__
```js
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick0 = popups.pick("Choose the randomness of the scene", rLevels, 2);
if (pick0 === null) { return null; }

const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick1 =
	popups.pick("Choose the NPC's feelings towards the pc(s)", relationship, 3);
if (pick1 === null) { return null; }

const demeanors =
[
	"Pick randomly", "Scheming", "Insane",
	"Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick2 = popups.pick("Choose the pc's demeanor", demeanors);
if (pick2 === null) { return null; }

if (pick2 === 0)
{
	return expand("une " + (pick0 + 1) + " " + (pick1 + 1));
}
else
{
	return expand("une " + (pick0 + 1) + " " + (pick1 + 1) + " " + pick2);
}
```
__
une - Asks the user to choose the scene's randomness (for picking the npc's power).
Asks the user to choose the npc's felings towards the pc(s) (for the npc's mood).
Asks the user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Displays the npc's character (identity, power, motive) and the npc's interaction for this scene (mood, bearing, focus).


__
```
^une character$
```
__
```js
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick = popups.pick("Choose the randomness of the scene", rLevels, 2);
if (pick === null) { return null; }

return expand("une character " + (pick+1));
```
__
une character - Asks the user to choose the scene's randomness (for picking the npc's power).
Displays the npc's character (identity, power, motive).


__
```
^une interact$
```
__
```js
const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick1 =
	popups.pick("Choose the NPC's feelings towards the pc(s)", relationship, 3);
if (pick1 === null) { return null; }

const demeanors =
[
	"Pick randomly", "Scheming", "Insane",
	"Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick2 = popups.pick("Choose the pc's demeanor", demeanors);
if (pick2 === null) { return null; }

if (pick2 === 0)
{
	return expand("une interact " + (pick1 + 1) + " ");
}
else
{
	return expand("une interact " + (pick1 + 1) + " " + pick2);
}
```
__
une interact - Asks the user to choose the npc's felings towards the pc(s) (for the npc's mood).
Asks the user to choose the npc's demeanor, or pick it randomly (for the npc's bearing).
Displays the npc's interaction for this scene (mood, bearing, focus).


__
```
^une power$
```
__
```js
const rLevels =
[
	"Order", "Calm", "Standard",
	"Disarray", "Chaos"
];
const pick = popups.pick("Choose the randomness of the scene", rLevels, 2);
if (pick === null) { return null; }

return expand("une power " + (pick+1));
```
__
une power - Asks the user to choose the scene's randomness.
Displays the npc's power relative to the pc(s) power.


__
```
^une mood$
```
__
```js
const relationship =
[
	"Loved", "Friendly", "Peaceful", "Neutral",
	"Distrustful", "Hostile", "Hated"
];
const pick = popups.pick(
	"Choose the NPC's feelings towards the pc(s)", relationship, 3);
if (pick === null) { return null; }

return expand("une mood " + (pick+1));
```
__
une mood - Asks the user to choose the npc's felings towards the pc(s).
Displays the npc's mood for this scene.


__
```
^une bearing$
```
__
```js
const demeanors =
[
	"Pick randomly", "Scheming", "Insane",
	"Friendly", "Hostile", "Inquisitive",
	"Knowing", "Mysterious", "Prejudice"
];
const pick = popups.pick("Choose the pc's demeanor", demeanors);
if (pick === null) { return null; }

if (pick === 0)
{
	return expand("une bearing ");
}
else
{
	return expand("une bearing " + pick);
}
```
__
une bearing - Asks the user to choose the npc's demeanor, or pick it randomly.
Displays a the npc's bearing.
