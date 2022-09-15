---
obsidianUIMode: preview
---

An extension to __clips.sfile__ that provides graphical ui versions of shortcuts.


__
```
^(?:clips? get|cg)$
```
__
```js
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
if (!clipNames.length)
{
	return "No clips to get.";
}
const pick = popups.pick("Choose a clip to get", clipNames);
if (pick === null) { return null; }
return _inlineScripts.state.sessionState.clips[clipNames[pick]];
```
__
clips get - Asks the user to choose from list of clips.
Expands to the value of that clip.
	- Alternative: __cg__


__
```
^clips? remove$
```
__
```js
const clipNames = Object.keys(_inlineScripts.state.sessionState.clips).sort();
if (!clipNames.length)
{
	return "No clips to remove.\n\n";
}
const pick = await popups.pick("Choose a clip to remove", clipNames);
if (pick === null) { return null; }
delete _inlineScripts.state.sessionState.clips[clipNames[pick]];
return "Clip __" + clipNames[pick] + "__ removed.\n\n";
```
__
clips remove - Asks the user to choose from list of clips.
Removes that clip.
