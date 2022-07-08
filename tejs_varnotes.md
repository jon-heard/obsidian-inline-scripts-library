---
obsidianUIMode: preview
---

Setup and manage variables within notes.  Useful for adding data points (vars) into a note, then reading from and writing to those vars.

Can be used by other shortcut-files for working with data in notes.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the managed varnotes.

The syntax for a noteVar (a variable in a note) is \*\*\%\%variable name\%\%\*\*\_variable value\_:
- double-asterisk
- double-percentages
- __variable name__
- double-percentages
- double-asterisk
- single-underscore
- __variable value__
- single-underscore

The variable name is case-INsensitive, so STR, str and Str are all considered the same name.

Examples:
- \*\*\%\%Name\%\%\*\*\_Maggie Smith\_.
    - This specifies that the notevar named "name" (case-insensitive) holds the value "Maggie Smith".
- \*\*\%\%str\%\%\*\*\_18\_.
    - This specifies that the notevar named "str" (case-insensitive) holds the value 18.


~~
```
^tejs setup$
```
~~
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.varnotes ||= {};
window._tejs.varnotes ||= {};
window._tejs.varnotes.regex_vars ||= /\*\*%%([_a-zA-Z_][_a-zA-Z_0-9]*)%%\*\*_([^_]+)_/g;
window._tejs.varnotes.regex_var ||= "\\*\\*%%~1~%%\\*\\*_([^_]+)_";

if (!window._tejs.varnotes.revLookup)
{
	window._tejs.varnotes.revLookup = {};
	for (const noteName in window._tejs.state.varnotes)
	{
		window._tejs.varnotes.revLookup
			[window._tejs.state.varnotes[noteName]] = noteName;
	}
}

if (!window._tejs.varnotes.noteVars)
{
	window._tejs.varnotes.noteVars ||= {};
	for (const varnoteName in window._tejs.state.varnotes)
	{
		expand("varnotes refresh " + varnoteName);
	}
}

if (!window._tejs.varnotes.onModified)
{
	window._tejs.varnotes.onModified = file =>
	{
		let varnoteName = window._tejs.varnotes.revLookup[file.path];
		if (varnoteName)
		{
			expand("varnotes refresh " + varnoteName);
		}
	};
	app.vault.on("modify", window._tejs.varnotes.onModified);
}

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.varnotes ||= expand =>
{
	expand("reset varnotes");
};
window._tejs.listeners.state.onLoad ||= {};
window._tejs.listeners.state.onLoad.varnotes ||= expand =>
{
	expand("varnotes refresh");
};
```
~~
Sets up a state variable for varnotes.  Sets up callback for state "reset" event to reset itself. 
 Registers the file-modified event.


~~
```
^tejs shutdown$
```
~~
```js
// Unregister note-modify event listener
if (window._tejs.varnotes?.onModified)
{
	app.vault.off("modify", window._tejs.varnotes.onModified);
	delete window._tejs.varnotes.onModified;
}

// Unregister state shortut-file listeners
delete window._tejs.listeners?.state?.onReset?.varnotes;
delete window._tejs.listeners?.state?.onLoad?.varnotes;
```
~~
Unregisters event callbacks.


~~
```
^reset varnotes$
```
~~
```js
window._tejs.state ||= {};
window._tejs.state.varnotes = {};
window._tejs.varnotes.noteVars = {};
return "All varnotes cleared.\n\n";
```
~~
reset varnotes - Removes all stored varnotes from the session state.


~~
```
^varnotes$
```
~~
```js
let result = ["__Varnotes__:\n"];
let hasNone = true;
for (const key in window._tejs.state.varnotes)
{
	result.push("- " + key + " - " + window._tejs.state.varnotes[key] + "\n");
	hasNone = false;
}
if (hasNone) { result.push("NONE"); }
result.push("\n\n");
return result;
```
~~
varnotes - Lists all varnotes and the files they are connected to.


~~
```
^varnotes vars ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
let result = ["__Varnotes - ", $1, "__:\n"];
const noteVars = window._tejs.varnotes.noteVars[$1];
let hasVars = false;
if (noteVars)
{
	for (const varName in noteVars)
	{
		if (varName == "varnote") { continue; }
		result.push("- __" + varName + "__ - \"" + noteVars[varName] + "\"\n");
		hasVars = true;
	}
}
if (!hasVars) { result.push("NO VARS"); }
result.push("\n\n");
return result;
```
~~
varnotes vars {varnote name} - Lists all the vars for the varnote named {varnote name} (a required name property).


~~
```
^varnotes add ([a-zA-Z_][a-zA-Z_0-9]*) (.*)$
```
~~
```js
$2 = $2.endsWith(".md") ? $2 : $2 + ".md";
window._tejs.state.varnotes[$1] = $2
window._tejs.varnotes.revLookup[$2] = $1;
expand("varnotes refresh " + $1);
return "Varnote \"" + $1 + "\" added.\n\n";
```
~~
varnotes add {varnote name} {file address} - Adds a varnote named {varnote name} (a required name parameter) that is attached to the file at {file address} (a required file address parameter).


~~
```
^varnotes remove ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
if (!window._tejs.state.varnotes[$1])
{
	return "Varnote \"" + $i + "\" not removed.  Does not exist.\n\n";
}
delete window._tejs.varnotes.revLookup[window._tejs.state.varnotes[$1]];
delete window._tejs.state.varnotes[$1];
delete window._tejs.varnotes.noteVars[$1];
return "Varnote \"" + $1 + "\" removed.\n\n";
```
~~
varnotes remove {varnote name} - Removes the varnote {var name} (a required name parameter).


~~
```
^(?:varnotes get|vn) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const noteVars = window._tejs.varnotes.noteVars[$1];
if (!noteVars) { return [ "Varnote \"", $1, "\" not found.\n\n" ]; }
const noteVar = noteVars[$2.toLowerCase()];
if (!noteVar) { return [ "Var \"", $2, "\" not found.\n\n" ]; }
return noteVar;
```
~~
varnotes get {varnote name} {var name} - Gets the value of the var named {var name} (a required name parameter) in the varnote named {varnote name} (a required name parameter).
    - Alternative shortcut: __vn {varnote name} {var name}__.


~~
```
^(?:varnotes set|vnset) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*) (.+)$
```
~~
```js
if ($3.contains("_")) { return "Var \"" + $2 + "\" not set. New value contains underscore: \"" + $3 + "\"."; }
const noteVars = window._tejs.varnotes.noteVars[$1];
if (!noteVars) { return "Var \"" + $2 + "\" not set.  Varnote \"" + $1 + "\" not found.\n\n"; }
let content = noteVars["varnote"];
if (!content) { return "Var \"" + $2 + "\" not set.  Varnote \"" + $1 + "\" Has no content.\n\n"; }

const regex =
	window._tejs.varnotes.regex_var.replace("~1~", $2);
const r = content.match(regex);
if (!r) { return ["Var \"" + $2 + "\" not set.  Var not found.\n\n"]; }
content = content.slice(0, r.index + 9 + $2.length) + $3 + content.slice(r.index + 9 + $2.length + r[1].length);

const filename = window._tejs.state.varnotes[$1];
if (!filename)
{
	return "Var \"" + $2 + "\" not set.  Varnote \"" + $1 + "\" not found.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return "Var \"" + $2 + "\" not set.  File \"" + filename + "\" not found.\n\n";
}
app.vault.modify(file, content);

return [ "Var \"", $2, "\" in varnote \"", $1, "\" set to \"", $3, "\".\n\n" ];
```
~~
varnotes set {varnote name} {var name} {value} - Sets the var named {var name} (a required name parameter) to {value} (a required text parameter), in the varnote named {varnote name} (a required name parameter).
    - Alternative shortcut: __vnset {varnote name} {var name} {value}__.


~~
```
^varnotes refresh$
```
~~
```js
let result = [];
for (const name in window._tejs.state.varnotes)
{
	const newResult = expand("varnotes refresh " + name);
	if (!Array.isArray(newResult))
	{
		result.push(newResult, "\n");
	}
}
result.push("Varnote refreshing finished.\n\n");
return result;
```
~~
varnotes refresh - Get the latest vars from all varnotes and store it all.


~~
```
^varnotes refresh ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const filename = window._tejs.state.varnotes[$1];
if (!filename)
{
	return "Varnote \"" + $1 + "\" not refreshed.  File does not exist.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return "Varnote \"" + $1 + "\" not refreshed.  File \"" + filename + "\" not found.\n\n";
}
app.vault.cachedRead(file).then(r =>
{
	window._tejs.varnotes.noteVars[$1] = {};
	for (let ri of r.matchAll(window._tejs.varnotes.regex_vars))
	{
		window._tejs.varnotes.noteVars[$1][ri[1].toLowerCase()] = ri[2];
	}
	window._tejs.varnotes.noteVars[$1]["varnote"] = r;
});
return [ "Varnote \"", $1, "\" refreshed.\n\n" ];
```
~~
varnotes refresh {varnote name} - Pull the vars from varnote {varnote name} (a required "name" parameter).
