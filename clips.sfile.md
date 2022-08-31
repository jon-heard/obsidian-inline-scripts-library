---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the clips.


__
```
^sfile setup$
```
__
```js
function confirmObjPath(path, leaf)
{
    path = path.split(".");
    let parent = window;
    for (let i = 0; i < path.length-1; i++)
    {
        parent = (parent[path[i]] ||= {});
    }
    parent[path[path.length-1]] ||= (leaf || {});
}

confirmObjPath("_inlineScripts.state.clips");
confirmObjPath("_inlineScripts.clips.priorExpansion", "");
confirmObjPath(
	"_inlineScripts.inlineScripts.listeners.state.onReset.clips",
	function(expand)
	{
		expand("clips reset");
	});
confirmObjPath(
	"_inlineScripts.inlineScripts.listeners." +
	"inlineScripts.onExpansion.clips",
	function(expansionInfo)
	{
		_inlineScripts.clips.priorExpansion =
			expansionInfo.expansionText;
	});
```
__
Sets up a state variable for the clips.  Sets up callback for state "reset" event to reset itself.


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.inlineScripts?.listeners?.
	state?.onReset?.clips;
delete _inlineScripts.inlineScripts?.listeners?.
	inlineScripts?.onExpansion?.clips;
```
__
Unregisters event callbacks.


__
```
^clips reset?$
```
__
```js
_inlineScripts.state.clips = {};
return "All clips cleared.\n\n";
```
__
clips reset - Remove all clips.


__
```
^clips?$
```
__
```js
let clipNames = Object.keys(_inlineScripts.state.clips);
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
_inlineScripts.state.clips[$1] = $2;
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
let text = _inlineScripts.state.clips[$1];
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
_inlineScripts.state.clips[$1] = _inlineScripts.clips.priorExpansion;
return "Clip __" + $1 + "__ set to:\n***\n" + _inlineScripts.clips.priorExpansion + "\n***\n\n";
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
if (_inlineScripts.state.clips[$1])
{
	delete _inlineScripts.state.clips[$1];
	return "Clip __" + $1 + "__ removed.\n\n";
}
return "Clip __" + $1 + "__ not removed.  Does not exist.\n\n";
```
__
clips remove {name: name text} - Removes the clip named {name}.
