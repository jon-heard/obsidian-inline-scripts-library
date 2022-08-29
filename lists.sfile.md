---
obsidianUIMode: preview
---

Shortcuts for working with lists.

Uses __state.sfile__ shortcut-file (optional).
It uses this to save & load the lists.


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
confirmObjPath("_inlineScripts.state.lists");
confirmObjPath(
	"_inlineScripts.listeners.state.onReset.lists",
	function(expand)
	{
		expand("reset lists");
	});
```
__
Sets up a state variable for the lists.  Sets up callback for state "reset" event to reset itself.


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.listeners?.state?.onReset?.lists;
```
__
Unregisters event callbacks.


__
```
^reset lists?$
```
__
```js
confirmObjPath("_inlineScripts.state");
_inlineScripts.state.lists = {};
return "All lists cleared.\n\n";
```
__
reset lists - Clear all lists.
***


__
__
```js
function getListItems(name)
{
	let list = _inlineScripts.state.lists[name];
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
let listNames = Object.keys(_inlineScripts.state.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	const items = await getListItems(listNames[i]);
	const listType = _inlineScripts.state.lists[listNames[i]].type;
	if (listType !== "basic")
	{
		listNames[i] +=
			"    _(" + listType + ": " +
			_inlineScripts.state.lists[listNames[i]].content +
			")_";
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
const listType = _inlineScripts.state.lists[$1]?.type || "basic";
let content = "";
if (listType !== "basic")
{
	content +=
		"    _(" + listType + ": " +
		_inlineScripts.state.lists[$1].content + ")_";
}
return [ "List __" + $1 + "__" + content + ":\n", items, "\n\n" ];
```
__
lists list {list name: name text} - Show all items in the list {list name}.


__
```
^lists? add ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
_inlineScripts.state.lists[$1] ||= { type: "basic", content: [] };
const ERROR_PREFIX = "Item __" + $2 + "__ not added to list __" + $1 + "__.  ";
const type = _inlineScripts.state.lists[$1].type;
if (type === "basic")
{
	const c = _inlineScripts.state.lists[$1].content;
	c.push($2);
	c.sort();
	return "__" + $2 + "__ added to list __" + $1 + "__.\n\n";
}
else if (type === "combo")
{
	const c = _inlineScripts.state.lists[$1].content;
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
lists add {list name: name text} {item: text} - Add {item} to the list {list name}.  Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.


__
```
^lists? pick (|[_a-zA-Z][_a-zA-Z0-9]*) ?(|[1-9][0-9]*)$
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
lists pick {list name: name text} {item index: >0, default: ""} - Get a random item from the list {list name}.  If {item index} is specified, then the item at that index is picked, instead of random.
***


__
```
^lists? remove ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
const ERROR_PREFIX =
	  "Item __" + $2 + "__ not removed from list __" +
	  $1 + "__.  ";
if (!_inlineScripts.state.lists[$1])
{
	return ERROR_PREFIX + "Item not found.\n\n";
}
const type = _inlineScripts.state.lists[$1].type;
if (type === "basic")
{
	const list = await getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Item not found.\n\n";
	}
	const c = _inlineScripts.state.lists[$1].content;
	let i = c.lastIndexOf($2);
	c.splice(i, 1);
	return "__" +
		$2 + "__ removed from list __" + $1 +
		"__.\n\n";
}
else if (type === "combo")
{
	const list = await getListItems($1);
	if (!list.contains($2))
	{
		return ERROR_PREFIX + "Item not found.\n\n";
	}
	const c = _inlineScripts.state.lists[$1].content;
	// Iterate in reverse order, to be sure and remove LAST entry
	for (let i = c.length-1; i >= 0; i--)
	{
		let result = expand(
			"lists remove " + c[i] + " " + $2);
		if (!result.startsWith("Item"))
		{
			return "__" +
				$2 + "__ removed from list __" + $1 +
				"__.\n\n";
		}
	}
	return ERROR_PREFIX +
		"Failed on sub-list of this combo-list.\n\n";
}
else
{
	return ERROR_PREFIX +
		"List type does not support this operation.\n\n";
}
```
__
lists remove {list name: name text} {item: text} - Remove an instance of {item} from the list {list name}.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.


__
```
^lists? replace ([_a-zA-Z][_a-zA-Z0-9]*) ([^ ]+) (.+)$
```
__
```js
const ERROR_PREFIX =
	  "Item __" + $2 + "__ not replaced in list __" +
	  $1 + "__.  ";
if (!_inlineScripts.state.lists[$1])
{
	return ERROR_PREFIX + "Item not found.\n\n";
}
const type = _inlineScripts.state.lists[$1].type;
if (type === "basic")
{
	const c = _inlineScripts.state.lists[$1].content;
	for (let i = 0; i < c.length; i++)
	{
		if (c[i] == $2.replaceAll("\t", " "))
		{
			c[i] = $3;
		}
	}
	return "All items of __" +
		$2 + "__ in list __" + $1 +
		"__ replaced with __" + $3 + "__.\n\n";
}
else if (type === "combo")
{
	const subLists = _inlineScripts.state.lists[$1].content;
	for (const subList of subLists)
	{
		expand("lists replace " + subList + " " + $2 + " " + $3);
	}
	return "All items of __" +
		$2 + "__ in list __" + $1 +
		"__ replaced with __" + $3 + "__.\n\n";
}
else
{
	return ERROR_PREFIX +
		"List type does not support this operation.\n\n";
}
```
__
lists replace {list name: name text} {item: non-space text} {replacement: text} - Replace all instances of {item} with {replacement}.  Any space characters in {item} should be replaced by a tab character to reserve the space character for the end of {item}.


__
```
^lists? removelist ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if (_inlineScripts.state.lists[$1])
{
	delete _inlineScripts.state.lists[$1];
	return "List __" + $1 + "__ removed.\n\n";
}
return "Failed to remove list __" + $1 + "__.  List does not exist.\n\n";
```
__
lists removelist {list name: name text} - Remove the entire list {list name}.
***


__
```
^lists? addfolder ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
if (!$2.endsWith("/")) { $2 += "/"; }
_inlineScripts.state.lists[$1] = { type: "folder", content: $2 };
return "List __" + $1 + "__ added as a folder-list linked to the folder \"__" + $2 + "__\".\n\n";
```
__
lists addfolder {list name: name text} {folder: text} - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the notes in the linked folder.


__
```
^lists? addcombo ([_a-zA-Z][_a-zA-Z0-9]*) ([_a-zA-Z][ _a-zA-Z0-9]*)$
```
__
```js
let links = $2.split(" ").filter(v => v);
_inlineScripts.state.lists[$1] = { type: "combo", content: links };
return "List __" + $1 + "__ added as a combo-list linked to:\n. " + links.join("\n. ") + "\n\n";
```
__
lists addcombo {list name: name text} {sub list 1: name text, default: ""} {sub list 2: name text, default: ""}... - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.


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
if (!_inlineScripts.state.lists.hasOwnProperty($1))
{
	return "none";
}
return _inlineScripts.state.lists[$1].type;
```
__
hidden - get the type of the list named {list name}.  Useful internally (as a sub-shortcut).
