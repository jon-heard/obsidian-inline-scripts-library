Shortcuts for working with lists.

Uses __tejs_state__ shortcut-file (optional) to save & load this shortcut-file's state.

The tejs_state shortcut-file should be placed before this one in the settings.


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.lists ||= {};
```
~~
Sets up a variable to store lists in the global tejs state variable.


~~
```
^reset lists$
```
~~
```js
window._tejsState ||= {};
window._tejsState.lists = {};
return "All lists cleared.\n\n";
```
~~
reset lists - Clear all lists.


~~
~~
```js
function getListItems(name)
{
	let list = window._tejsState.lists[name];
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
				if (key.startsWith(list.content))
				{
					key = key.substr(list.content.length);
					if (key.contains("/")) { continue; }
					if (!key.endsWith(".md")) { continue; }
					result.push("[[" + key.substring(0, key.length-3) + "]]");
				}
			}
			break;
		case "combo":
			for (let i = 0; i < list.content.length; i++)
			{
				result = result.concat(getListItems(list.content[i]));
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
^lists$
```
~~
```js
let listNames = Object.keys(window._tejsState.lists);
listNames.sort();
for (let i = 0; i < listNames.length; i++)
{
	let items = getListItems(listNames[i]);
	let listType = window._tejsState.lists[listNames[i]].type;
	if (listType != "basic")
	{
		listNames[i] += " _(" + listType + ")_";
	}
	listNames[i] = "- " + listNames[i] + "\n      " + (items.length ? items.join(", ") : "EMPTY");
}
return [ "__Lists__\n", (listNames.length ? listNames.join("\n") : "NONE"), "\n\n" ];
```
~~
lists - Show all list and all items for each list.


~~
```
^lists list ([_a-zA-Z]+)$
```
~~
```js
let items = getListItems($1);
items = (items && items.length ? items.join(", ") : "none");
return [ "__List \"", $1, "\"__\n", items, "\n\n" ];
```
~~
lists list {list name} - Show all items in the list {list name}.


~~
```
^lists add ([_a-zA-Z]+) (.+)$
```
~~
```js
window._tejsState.lists[$1] ||= { type: "basic", content: [] };
if (window._tejsState.lists[$1].type != "basic")
{
	return [ "Failed to add to list \"", $1, "\".  Not a basic list.\n\n" ];
}
window._tejsState.lists[$1].content.push($2);
window._tejsState.lists[$1].content.sort();
return ["\"", $2, "\" added to list \"", $1, "\".\n\n" ];
```
~~
lists add {list name} {item} - Add {item} to the list {list name}.  Allows duplicate items.  Cannot add to folder-lists or combo-lists.


~~
```
^lists pick ([_a-zA-Z]*)$
```
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
let items = getListItems($1);
if (items && items.length)
{
	let result = items[roll(items.length)-1];
	return [ "\"", result, "\" picked from list \"", $1, "\".\n\n" ];
}
return [ "Failed to pick from list \"", $1, "\".  List is empty.\n\n" ];
```
~~
lists pick {list name} - Get a random item from the list {list name}.


~~
```
^lists remove ([_a-zA-Z]+) (.+)$
```
~~
```js
if (window._tejsState.lists[$1])
{
	if (window._tejsState.lists[$1].type != "basic")
	{
		return [ "Failed to remove \"", $2, "\" from list \"", $1, "\".  Not a basic list.\n\n" ];
	}
	let i = window._tejsState.lists[$1].content.lastIndexOf($2);
	if (i >= 0)
	{
	    window._tejsState.lists[$1].content.splice(i, 1);
	    return [ "\"", $2, "\" removed from list \"", $1, "\".\n\n" ];
	}
}
return [ "Failed to remove \"", $2, "\" from list \"", $1, "\".  Not found in list.\n\n" ];
```
~~
lists remove {list name} {item} - Remove an instance of {item} from the list {list name}.  Cannot remove from folder-lists or combo-lists.


~~
```
^lists removelist ([_a-zA-Z]+)$
```
~~
```js
if (window._tejsState.lists[$1])
{
	delete window._tejsState.lists[$1];
	return [ "List \"", $1, "\" removed.\n\n" ];
}
return [ "Failed to remove list \"", $1, "\".  List does not exist.\n\n" ];
```
~~
lists removelist {list name} - Remove the entire list {list name}.


~~
```
^lists addfolder ([_a-zA-Z]+) (.+)$
```
~~
```js
if (!$2.endsWith("/")) { $2 += "/"; }
window._tejsState.lists[$1] = { type: "folder", content: $2 };
return [ "List \"", $1, "\" added as a folder-list that is linked to the folder \"", $2, "\".\n\n" ];
```
~~
lists addfolder {list name} {folder} - Create a folder-list named {list name} that is linked to the folder {folder}.  A "folder-list" is a list who's items are the names of the files in the linked folder.


~~
```
^lists addcombo ([_a-zA-Z]+) ([a-zA-Z ]+)$
```
~~
```js
let links = $2.split(" ");
window._tejsState.lists[$1] = { type: "combo", content: links };
return [ "List \"", $1, "\" added as a combo-list linked to: ", links.join(", "), ".\n\n" ];
```
~~
lists addcombo {list name} {sub list 1} {sub list 2}... - Create a combo-list named {list name} that is linked to the sublists given as {sub list 1}, {sub list 2}, etc.  A "combo-list" is a list who's items are all of the items of its linked sublists.
