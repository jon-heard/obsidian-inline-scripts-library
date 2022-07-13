---
obsidianUIMode: preview
---

Shortcuts for working with lists.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the lists.


~~
```
^tejs setup$
```
~~
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.lists ||= {};

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.lists ||= expand =>
{
	expand("reset lists");
};
```
~~
Sets up a state variable for the lists.  Sets up callback for state "reset" event to reset itself.


~~
```
^tejs shutdown$
```
~~
```js
delete window._tejs.listeners?.state?.onReset?.lists;
```
~~
Unregisters event callbacks.


~~
```
^reset lists?$
```
~~
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.lists = {};
return "All lists cleared.\n\n";
```
~~
reset lists - Clear all lists.


~~
~~
```js
function getListItems(name)
{
	let list = window._tejs.state.lists[name];
	if (!list) { return []; }
	let result = [];
	switch (list.type)
	{
		case "basic":
			return list.content;
			break;
		case "folder":
			for (let key in app.fileManager.vault.fileMap)
			{
				if (!key.startsWith(list.content)) { continue; }
				key = key.substr(list.content.length);
				if (key.contains("/")) { continue; }
				if (!key.endsWith(".md")) { continue; }
				result.push("[[" + key.substring(0, key.length-3) + "]]");
			}
			break;
		case "combo":
			for (const sublist of list.content)
			{
				result = result.concat(getListItems(sublist));
			}
			result.sort();
			break;
	}
	return result;
}
```
~~
getListItems - a function to get the items of a given list, regardless of list type.


~~
```
^lists?$
```
~~
```js
let listNames = Object.keys(window._tejs.state.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	const items = getListItems(listNames[i]);
	const listType = window._tejs.state.lists[listNames[i]].type;
	if (listType !== "basic")
	{
		listNames[i] += "    _(" + listType + ": " + window._tejs.state.lists[listNames[i]].content + ")_";
	}
	listNames[i] = listNames[i] + "\n        " + (items.length ? items.join(", ") : "NONE");
}
return "Lists:\n    " + (listNames.length ? listNames.join("\n    ") : "NONE") + "\n\n";
```
~~
lists - Show all list and all items for each list.


~~
```
^lists? list ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
let items = getListItems($1);
items = (items?.length ? items.join(", ") : "NONE");
const listType = window._tejs.state.lists[$1].type;
let content = "";
if (listType !== "basic")
{
	content += "    _(" + listType + ": " + window._tejs.state.lists[$1].content + ")_";
}
return [ "List __" + $1 + "__" + content + ":\n    ", items, "\n\n" ];
```
~~
lists list {list name} - Show all items in the list {list name}.


~~
```
^lists? pick ((?:[_a-zA-Z][_a-zA-Z0-9]*)?)((?: [1-9][0-9]*)?)$
```
~~
```js
// note: Test string accepts empty {list name} for use as a sub-shortcut.
// note: Success returns string array for use as a sub-shortcut.
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let items = getListItems($1);
if (items?.length || $2)
{
	let result = Number($2) || roll(items.length);
	result = items[result - 1];
	return [ "__", result, "__ picked from list __", $1, "__.\n\n" ];
}
return [ "Failed to pick from list __" + $1 + "__.  List is empty.\n\n" ];
```
~~
lists pick {list name} - Get a random item from the list {list name}.


~~
```
^lists? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
~~
```js
window._tejs.state.lists[$1] ||= { type: "basic", content: [] };
const ERROR_PREFIX = "Failed to add __" + $2 + "__ to list __" + $1 + "__.  ";
const type = window._tejs.state.lists[$1].type;
if (type === "basic")
{
	const c = window._tejs.state.lists[$1].content;
	c.push($2);
	c.sort();
	return "__" + $2 + "__ added to list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const c = window._tejs.state.lists[$1].content;
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
~~
lists add {list name} {item} - Add {item} to the list {list name}.  Allows duplicate items.
    - Can only add to basic lists and combo lists that contain basic lists.


~~
```
^lists? remove ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
~~
```js
const ERROR_PREFIX = "Failed to remove __" + $2 + "__ from list __" + $1 + "__.  ";
if (!window._tejs.state.lists[$1])
{
	return ERROR_PREFIX + "Entry not found.\n\n";
}
const type = window._tejs.state.lists[$1].type;
if (type === "basic")
{
	const list = getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Entry not found.\n\n";
	}
	const c = window._tejs.state.lists[$1].content;
	let i = c.lastIndexOf($2);
	c.splice(i, 1);
	return "__" + $2 + "__ removed from list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const list = getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Entry not found.\n\n";
	}
	const c = window._tejs.state.lists[$1].content;
	// Iterate in reverse order, to be sure and remove LAST entry
	for (let i = c.length-1; i >= 0; i--)
	{
		let result = expand("lists remove " + c[i] + " " + $2);
		if (!result.startsWith("Failed"))
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
~~
lists remove {list name} {item} - Remove an instance of {item} from the list {list name}.
    - Can only remove from basic lists and combo lists that contain basic lists.


~~
```
^lists? removelist ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
if (window._tejs.state.lists[$1])
{
	delete window._tejs.state.lists[$1];
	return "List __" + $1 + "__ removed.\n\n";
}
return "Failed to remove list __" + $1 + "__.  List does not exist.\n\n";
```
~~
lists removelist {list name} - Remove the entire list {list name}.


~~
```
^lists? addfolder ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
~~
```js
if (!$2.endsWith("/")) { $2 += "/"; }
window._tejs.state.lists[$1] = { type: "folder", content: $2 };
return "List __" + $1 + "__ added as a folder-list that is linked to the folder \"__" + $2 + "__\".\n\n";
```
~~
lists addfolder {list name} {folder} - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.


~~
```
^lists? addcombo ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][ _a-zA-Z0-9]*)$
```
~~
```js
let links = $2.split(" ");
window._tejs.state.lists[$1] = { type: "combo", content: links };
return "List __" + $1 + "__ added as a combo-list linked to:\n    " + links.join(", ") + "\n\n";
```
~~
lists addcombo {list name} {sub list 1} {sub list 2}... - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.


~~
```
^lists? listraw ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
return getListItems($1);
```
~~
hidden - get the items in a list without any formatting.  Useful internally (as a sub-shortcut).


~~
```
^lists? type ([_a-zA-Z][_a-zA-Z0-9]*)$
```
~~
```js
if (!window._tejs.state.lists.hasOwnProperty($1))
{
	return "none";
}
return window._tejs.state.lists[$1].type;
```
~~
hidden - get the type of the list named {list name}.  Useful internally (as a sub-shortcut).
