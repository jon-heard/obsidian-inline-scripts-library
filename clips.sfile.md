---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the clips.

This shortcut-file has supplementary shortcut files:
__clips_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.clips");
confirmObjectPath("_inlineScripts.clips.priorExpansion", "");
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.clips",
	function()
	{
		expand("clips reset");
	});
confirmObjectPath(
	"_inlineScripts.inlineScripts.listeners.onExpansion.clips",
	(expansionInfo) =>
	{
		_inlineScripts.clips.priorExpansion =
			expansionInfo.expansionText;
	});
```
__
Sets up this shortcut-file


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.state?.listeners?.onReset?.clips;
delete _inlineScripts.inlineScripts?.listeners?.onExpansion?.clips;
```
__
Shuts down this shortcut-file


__
```
^clips reset?$
```
__
```js
_inlineScripts.state.sessionState.clips = {};
return "All clips cleared.\n\n";
```
__
clips reset - Removes all clips.


__
```
^clips?$
```
__
```js
let clipNames = Object.keys(_inlineScripts.state.sessionState.clips);
return "Clips:\n" + (clipNames.length ? clipNames.join(", ") : "NONE") + "\n\n";
```
__
clips - Lists all stored clips.


__
```
^clips? set ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
$1 = $1.toLowerCase();
_inlineScripts.state.sessionState.clips[$1] = $2;
return "Clip __" + $1 + "__ set to:\n" + $2 + "\n\n";
```
__
clips set {name: name text} {value: text} - Creates / Sets a clip named {name} to the string {value}.


__
```
^(?:clips? get|cg) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
let text = _inlineScripts.state.sessionState.clips[$1];
return text || "";
```
__
clips get {name: name text} - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: name text}__


__
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
_inlineScripts.state.sessionState.clips[$1] = _inlineScripts.clips.priorExpansion;
return "Clip __" +
	$1 + "__ set to:\n***\n" + _inlineScripts.clips.priorExpansion + "\n***\n\n";
```
__
clips expansion {name: name text} - Creates a clip named {name} that stores the previous expansion.


__
```
^clips? remove ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
if (_inlineScripts.state.sessionState.clips[$1])
{
	delete _inlineScripts.state.sessionState.clips[$1];
	return "Clip __" + $1 + "__ removed.\n\n";
}
return "Clip __" + $1 + "__ not removed.  Does not exist.\n\n";
```
__
clips remove {name: name text} - Removes the clip named {name}.
