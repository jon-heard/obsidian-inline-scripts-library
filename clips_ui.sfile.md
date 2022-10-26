---
obsidianUIMode: preview
---

An extension to __clips.sfile__ that provides graphical ui versions of shortcuts.


__
```
^ui clips? reset$
```
__
```js
return expand("clips reset");
```
__
ui clips reset - Removes all clips.


__
```
^ui clips?$
```
__
```js
return expand("clips");
```
__
ui clips - Lists all stored clips.


__
```
^ui clips? set$
```
__
```js
// Choose the clip name
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
let name = popups.input("Enter a clip name to set", "", clipNames);
if (!name) { return null; }
name = name.replaceAll(" ", "_");

// Choose the clip value
const value = popups.input("Enter a value for clip <b>" + name + "</b>");
if (value === null) { return null; }

return expand("clips set " + name + " " + value);
```
__
ui clips set - Asks user for a clip name and value, then assigns the value to the clip name.


__
```
^ui (?:clips? get|cg)$
```
__
```js
// Choose the clip from the list of clips
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
if (!clipNames.length)
{
	return "<No clips to get>";
}
const pick = popups.pick("Choose a clip to get", clipNames, 0, "adaptive");
if (pick === null) { return null; }

return expand("clips get " + clipNames[pick]);
```
__
ui clips get - Asks the user to choose from list of clips.
Expands to the value of that clip.
	- Alternative: __ui cg__


__
```
^ui clips? expansion$
```
__
```js
// Choose the clip name
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
let name = popups.input("Enter a clip name for the prior expansion", "", clipNames);
if (!name) { return null; }
name = name.replaceAll(" ", "_");

return expand("clips expansion " + name);
```
__
ui clips expansion - Asks the user to enter a clip name, then assigns the prior shortcut expansion  text to it.


__
```
^ui clips? remove$
```
__
```js
// Choose the clip name
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
if (!clipNames.length)
{
	return expFormat("Clip not removed.  No clips to remove.");
}
const pick = await popups.pick("Choose a clip to remove", clipNames, 0, "adaptive");
if (pick === null) { return null; }

return expand("clips remove " + clipNames[pick]);
```
__
ui clips remove - Asks the user to choose a clip, then removes that clip.
