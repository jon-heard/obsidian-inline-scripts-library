---
obsidianUIMode: preview
---

Setup and manage variables within notes.  Can be used by other shortcut-files for working with data in notes.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the managed varnotes.

A variable in a note follows one of these forms (where $ is the end of a line):

```
**%%  variable_name  %%**  variable_value  $
```

```
**%%  variable_name  %%**  _  variable_value  _
```

A few points about these forms
- In both forms above, any place where you see spaces can have 0 or more spaces.
* The variable_name is case-INsensitive, so STR, str and Str are all considered the same name.

Examples:
```
**%%Name%%**_Maggie Smith_
```
This specifies that the variable named "name" (case-insensitive) holds the value "Maggie Smith".
```
**%% str %%**  18
```
This specifies that the variable named "str" (case-insensitive) holds the value 18.


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
window._tejs.varnotes.REGEX_VARS ||= /\*\*%%[ ]*([_a-zA-Z_][_a-zA-Z_0-9]*)[ ]*%%\*\*[ ]*(?:_[ ]*([^_]*[^_ ])[ ]*_|([^\n]*[^\n ])[ ]*$)/g;
window._tejs.varnotes.REGEX_VAR ||= "(\\*\\*%%[ ]*~1~[ ]*%%\\*\\*[ ]*)(?:(_[ ]*)([^_]*[^_ ])[ ]*_|([^\n]*[^\n ])[ ]*$)";

if (!window._tejs.varnotes.variables)
{
	window._tejs.varnotes.variables ||= {};
	for (const varnoteName in window._tejs.state.varnotes)
	{
		expand("varnotes refresh " + varnoteName);
	}
}

if (!window._tejs.varnotes.onModified)
{
	window._tejs.varnotes.onModified = file =>
	{
		for (const key in window._tejs.state.varnotes)
		{
			if (window._tejs.state.varnotes[key] == file.path)
			{
				expand("varnotes refresh " + key);
			}
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
^reset varnotes?$
```
~~
```js
window._tejs.state ||= {};
window._tejs.state.varnotes = {};
window._tejs.varnotes.variables = {};
return "All varnotes cleared.\n\n";
```
~~
reset varnotes - Removes all stored varnotes from the session state.
***


~~
```
^varnotes?$
```
~~
```js
let result = ["Varnotes:\n"];
let hasNone = true;
for (const key in window._tejs.state.varnotes)
{
	result.push(". " + key + "    _(" + window._tejs.state.varnotes[key] + ")_\n");
	hasNone = false;
}
if (hasNone) { result.push("NONE\n"); }
result.push("\n");
return result;
```
~~
varnotes - Lists all varnotes and the files they are connected to.


~~
```
^varnotes? vars ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
let result = [ "Varnote __", $1, "__:\n" ];
const variables = window._tejs.varnotes.variables[$1];
if (variables)
{
	let hasVariables = false;
	for (const varName in variables)
	{
		if (varName === "varnote") { continue; }
		if (!hasVariables)
		{
			result.push("\n|name|value|\n|-:|:-|\n");
			hasVariables = true;
		}
		result.push("    |" + varName + "|" + variables[varName] + "|\n");
	}
	if (!hasVariables) { result.push("NO VARS\n"); }
	result.push("\n");
}
else
{
	result = "Varnote __" + $1 + "__ not registered.\n\n";
}
return result;
```
~~
varnotes vars {varnote name} - Lists all the variables for the varnote named {varnote name} (a required name property).


~~
```
^varnotes? add ([a-zA-Z_][a-zA-Z_0-9]*) (.*)$
```
~~
```js
$2 = $2.endsWith(".md") ? $2 : $2 + ".md";
window._tejs.state.varnotes[$1] = $2
expand("varnotes refresh " + $1);
return "Varnote __" + $1 + "__ added.\n\n";
```
~~
varnotes add {varnote name} {file address} - Adds a varnote named {varnote name} (a required name parameter) that is attached to the file at {file address} (a required file address parameter).


~~
```
^varnotes? remove ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
if (!window._tejs.state.varnotes[$1])
{
	return "Varnote __" + $1 + "__ not removed.  Does not exist.\n\n";
}
delete window._tejs.state.varnotes[$1];
delete window._tejs.varnotes.variables[$1];
return "Varnote __" + $1 + "__ removed.\n\n";
```
~~
varnotes remove {varnote name} - Removes the varnote {varnote name} (a required name parameter).


~~
```
^(?:varnotes? get|vn) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const variables = window._tejs.varnotes.variables[$1];
if (!variables) { return [ "Varnote __", $1, "__ not found.\n\n" ]; }
const variable = variables[$2.toLowerCase()];
if (!variable) { return [ "Variable __", $2, "__ not found.\n\n" ]; }
return variable;
```
~~
varnotes get {varnote name} {variable name} - Gets the value of the variable named {variable name} (a required name parameter) in the varnote named {varnote name} (a required name parameter).
        Alternative shortcut: __vn {varnote name} {variable name}__


~~
```
^(?:varnotes? set|vnset) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*) (.+)$
```
~~
```js
const ERROR_PREFIX = "Variable __" + $2 + "__ not set.  ";
if ($3.contains("_")) { return ERROR_PREFIX + "New value contains underscore: \"__" + $3 + "__\"."; }
const variables = window._tejs.varnotes.variables[$1];
if (!variables) { return ERROR_PREFIX + "Varnote __" + $1 + "__ not registered.\n\n"; }
let content = variables["varnote"];
if (!content) { return ERROR_PREFIX + "Varnote __" + $1 + "__ has no content.\n\n"; }

const regex = window._tejs.varnotes.REGEX_VAR.replace("~1~", $2);
let changeBlocks = [];
for (const r of content.matchAll(regex))
{
	changeBlocks.push(
	{
		start: r.index + r[1].length + (r[2]||"").length,
		length: Math.max((r[3]||"").length, (r[4]||"").length)
	});
}
if (!changeBlocks.length)
{
	return [ ERROR_PREFIX + "Not found in varnote __" + $1 + "__.\n\n" ];
}

changeBlocks.reverse();
for (const changeBlock of changeBlocks)
{
	content =
		content.slice(0, changeBlock.start) + $3 +
		content.slice(changeBlock.start + changeBlock.length);
}

const filename = window._tejs.state.varnotes[$1];
if (!filename)
{
	return ERROR_PREFIX + "Varnote __" + $1 + "__ not registered.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return ERROR_PREFIX + "Note __" + filename + "__ not found.\n\n";
}
app.vault.modify(file, content);

return [ "Variable __", $2, "__ in varnote __", $1, "__ set to \"__", $3, "__\".\n\n" ];
```
~~
varnotes set {varnote name} {variable name} {value} - Sets the variable named {variable name} (a required name parameter) to {value} (a required text parameter), in the varnote named {varnote name} (a required name parameter).
        Alternative shortcut: __vnset {varnote name} {variable name} {value}__


~~
```
^varnotes? refresh$
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
result.push("Varnotes refreshed.\n\n");
return result;
```
~~
varnotes refresh - Get the latest variables from all varnotes.


~~
```
^varnotes? refresh ([a-zA-Z_][a-zA-Z_0-9]*)$
```
~~
```js
const ERROR_PREFIX = "Varnote __" + $1 + "__ not refreshed.  ";
const filename = window._tejs.state.varnotes[$1];
if (!filename)
{
	return ERROR_PREFIX + "Not registered.\n\n";
}
const file = app.vault.fileMap[filename];
if (!file)
{
	return ERROR_PREFIX + "File __" + filename + "__ not found.\n\n";
}
app.vault.cachedRead(file).then(r =>
{
	window._tejs.varnotes.variables[$1] = {};
	for (const match of r.matchAll(window._tejs.varnotes.REGEX_VARS))
	{
		const varName = match[1].toLowerCase();

		// Only take the first value of a variable (if it has multiple
		// instances in note)
		if (window._tejs.varnotes.variables[$1][varName])
		{
			continue;
		}

		window._tejs.varnotes.variables[$1][varName] =
			((match[2]||"") + (match[3]||""));
	}
	window._tejs.varnotes.variables[$1]["varnote"] = r;
});
return [ "Varnote __", $1, "__ refreshed.\n\n" ];
```
~~
varnotes refresh {varnote name} - Pull the variables from varnote {varnote name} (a required "name" parameter).
