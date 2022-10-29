---
obsidianUIMode: preview
---

This shortcut-file includes shortcuts to get and set note-variables.  Note-variables are variables set in the YAML frontmatter.  They can be used by many plugins, including DataView and TTRPG StatBlocks.

This shortcut file can be used by other shortcut-files to let them read and manipulate data in notes for many uses, including TTRPG character sheets.

This shortcut-file has a tutorial video available:
- [Using the "notevars" shortcut-file to work with note variables](https://www.youtube.com/watch?v=EsV4WcMwhbA) (runtime 6:59)

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the isMarkdownRefreshed flag.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Initialize state (just a flag)
confirmObjectPath(
	"_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed", true);

// Event callback - state.onReset
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.notevars",
	function()
	{
		expand("notevars reset");
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
// Delete the state
delete _inlineScripts.state?.sessionState?.notevars;

// Delete the event callback
delete _inlineScripts.state?.listeners?.onReset?.notevars;
```
__
Shuts down this shortcut-file


__
```
^notevars reset$
```
__
```js
const confirmObjectPath = _inlineScripts.inlineScripts.HelperFncs.confirmObjectPath;

// Reset the state
confirmObjectPath("_inlineScripts.state.sessionState.notevars");
_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed = true;

return expFormat("All notepicks cleared.");
```
__
notevars reset - Clears the isMarkdownRefreshed flag.
***


__
```
^notevars isMarkdownRefreshed ?(|[y|n])$
```
__
```js
// If parameter is specified, update the flag based on it
if ($1)
{
	_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed = ($1 === "y");
}

// Return a notification announcing the flag's current state
return expFormat(
	"notevars isMarkdownRefreshed is " +
	(_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed ?
	"__enabled__" : "__disabled__"));
```
__
notevars isMarkdownRefreshed {state: y OR n, default: ""} - If {state} is given, assigns it to the notevars "isMarkdownRefreshed" flag.  Otherwise, displays the current "isMarkdownRefreshed" flag.
If isMarkdownRefreshed flag is set then a note's markdown is refreshed each time one of it's variables is set.
***


__
```
^notevars get ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*)
```
__
```js
// Remove any quotes around the note path parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];

// If no file object, or is a folder, early out
if (!file)
{
	return expFormat([ "", "No value.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "No value.  __" + $1 + "__ is a folder." ]);
}

// Get the file's cached data
const cache = app.metadataCache.getFileCache(file);

// If no cached data, early out
if (!cache)
{
	return expFormat(
		[ "", "No value.  Note __" + $1 + "__ is not properly cached by Obsidian." ]);
}

// Get the front-matter object
const fm = cache.frontmatter;

// If no front-matter, early out (no message, since it technically worked, but the
// variable is empty)
if (!fm)
{
	return null;
}

// Get the variable valuable
const result = fm[$2] || fm[$2 + ":"] || null;

// Return the result.  If it's an array, it's joined to a single string.
return Array.isArray(result) ? result.join(",") : result;
```
__
notevars get {note name: path text} {variable name: name text} - Expands to the value of variable {variable name} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars getArray ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) ([0-9]+)$
```
__
```js
// Remove any quotes around the note path parameter
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];

// If no file object, or is a folder, early out
if (!file)
{
	return expFormat([ "", "No value.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "No value.  __" + $1 + "__ is a folder." ]);
}

// Get the file's cached data
const cache = app.metadataCache.getFileCache(file);

// If no cached data, early out
if (!cache)
{
	return expFormat(
		[ "", "No value.  Note __" + $1 + "__ is not properly cached by " +
		"Obsidian.\n\n" ]);
}

// Get the front-matter object
const fm = cache.frontmatter;

// If no front-matter, early out (no message, since it technically worked, but the
// variable is empty)
if (!fm)
{
	return null;
}

// Get the variable valuable
const result = fm[$2] || fm[$2 + ":"] || null;

// Return the result, indexed by $3 (as an array)
return Array.isArray(result) ? result[$3] : ($3 === "0") ? result : null;
```
__
notevars getArray {note name: path text} {array name: name text} {index: >=0} - Expands to the value of item {index} of array {array name} in note {note name}.  If {note name} is "." then it represents the current note.


__
__
```js
// Refresh the specified file's view the next time it is changed.
// This lets variable changes propegate to DataView inline queries.
function refreshPreviewOnNextModify(file)
{
	// This function does nothing unless isMarkdownRefreshed flag is true
	if (!_inlineScripts.state.sessionState.notevars.isMarkdownRefreshed) { return; }

	// This function does nothing unless the file is opened in Obsidian
	if (!_inlineScripts.inlineScripts.HelperFncs.getLeafForFile(file)) { return; }

	// Setup an event callback for a file being changed
	const onChanged = (changedFile) =>
	{
		// If the changed file is the one we're monitoring
		if (changedFile === file)
		{
			// Stop monitoring for file changes
			app.metadataCache.off("changed", onChanged);

			// Find the view for the specified file
			const leaf = _inlineScripts.inlineScripts.HelperFncs.getLeafForFile(file);
			if (!leaf) { return; }
			const view = leaf.view;

			// Force the view to re-render on the next frame
			setTimeout(() => view.modes.preview.rerender(true), 0);
		}
	}
	app.metadataCache.on("changed", onChanged);
}
```
__
Helper scripts


__
```
^notevars set ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
// Remove any quotes that surround the path
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];

// If no file object, or is a folder, early out
if (!file)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  __" + $1 + "__ is a folder." ]);
}

// Get the file's content
const content = await app.vault.cachedRead(file);

// If failed to load content,early out
if (content === null || content === undefined)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set. Note __" + $1 + "__ read failed." ]);
}

// Resolve value - escape characters for newline and tab
$3 = $3.replaceAll("\\n", "\n").replaceAll("\\t", "\t");


// Start the final expansion
let result;

// Get the file content's header
const fmMatch = content.match(/^(\n*---)(\n[\S\s]*\n)(\n*---\n)/);

// If there isn't yet a frontmatter, create it along with the variable name/value pair
if (!fmMatch)
{
	result = "---\n" + $2 + ": " + $3 + "\n---\n\n" + content;
}

// If there IS a frontmatter, add the variable name/value pair to it
else
{
	// Find the variable in the frontmatter
	const varMatch = fmMatch[2].match("(\n" + $2 + ":)[^\n]*\n");

	// If the variable is found, modify it
	if (varMatch)
	{
		// Determine what character the variable definition ends at
		let valueEndIndex = varMatch.index + varMatch[0].length;
		while(fmMatch[2][valueEndIndex] === "-" ||
		      fmMatch[2][valueEndIndex] === " ")
		{
			valueEndIndex = fmMatch[2].indexOf("\n", valueEndIndex) + 1;
		}

		// Insert the new variable definition in the old one's place
		result =
			fmMatch[1] + fmMatch[2].slice(0, varMatch.index) + varMatch[1] + " " +
			$3 + "\n" + fmMatch[2].slice(valueEndIndex) + fmMatch[3] +
			content.slice(fmMatch[0].length);
	}

	// If the variable is not found append it to the frontmatter
	else
	{
		result =
			fmMatch[1] + fmMatch[2] + $2 + ": " + $3 + "\n" + fmMatch[3] +
			content.slice(fmMatch[0].length);
	}
}

// Add an event callback to react to the next file-change by updating the file's view
refreshPreviewOnNextModify(file);

// Write the content changes back into the file
await app.vault.modify(file, result);

return expFormat(
	"Note __" + $1 + "__, variable __" + $2 + "__ set to __" + $3 + "__.");
```
__
notevars set {note name: path text} {variable name: name text} {value: text} - Sets the value of variable {variable name} to {value} in note {note name}.  If {note name} is "." then it represents the current note.


__
```
^notevars setArray ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ([_a-zA-Z][_a-zA-Z0-9]*) (.*)$
```
__
```js
// Remove any quotes that surround the path
$1 = $1.replaceAll(/^\"|\"$/g, "");

// If notename is ".", change it to the current file
if ($1 === ".") { $1 = app.workspace.getActiveFile()?.path; }

// Get the file object for the specified note
const file = app.vault.fileMap[$1] || app.vault.fileMap[$1 + ".md"];

// If no file object, or is a folder, early out
if (!file)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  Note __" + $1 + "__ not found." ]);
}
if (file.children)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set.  __" + $1 + "__ is a folder." ]);
}

// Get the file's content
const content = await app.vault.cachedRead(file);

// If failed to load content,early out
if (content === null || content === undefined)
{
	return expFormat(
		[ "", "Variable __" + $2 + "__ not set. Note __" + $1 + "__ read failed." ]);
}

// Resolve value - escape characters for newline and tab, comma-split into an array
$3 = $3.replaceAll("\\n", "\n").replaceAll("\\t", "\t").split(",").filter(v => v);

// Start the final expansion
let result;

// Get the file content's frontmatter
const fmMatch = content.match(/^(\n*---)(\n[\S\s]*\n)(\n*---\n)/);

// If there isn't yet a frontmatter, create it along with the variable name/value pair
if (!fmMatch)
{
	result = "---\n" + $2 + ":\n";
	for (let i = 0; i < $3.length; i++)
	{
		result += "- " + $3[i] + "\n";
	}
	result += "---\n\n" + content;
}

// If there IS a frontmatter, add the variable name/value pair to it
else
{
	// Find the variable in the frontmatter
	const varMatch = fmMatch[2].match("(\n" + $2 + ":)[^\n]*\n");

	// If the variable is found, modify it
	if (varMatch)
	{
		// Determine what character the variable definition ends at
		let valueEndIndex = varMatch.index + varMatch[0].length;
		while(fmMatch[2][valueEndIndex] === "-" ||
		      fmMatch[2][valueEndIndex] === " ")
		{
			valueEndIndex = fmMatch[2].indexOf("\n", valueEndIndex) + 1;
		}

		// Insert the new variable definition in the old one's place
		result =
			fmMatch[1] + fmMatch[2].slice(0, varMatch.index) + varMatch[1] + "\n";
		for (let i = 0; i < $3.length; i++)
		{
			result += "- " + $3[i] + "\n";
		}
		result +=
			fmMatch[2].slice(valueEndIndex) + fmMatch[3] +
			content.slice(fmMatch[0].length);
	}

	// If the variable is not found append it to the frontmatter (as an array)
	else
	{
		result = fmMatch[1] + fmMatch[2] + $2 + ":\n";
		for (let i = 0; i < $3.length; i++)
		{
			result += "- " + $3[i] + "\n";
		}
		result += fmMatch[3] + content.slice(fmMatch[0].length);
	}
}

// Add an event callback to react to the next file-change by updating the file's view
refreshPreviewOnNextModify(file);

// Write the content changes back into the file
await app.vault.modify(file, result);

return expFormat(
	"Note __" + $1 + "__, variable __" + $2 + "__ set to __" + $3 + "__.");
```
__
notevars setArray {note name: path text} {array name: name text} {values: comma separated text} - Sets the values of array {array name} to {value1}, {value2}, etc. in note {note name}.  If {note name} is "." then it represents the current note.
