---
obsidianUIMode: preview
---

An extension to __lists.sfile__ that provides graphical ui versions of shortcuts.


__
```
^lists? list$
```
__
```js
const listNames =
	  Object.keys(_inlineScripts.state.lists)
	  .sort();
if (!listNames.length)
{
	return "No lists available.\n\n";
}

const pick = popups.pick(
	"Choose a list to view", listNames);
if (pick === null) { return null; }

return expand("lists list " + listNames[pick]);
```
__
lists list - Asks user to choose from all lists.
Show all items in that list.


__
```
^lists? add$
```
__
```js
let listNames =
	  Object.keys(_inlineScripts.state.lists)
	  .sort();
let listName = null;
if (listNames.length)
{
	listNames =
		[ "!! New list !!" ].concat(listNames);
	const pick = popups.pick(
		"Choose a list to add to", listNames);
	if (pick === null) { return null; }
	if (!pick)
	{
		listNames = [];
	}
	else
	{
		listName = listNames[pick];
	}
}
if (!listNames.length)
{
	listName = popups.input(
		"Enter a list to add to");
	if (!listName)
	{
		return null;
	}
}

const item = popups.input(
	"Type up an item to add to list <b>" +
	listName + "</b>");
if (!item) { return null; }

return expand(
	"lists add " + listName + " " + item);
```
__
lists add - Asks user to choose from all lists.
Asks the user to enter an item to add to that list.
Allows duplicate items.
    - Can only add to (1) basic lists and (2) combo lists that contain basic lists.


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
function getNamesOfPopulatedLists()
{
	function asyncFilter(arr, predicate)
	{
		const results = await Promise.all(arr.map(predicate));
		return arr.filter((_v, index) => results[index]);
	}
	let result =
		  Object.keys(_inlineScripts.state.lists)
		  .sort();
	result = await asyncFilter(result,
		function(v){ await getListItems(v).length; });
	return result;
}
```
__
getListItems - get the items of a given list, regardless of list type.
getNamesOfPopulatedLists - returns the names of lists that have items.


__
```
^lists? pick$
```
__
```js
const listNames =
	await getNamesOfPopulatedLists();
if (!listNames.length)
{
	return "There are no non-empty lists.\n\n";
}

const pick = popups.pick(
	"Choose a (non-empty) list to pick randomly from",
	listNames);
if (pick === null) { return null; }

return expand("lists pick " + listNames[pick]);
```
__
lists pick - Asks user to choose from all lists.
Gets a random item from that list.


__
```
^lists? remove$
```
__
```js
const listNames =
	await getNamesOfPopulatedLists();
if (!listNames.length)
{
	return "There are no non-empty lists.\n\n";
}

const pick = popups.pick(
	"Choose a list (non-empty) to remove an item from",
	listNames);
if (pick === null) { return null; }

let items = await getListItems(listNames[pick]);
console.log(items);
const pick2 = popups.pick(
	"Choose an item to remove.", items);
if (pick2 === null) { return null; }

return expand(
	"lists remove " + listNames[pick] + " " + items[pick2]);
```
__
lists remove - Asks user to choose from all lists.
Asks the user to choose an item from that list.
Removes the last instance of that item from that list.
    - Can only remove from (1) basic lists and (2) combo lists that contain basic lists.


__
```
^lists? replace$
```
__
```js
const listNames =
	await getNamesOfPopulatedLists();
if (!listNames.length)
{
	return "There are no non-empty lists.\n\n";
}

const pick = popups.pick(
	"Choose a (non-empty) list to replace an item from",
	listNames);
if (pick === null) { return null; }

let items = await getListItems(listNames[pick]);
items = [...new Set(items)];
const pick2 = popups.pick("Choose an item to replace.", items);
if (pick2 === null) { return null; }

let replacement = popups.input(
	"Enter a replacement for \"<b>" +
	items[pick2] + "</b>\".", items[pick2]);
if (!replacement) { return null; }

let item = items[pick2].replaceAll(" ", "\t");
return expand(
	"lists replace " + listNames[pick] + " " + item + " " + replacement);
```
__
lists replace - Asks user to choose from all lists.
Asks the user to choose an item from that list.
Asks user to enter a new value for that item.
Replaces all instances of the item within the list with the replacement.


__
```
^lists? removelist$
```
__
```js
const listNames =
	  Object.keys(_inlineScripts.state.lists)
	  .sort();
if (!listNames.length)
{
	return "No lists available.\n\n";
}

const pick = popups.pick(
	"Choose a list to remove", listNames);
if (pick === null) { return null; }
return expand(
	"lists removelist " + listNames[pick]);
```
__
lists removelist - Asks user to choose from all lists.
Removes that list.


__
```
^lists? addfolder$
```
__
```js
let listName = popups.input("Type the name of the new folder-list.");
if (!listName) { return null; }
listName = listName.replace(" ", "_");

const folders =
	Object.keys(app.vault.fileMap)
	.filter(v => v !== "/" && !v.endsWith(".md"));
const pick = popups.pick("Choose the folder to attach <b>" + listName + "</b> to.", folders);
if (pick === null) { return null; }

return expand("lists addfolder " + listName + " " + folders[pick]);
```
__
lists addfolder - Asks user to type a name for the new folder-list.
Asks user to choose a folder.
The folder-list is created, attached to the folder.


__
```
^lists? addcombo$
```
__
```js
let listName = popups.input("Type the name of the new combo-list.");
if (!listName) { return null; }
listName = listName.replace(" ", "_");

let choices = Object.keys(_inlineScripts.state.lists);
let picks = [];
do
{
	const listsSoFar =
		!picks.length ?
		"" :
		"<br/><br/>Lists so far:</br>" +
			picks.join("<br/>") +
			"<br/><br/>";
	let pick =
		popups.pick("Choose a list to attach " +
		"to the combo-list <b>" + listName +
		"</b>" + listsSoFar,
		choices);
	if (pick === null) { return null; }
	if (!picks.length)
	{
		choices.unshift("!! No more lists !!");
		pick++;
	}
	else if (pick === 0)
	{
		break;
	}
	picks.push(choices[pick]);
}
while (true);

return expand(
	"lists addcombo " + listName +
	" " + picks.join(" "));
```
__
lists addcombo - Ask user to type a name for the new combo-list.
Asks user to choose different lists.
The combo-list is created, attached to the different lists.
