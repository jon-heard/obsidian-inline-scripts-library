This shortcut-file includes shortcuts that let the user manage a session state.  Effectively allows saving and restoring data between sessions.  

This shortcut-file also includes shortcuts that let the user manage "clips" of text.

This shortcut file can be used by other shortcut files to help them save/restore their states.

To save the session state, use the "state" shortcut (no parameters).  This expands to a "state string", which represents the current session state, which you can leave as part of the note.  To load the session state, copy the state string and pass it into the "state" shortcut.

Enter the shortcut "help state" for reference.


~~
```
^help state$
```
~~
```js
let result = "### TEJS STATE SHORTCUTS HELP\n";
result += "- __help state__ - Display this help text.\n";
result += "- __reset state__ - Clear all state.\n";
result += "***\n";
result += "- __state__ - Expands to the current state (for session saving).\n";
result += "- __state {state}__ - Sets the current state based on {state}: a string created previously with the \"state\" shortcut.\n";
result += "***\n";
result += "- __clip__ - List all stored clips.\n";
result += "- __clip {name}__ - Expands to the value stored in clip {name}.\n";
result += "- __clipadd {name} {value}__ - Creates a clip {name} that stores {value}.\n";
result += "- __clipremove {name}__ - Removes the clip {name}.\n";
return result + "\n";
```


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.clips ||= {};
```


~~
```
^reset state$
```
~~
```js
window._tejsState = {};
window._tejsState.clips = {};
return "All state cleared.\n\n";
```

~~
```
^state$
```
~~
```js
return "__TEJS state__\n" + JSON.stringify(window._tejsState) + "\n\n";
```


~~
```
^state (.*)$
```
~~
```js
window._tejsState = JSON.parse($1);
return "TEJS state loaded.\n\n";
```


~~
```
^clip$
```
~~
```js
let clipNames = Object.keys(window._tejsState.clips);
return "__Clips__\n" + (clipNames.length ? clipNames.join(", ") : "none") + "\n\n";
```


~~
```
^clip ([a-zA-Z]+)$
```
~~
```js
let text = window._tejsState.clips[$1];
return text || "";
```


~~
```
^clipadd ([a-zA-Z]+) (.+)$
```
~~
```js
window._tejsState.clips[$1] = $2;
return "__Clip \"" + $1 + "\" set to__\n" + $2 + "\n\n";
```


~~
```
^clipremove ([a-zA-Z]+)$
```
~~
```js
if (window._tejsState.clips.hasOwnProperty($1))
{
	delete window._tejsState.clips[$1];
	return "Clip \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove clip \"" + $1 + "\".  Does not exist.\n\n";
```
