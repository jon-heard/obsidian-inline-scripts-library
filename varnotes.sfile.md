---
obsidianUIMode: preview
---

Setup and manage variables within notes.  Can be used by other shortcut-files for working with data in notes.

Uses __state.sfile__ shortcut-file (optional).
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


__
```
^sfile setup$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.varnotes ||= {};
window._inlineScripts.varnotes ||= {};
window._inlineScripts.varnotes.REGEX_VARS ||= /\*\*%%[ ]*([_a-zA-Z_][_a-zA-Z_0-9]*)[ ]*%%\*\*[ ]*(?:_[ ]*([^_]*[^_ ])[ ]*_|([^\n]*[^\n ])[ ]*$)/g;
window._inlineScripts.varnotes.REGEX_VAR ||= "(\\*\\*%%[ ]*~1~[ ]*%%\\*\\*[ ]*)(?:(_[ ]*)([^_]*[^_ ])[ ]*_|([^\n]*[^\n ])[ ]*$)";

if (!window._inlineScripts.varnotes.variables)
{
	window._inlineScripts.varnotes.variables ||= {};
	for (const varnoteName in window._inlineScripts.state.varnotes)
	{
		expand("varnotes refresh " + varnoteName);
	}
}

if (!window._inlineScripts.varnotes.onModified)
{
	window._inlineScripts.varnotes.onModified = file =>
	{
		for (const key in window._inlineScripts.state.varnotes)
		{
			if (window._inlineScripts.state.varnotes[key] == file.path)
			{
				expand("varnotes refresh " + key);
			}
		}
	};
	app.vault.on("modify", window._inlineScripts.varnotes.onModified);
}

window._inlineScripts.listeners ||= {};
window._inlineScripts.listeners.state ||= {};
window._inlineScripts.listeners.state.onReset ||= {};
window._inlineScripts.listeners.state.onReset.varnotes ||= expand =>
{
	expand("reset varnotes");
};
window._inlineScripts.listeners.state.onLoad ||= {};
window._inlineScripts.listeners.state.onLoad.varnotes ||= expand =>
{
	expand("varnotes refresh");
};
```
__
Sets up a state variable for varnotes.  Sets up callback for state "reset" event to reset itself. 
 Registers the file-modified event.


__
```
^sfile shutdown$
```
__
```js
// Unregister note-modify event listener
if (window._inlineScripts.varnotes?.onModified)
{
	app.vault.off("modify", window._inlineScripts.varnotes.onModified);
	delete window._inlineScripts.varnotes.onModified;
}

// Unregister state shortut-file listeners
delete window._inlineScripts.listeners?.state?.onReset?.varnotes;
delete window._inlineScripts.listeners?.state?.onLoad?.varnotes;
```
__
Unregisters event callbacks.


__
```
^reset varnotes?$
```
__
```js
window._inlineScripts.state ||= {};
window._inlineScripts.state.varnotes = {};
window._inlineScripts.varnotes.variables = {};
return "All varnotes cleared.\n\n";
```
__
reset varnotes - Removes all stored varnotes from the session state.
***


__
```
^varnotes?$
```
__
```js
let result = ["Varnotes:\n"];
let hasNone = true;
for (const key in window._inlineScripts.state.varnotes)
{
	result.push(". " + key + "    _(" + window._inlineScripts.state.varnotes[key] + ")_\n");
	hasNone = false;
}
if (hasNone) { result.push("NONE\n"); }
result.push("\n");
return result;
```
__
varnotes - Lists all varnotes and the files they are connected to.


__
```
^varnotes? vars ([a-zA-Z_][a-zA-Z_0-9]*)$
```
__
```js
let result = [ "Varnote __", $1, "__:\n" ];
const variables = window._inlineScripts.varnotes.variables[$1];
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
__
varnotes vars {varnote name: required, name} - Lists all the variables for the varnote named {varnote name}.


__
```
^varnotes? add ([a-zA-Z_][a-zA-Z_0-9]*) (.*)$
```
__
```js
$2 = $2.endsWith(".md") ? $2 : $2 + ".md";
window._inlineScripts.state.varnotes[$1] = $2
expand("varnotes refresh " + $1);
return "Varnote __" + $1 + "__ added.\n\n";
```
__
varnotes add {varnote name: required, name} {file address: required, text} - Adds a varnote named {varnote name} that is attached to the file at {file address}.


__
```
^varnotes? remove ([a-zA-Z_][a-zA-Z_0-9]*)$
```
__
```js
if (!window._inlineScripts.state.varnotes[$1])
{
	return "Varnote __" + $1 + "__ not removed.  Does not exist.\n\n";
}
delete window._inlineScripts.state.varnotes[$1];
delete window._inlineScripts.varnotes.variables[$1];
return "Varnote __" + $1 + "__ removed.\n\n";
```
__
varnotes remove {varnote name: required, name} - Removes the varnote {varnote name}.


__
```
^(?:varnotes? get|vn) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*)$
```
__
```js
const variables = window._inlineScripts.varnotes.variables[$1];
if (!variables) { return [ "Varnote __", $1, "__ not found.\n\n" ]; }
const variable = variables[$2.toLowerCase()];
if (!variable) { return [ "Variable __", $2, "__ not found.\n\n" ]; }
return variable;
```
__
varnotes get {varnote name: required, name} {variable name: required name} - Gets the value of the variable named {variable name} in the varnote named {varnote name}.
        Alternative shortcut: __vn {varnote name} {variable name}__


__
```
^(?:varnotes? set|vnset) ([a-zA-Z_][a-zA-Z_0-9]*) ([a-zA-Z_][a-zA-Z_0-9]*) (.+)$
```
__
```js
const ERROR_PREFIX = "Variable __" + $2 + "__ not set.  ";
if ($3.contains("_")) { return ERROR_PREFIX + "New value contains underscore: \"__" + $3 + "__\"."; }
const variables = window._inlineScripts.varnotes.variables[$1];
if (!variables) { return ERROR_PREFIX + "Varnote __" + $1 + "__ not registered.\n\n"; }
let content = variables["varnote"];
if (!content) { return ERROR_PREFIX + "Varnote __" + $1 + "__ has no content.\n\n"; }

const regex = window._inlineScripts.varnotes.REGEX_VAR.replace("~1~", $2);
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

const filename = window._inlineScripts.state.varnotes[$1];
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
__
varnotes set {varnote name: required, name} {variable name: required name} {value: required text} - Sets the variable named {variable name} to {value}, in the varnote named {varnote name}.
        Alternative shortcut: __vnset {varnote name} {variable name} {value}__


__
```
^varnotes? refresh$
```
__
```js
let result = [];
for (const name in window._inlineScripts.state.varnotes)
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
__
varnotes refresh - Get the latest variables from all varnotes.


__
```
^varnotes? refresh ([a-zA-Z_][a-zA-Z_0-9]*)$
```
__
```js
const ERROR_PREFIX = "Varnote __" + $1 + "__ not refreshed.  ";
const filename = window._inlineScripts.state.varnotes[$1];
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
	window._inlineScripts.varnotes.variables[$1] = {};
	for (const match of r.matchAll(window._inlineScripts.varnotes.REGEX_VARS))
	{
		const varName = match[1].toLowerCase();

		// Only take the first value of a variable (if it has multiple
		// instances in note)
		if (window._inlineScripts.varnotes.variables[$1][varName])
		{
			continue;
		}

		window._inlineScripts.varnotes.variables[$1][varName] =
			((match[2]||"") + (match[3]||""));
	}
	window._inlineScripts.varnotes.variables[$1]["varnote"] = r;
});
return [ "Varnote __", $1, "__ refreshed.\n\n" ];
```
__
varnotes refresh {varnote name: required, name} - Pull the variables from varnote {varnote name}.
