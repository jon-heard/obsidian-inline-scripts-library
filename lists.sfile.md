---
obsidianUIMode: preview
---

Shortcuts for working with lists.

This shortcut-file has a tutorial video available:
- [Using the "list" shortcut-file to manage lists](https://www.youtube.com/watch?v=xIYpnBKdYRg) (runtime 5:34)

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the lists.

This shortcut-file has supplementary shortcut files:
__lists_ui.sfile__ - Graphical UI versions of some of these shortcuts.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState.lists");
confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.lists",
	function()
	{
		expand("lists reset");
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
delete _inlineScripts.state?.listeners?.onReset?.lists;
```
__
Shuts down this shortcut-file


__
```
^lists? reset$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState");
_inlineScripts.state.sessionState.lists = {};
return "All lists cleared.\n\n";
```
__
lists reset - Clears all lists.
***


__
__
```js
// a function to get the items of a given list, regardless of list type.
function getListItems(name)
{
	let list = _inlineScripts.state.sessionState.lists[name];
	if (!list) { return []; }
	switch (list.type)
	{
		case "basic":
			return list.content;
			break;
		case "folder":
			const targetFile = app.fileManager.vault.fileMap[list.content];
			if (!targetFile || !targetFile.children)
			{
				return [];
			}
			return [... targetFile.children ]
				.filter(v => !v.children)
				.filter(v => v.name.endsWith(".md"))
				.map(v => "[[" + v.basename + "]]");
		case "combo":
		{
			let result = [];
			for (const sublist of list.content)
			{
				result = result.concat(getListItems(sublist));
			}
			return result.sort(
				(a, b) => a.toLowerCase().localeCompare(b.toLowerCase()) );
		}
	}
}
```
__
Helper scripts


__
```
^lists?$
```
__
```js
let listNames = Object.keys(_inlineScripts.state.sessionState.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	const items = getListItems(listNames[i]);
	const listType = _inlineScripts.state.sessionState.lists[listNames[i]].type;
	if (listType !== "basic")
	{
		listNames[i] +=
			"    _(" + listType + ": " + _inlineScripts.state.sessionState.
			lists[listNames[i]].content + ")_";
	}
	listNames[i] =
		listNames[i] + "\n    - " +
		(items.length ? items.join("\n    - ") : "NONE");
}
return "Lists:\n- " +
	(listNames.length ? listNames.join("\n- ") : "NONE") + "\n\n";
```
__
lists - Shows all lists and all items for each list.


__
```
^lists? list ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
let items = getListItems($1);
items = (items?.length ? (". " + items.join("\n. ")) : "NONE");
const listType = _inlineScripts.state.sessionState.lists[$1]?.type || "basic";
let content = "";
if (listType !== "basic")
{
	content +=
		"    _(" + listType + ": " +
		_inlineScripts.state.sessionState.lists[$1].content + ")_";
}
return [ "List __" + $1 + "__" + content + ":\n", items, "\n\n" ];
```
__
lists list {list name: name text} - Shows all items in the list {list name}.
***


__
```
^lists? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
_inlineScripts.state.sessionState.lists[$1] ||= { type: "basic", content: [] };
const ERROR_PREFIX = "Item __" + $2 + "__ not added to list __" + $1 + "__.  ";
const type = _inlineScripts.state.sessionState.lists[$1].type;
if (type === "basic")
{
	const c = _inlineScripts.state.sessionState.lists[$1].content;
	c.push($2);
	c.sort();
	return "__" + $2 + "__ added to list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const c = _inlineScripts.state.sessionState.lists[$1].content;
	// Iterate in reverse order, to add to the LAST sublist
	for (let i = c.length-1; i >= 0; i--)
	{
		let result = expand("lists add " + c[i] + " " + $2);
		if (!result.startsWith("Failed"))
		{
			return "__" + $2 + "__ added to list __" + $1 + "__.\n\n";
		}
	}
	return ERROR_PREFIX + "No sub-lists support this operation.\n\n";
}
else
{
	return ERROR_PREFIX + "List type does not support this operation.\n\n";
}
```
__
lists add {list name: name text} {item: text} - Adds {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.


__
```
^lists? rename ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return "List not renamed.  List __" + $1 + "__ not found.\n\n";
}
_inlineScripts.state.sessionState.lists[$2] = _inlineScripts.state.sessionState.lists[$1];
delete _inlineScripts.state.sessionState.lists[$1];
return "List __" + $1 + "__ renamed to __" + $2 + "__.\n\n";
```
__
lists rename {original list name: name text} {new list name: name text} - Changes the name of list {original list name} to {new list name}.


__
```
^lists? replace ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ ].*"|[^ ]) (.+)$
```
__
```js
$2 = $2.replaceAll(/^\"|\"$/g, "");

const ERROR_PREFIX =
	  "Item __" + $2 + "__ not replaced in list __" + $1 + "__.  ";
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return ERROR_PREFIX + "Item not found.\n\n";
}
const type = _inlineScripts.state.sessionState.lists[$1].type;
if (type === "basic")
{
	const c = _inlineScripts.state.sessionState.lists[$1].content;
	for (let i = 0; i < c.length; i++)
	{
		if (c[i] == $2)
		{
			c[i] = $3;
		}
	}
	return "All items of __" +
		$2 + "__ in list __" + $1 + "__ replaced with __" + $3 + "__.\n\n";
}
else if (type === "combo")
{
	const subLists = _inlineScripts.state.sessionState.lists[$1].content;
	for (const subList of subLists)
	{
		expand("lists replace " + subList + " " + $2 + " " + $3);
	}
	return "All items of __" +
		$2 + "__ in list __" + $1 + "__ replaced with __" + $3 + "__.\n\n";
}
else
{
	return ERROR_PREFIX + "List type does not support this operation.\n\n";
}
```
__
lists replace {list name: name text} {item: text} {replacement: text} - Replaces all instances of {item} with {replacement}.  {item} can only have spaces if it's surrounded by quotes.


__
```
^lists? remove ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
const ERROR_PREFIX = "Item __" + $2 + "__ not removed from list __" + $1 + "__.  ";
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return ERROR_PREFIX + "Item not found.\n\n";
}
const type = _inlineScripts.state.sessionState.lists[$1].type;
if (type === "basic")
{
	const list = getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Item not found.\n\n";
	}
	const c = _inlineScripts.state.sessionState.lists[$1].content;
	let i = c.lastIndexOf($2);
	c.splice(i, 1);
	return "__" +
		$2 + "__ removed from list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const list = getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Item not found.\n\n";
	}
	const c = _inlineScripts.state.sessionState.lists[$1].content;
	// Iterate in reverse order, to be sure and remove LAST entry
	for (let i = c.length-1; i >= 0; i--)
	{
		let result = expand("lists remove " + c[i] + " " + $2);
		if (!result.startsWith("Item"))
		{
			return "__" + $2 + "__ removed from list __" + $1 + "__.\n\n";
		}
	}
	return ERROR_PREFIX + "Failed on sub-list of this combo-list.\n\n";
}
else
{
	return ERROR_PREFIX + "List type does not support this operation.\n\n";
}
```
__
lists remove {list name: name text} {item: text} - Removes an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.


__
```
^lists? removelist ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (_inlineScripts.state.sessionState.lists[$1])
{
	delete _inlineScripts.state.sessionState.lists[$1];
	return "List __" + $1 + "__ removed.\n\n";
}
return "Failed to remove list __" + $1 + "__.  List does not exist.\n\n";
```
__
lists removelist {list name: name text} - Removes the entire list {list name}.
***


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
// note: Test string accepts empty {list name} for use as a sub-shortcut.
function roll(max)
{
	return Math.trunc(Math.random() * max + 1);
}
let items = getListItems($1);
if (items?.length)
{
	return expand("lists pick " + $1 + " " + roll(items.length));
}
return [ "Failed to pick from list __" + $1 + "__.  List is empty.\n\n" ];
```
__
lists pick {list name: name text} - Gets a random item from the list {list name}.


__
```
^lists? shortcutbatch (|[_a-zA-Z][_a-zA-Z0-9]*) -- (.*)$
```
__
```js
if (!_inlineScripts.state.sessionState.lists[$1])
{
	return "Shortcut batch not run.  List __" + $1 + "__ not found.\n\n";
}
let result = "Shortcut batch begun for list __" + $1 + "__...\n\n";
for (const item of getListItems($1))
{
	result += expand($2.replaceAll("%1", item));
}
return result + "... shortcut batch finished.\n\n";
```
__
lists shortcutbatch {list name: name text} -- {shortcut: text} - Runs shortcut {shortcut} once for each item in list {list name}, replacing "%1" in {shortcut} with the item.
***


__
```
^lists? addfolder ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
_inlineScripts.state.sessionState.lists[$1] = { type: "folder", content: $2 };
return "List __" +
	$1 + "__ added as a folder-list linked to the folder \"__" + $2 + "__\".\n\n";
```
__
lists addfolder {list name: name text} {folder: path text} - Creates a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.


__
```
^lists? addcombo ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][ _a-zA-Z0-9]*)$
```
__
```js
let links = $2.split(" ").filter(v => v);
_inlineScripts.state.sessionState.lists[$1] = { type: "combo", content: links };
return "List __" +
	$1 + "__ added as a combo-list linked to:\n. " + links.join("\n. ") + "\n\n";
```
__
lists addcombo {list name: name text} {sub list 1: name text, default: ""} {sub list 2: name text, default: ""}... - Creates a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.
***


__
```
^lists? fromfile lines ([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
let file = app.vault.fileMap[$1];
if ((!file || file.children) && !$1.endsWith(".md"))
{
	file = app.vault.fileMap[$2 + ".md"];
}
if (!file)
{
    return "Lines not added.  File __" + $1 + "__ not found.\n\n";
}
const content = await app.vault.cachedRead(file);
const lines = content.split("\n").filter(v => v);
for (const line of lines)
{
	expand("lists add " + $1 + " " + line);
}
const pick = lines[Math.trunc(Math.random()*lines.length)];
return "__" + lines.length + "__ items added to the __" + $1 + "__ list.\n\n";
```
__
lists fromfile lines {list name: name text} {file: path text} - Takes the file {file} and breaks it up into individual lines.  Adds each of those lines to list {list name} as an item.


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*) ?(|[1-9][0-9]*)$
```
__
```js
// note: Test string accepts empty {list name} for use as a sub-shortcut.
function roll(max) { return Math.trunc(Math.random() * max + 1); }

let items = getListItems($1);
if (items?.length || $2)
{
	let result = Number($2) || roll(items.length);
	if (result-1 >= items.length)
	{
		return [
			"Failed to pick from list __" + $1 + "__.  The item index " +
			result + " is out of range.\n\n" ];
	}
	result = items[result - 1];
	return [ "__", result, "__ picked from list __", $1, "__.\n\n" ];
}
return [ "Failed to pick from list __" + $1 + "__.  List is empty.\n\n" ];
```
__
hidden - lists pick {list name: name text} {item index: >0, default: ""} - Gets a random item from the list {list name}.  If {item index} is specified, then the item at that index is picked, instead of random.


__
```
^lists? listraw ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
return getListItems($1);
```
__
hidden - gets the items in a list without any formatting.  Useful internally (as a sub-shortcut).


__
```
^lists? type ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (!_inlineScripts.state.sessionState.lists.hasOwnProperty($1))
{
	return "none";
}
return _inlineScripts.state.sessionState.lists[$1].type;
```
__
hidden - gets the type of the list named {list name}.  Useful internally (as a sub-shortcut).
