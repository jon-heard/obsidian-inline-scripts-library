---
obsidianUIMode: preview
---

Shortcuts that let the user manage clips of text.  A "clip" is a bit of named text that can be quickly added to your note.  Similar to a shortcut, but simplistic and tied to the session state.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the clips.


__
__
```js
// confirm that an object path is available
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
```
__
Some userful functions


__
```
^sfile setup$
```
__
```js
confirmObjPath("_inlineScripts.state.clips");
confirmObjPath("_inlineScripts.clips.priorExpansion", "");
confirmObjPath(
	"_inlineScripts.listeners.state.onReset.clips",
	function(expand)
	{
		expand("reset clips");
	});
confirmObjPath(
	"_inlineScripts.listeners.inlineScripts.onExpansion.clips",
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
delete _inlineScripts.listeners?.state?.onReset?.clips;
delete _inlineScripts.listeners?.inlineScripts?.onExpansion?.clips;
```
__
Unregisters event callbacks.


__
```
^reset clips?$
```
__
```js
_inlineScripts.state.clips = {};
return "All clips cleared.\n\n";
```
__
reset clips - Remove all clips.


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
^(?:clips? get|cg) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
let text = _inlineScripts.state.clips[$1];
return text || "";
```
__
clips get {name: required, name} - Expands to the value stored in clip {name}.
	- Alternative: __cg {name: required, name}__


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
clips set {name: required, name} {value: required, text} - Creates / Sets a clip named {name} to the string {value}.


__
```
^clips? expansion ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
$1 = $1.toLowerCase();
_inlineScripts.state.clips[$1] = _inlineScripts.clips.priorExpansion;
return "Clip __" + $1 + "__ set to:\n" + _inlineScripts.clips.priorExpansion + "\n\n";
```
__
clips expansion {name: required name} - Creates a clip named {name} that stores the previous expansion.


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
return "Failed to remove clip __" + $1 + "__.  Does not exist.\n\n";
```
__
clips remove {name: required, name} - Removes the clip named {name}.
