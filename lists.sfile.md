---
obsidianUIMode: preview
---

Shortcuts for working with lists.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the lists.


__
```
^sfile setup$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.lists ||= {};

window._inlineScripts.listeners ||= {};
window._inlineScripts.listeners.state ||= {};
window._inlineScripts.listeners.state.onReset ||= {};
window._inlineScripts.listeners.state.onReset.lists ||= function(expand)
{
	expand("reset lists");
};
```
__
Sets up a state variable for the lists.  Sets up callback for state "reset" event to reset itself.


__
```
^sfile shutdown$
```
__
```js
delete window._inlineScripts.listeners?.state?.onReset?.lists;
```
__
Unregisters event callbacks.


__
```
^reset lists?$
```
__
```js
window._inlineScripts ||= {};
window._inlineScripts.state ||= {};
window._inlineScripts.state.lists = {};
return "All lists cleared.\n\n";
```
__
reset lists - Clear all lists.
***


__
__
```js
async function getListItems(name)
{
	let list = window._inlineScripts.state.lists[name];
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
				key = key.slice(list.content.length);
				if (key.contains("/")) { continue; }
				if (!key.endsWith(".md")) { continue; }
				result.push("[[" + key.slice(0,-3) + "]]");
			}
			break;
		case "combo":
			for (const sublist of list.content)
			{
				result = result.concat(await getListItems(sublist));
			}
			result.sort();
			break;
	}
	return result;
}
```
__
getListItems - a function to get the items of a given list, regardless of list type.


__
```
^lists?$
```
__
```js
let listNames = Object.keys(window._inlineScripts.state.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	const items = await getListItems(listNames[i]);
	const listType = window._inlineScripts.state.lists[listNames[i]].type;
	if (listType !== "basic")
	{
		listNames[i] += "    _(" + listType + ": " + window._inlineScripts.state.lists[listNames[i]].content + ")_";
	}
	listNames[i] = listNames[i] + "\n    - " + (items.length ? items.join("\n    - ") : "NONE");
}
return "Lists:\n- " + (listNames.length ? listNames.join("\n- ") : "NONE") + "\n\n";
```
__
lists - Show all list and all items for each list.


__
```
^lists? list ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
let items = await getListItems($1);
items = (items?.length ? (". " + items.join("\n. ")) : "NONE");
const listType = window._inlineScripts.state.lists[$1].type;
let content = "";
if (listType !== "basic")
{
	content += "    _(" + listType + ": " + window._inlineScripts.state.lists[$1].content + ")_";
}
return [ "List __" + $1 + "__" + content + ":\n", items, "\n\n" ];
```
__
lists list {list name: required, name} - Show all items in the list {list name}.


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*)(| [1-9][0-9]*)$
```
__
```js
// note: Test string accepts empty {list name} for use as a sub-shortcut.
// note: Success returns string array for use as a sub-shortcut.
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let items = await getListItems($1);
if (items?.length || $2)
{
	let result = Number($2) || roll(items.length);
	if (result-1 >= items.length)
	{
		return [ "Failed to pick from list __" + $1 + "__.  The item index " + result + " is out of range.\n\n" ];
	}
	result = items[result - 1];
	return [ "__", result, "__ picked from list __", $1, "__.\n\n" ];
}
return [ "Failed to pick from list __" + $1 + "__.  List is empty.\n\n" ];
```
__
lists pick {list name: required, name} {item index: optional, >0} - Get a random item from the list {list name}.  If {item index} is specified, then its item is picked, instead of random.
***


__
```
^lists? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
window._inlineScripts.state.lists[$1] ||= { type: "basic", content: [] };
const ERROR_PREFIX = "Failed to add __" + $2 + "__ to list __" + $1 + "__.  ";
const type = window._inlineScripts.state.lists[$1].type;
if (type === "basic")
{
	const c = window._inlineScripts.state.lists[$1].content;
	c.push($2);
	c.sort();
	return "__" + $2 + "__ added to list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const c = window._inlineScripts.state.lists[$1].content;
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
lists add {list name: required, name} {item: required, text} - Add {item} to the list {list name}.  Allows duplicate items.
    - Can only add to basic lists and combo lists that contain basic lists.


__
```
^lists? remove ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
const ERROR_PREFIX = "Failed to remove __" + $2 + "__ from list __" + $1 + "__.  ";
if (!window._inlineScripts.state.lists[$1])
{
	return ERROR_PREFIX + "Entry not found.\n\n";
}
const type = window._inlineScripts.state.lists[$1].type;
if (type === "basic")
{
	const list = await getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Entry not found.\n\n";
	}
	const c = window._inlineScripts.state.lists[$1].content;
	let i = c.lastIndexOf($2);
	c.splice(i, 1);
	return "__" + $2 + "__ removed from list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const list = await getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Entry not found.\n\n";
	}
	const c = window._inlineScripts.state.lists[$1].content;
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
__
lists remove {list name: required, name} {item: required, text} - Remove an instance of {item} from the list {list name}.
    - Can only remove from basic lists and combo lists that contain basic lists.


__
```
^lists? removelist ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (window._inlineScripts.state.lists[$1])
{
	delete window._inlineScripts.state.lists[$1];
	return "List __" + $1 + "__ removed.\n\n";
}
return "Failed to remove list __" + $1 + "__.  List does not exist.\n\n";
```
__
lists removelist {list name: required, name} - Remove the entire list {list name}.
***


__
```
^lists? addfolder ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
if (!$2.endsWith("/")) { $2 += "/"; }
window._inlineScripts.state.lists[$1] = { type: "folder", content: $2 };
return "List __" + $1 + "__ added as a folder-list linked to the folder \"__" + $2 + "__\".\n\n";
```
__
lists addfolder {list name: required, name} {folder: required, text} - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.


__
```
^lists? addcombo ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][ _a-zA-Z0-9]*)$
```
__
```js
let links = $2.split(" ");
window._inlineScripts.state.lists[$1] = { type: "combo", content: links };
return "List __" + $1 + "__ added as a combo-list linked to:\n. " + links.join("\n. ") + "\n\n";
```
__
lists addcombo {list name: required, name} {sub list 1: optional, name} {sub list 2: optional, name}... - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.


__
```
^lists? listraw ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
return await getListItems($1);
```
__
hidden - get the items in a list without any formatting.  Useful internally (as a sub-shortcut).


__
```
^lists? type ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (!window._inlineScripts.state.lists.hasOwnProperty($1))
{
	return "none";
}
return window._inlineScripts.state.lists[$1].type;
```
__
hidden - get the type of the list named {list name}.  Useful internally (as a sub-shortcut).
