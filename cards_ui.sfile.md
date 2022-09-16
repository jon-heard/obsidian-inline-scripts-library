__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
//confirmObjectPath("_inlineScripts.cards_ui.cardPropertiesPopup",
confirmObjectPath("_inlineScripts.cards_ui");
_inlineScripts.cards_ui.cardPropertiesPopup =
{
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		new SettingType(parent)
			.setName("Facing")
			.setDesc("Set all cards to be face-up or face-down.")
			.addDropdown((dropdown) =>
			{
				dropdown.addOptions([ "Don't change", "Face-down", "Face-up" ]);
				data.facing = dropdown;
				return dropdown;
			});
		new SettingType(parent)
			.setName("Width")
			.setDesc("Set the size of all cards, by their width.")
			.addText((text) =>
			{
				text.inputEl.setAttr("placeholder", "Leave blank to not change.");
				data.width = text;
				return text;
			});
		new SettingType(parent)
			.setName("Allow rotated")
			.setDesc(
				"If allowed, each card has a 50/50 chance of being upside-down.")
			.addDropdown((dropdown) =>
			{
				dropdown.addOptions([ "Don't change", "Allow", "Prevent" ]);
				data.allowRotated = dropdown;
				return dropdown;
			});
		new SettingType(parent)
			.setName("Allow duplicated")
			.setDesc("If allowed then cards are copied, instead of moved, when drawn.")
			.addDropdown((dropdown) =>
			{
				dropdown.addOptions([ "Don't change", "Allow", "Prevent" ]);
				data.allowDuplicated = dropdown;
				return dropdown;
			});
		const o = new SettingType(parent)
			.setName("Set origin")
			.setDesc("If true, the origin of each card is set to THIS card-pile.")
			.addToggle((toggle) =>
			{
				data.setOrigin = toggle;
				return toggle;
			});
			o.descEl.innerHTML +=
				"<br/>This is used in the \"cards recall\" shortcut.";
			o.infoEl.parentElement.parentElement.parentElement.style
				["overflow-x"] = "hidden";
	},
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }
		resolveFnc({
			facing:
				data.facing.getValue() === "1" ? "down" :
				data.facing.getValue() === "2" ? "up" : "",
			width: Number(data.width.getValue()) || "",
			allowRotated:
				data.allowRotated.getValue() === "1" ? "y" :
				data.allowRotated.getValue() === "2" ? "n" : "",
			allowDuplicated:
				data.allowDuplicated.getValue() === "1" ? "y" :
				data.allowDuplicated.getValue() === "2" ? "n" : "",
			setOrigin:
				data.setOrigin.getValue() ? "y" : "n"
		});
	}
}
//);
```
__


__
```
^sfile shutdown$
```
__
```js
delete _inlineScripts.cards_ui;
```
__



__
__
```js
userChooseExistingPileId = function(requestMessage, failMessage)
{
	const pileNames =
		Object.keys(_inlineScripts.state.sessionState.cards.piles);
	if (!pileNames.length)
	{
		return [null, failMessage + "  No card-piles to choose from.\n\n"];
	}
	const pileIndex =
		popups.pick("Choose a card-pile " + requestMessage, pileNames);
	if (pileIndex === null)
	{
		return [null, failMessage + "  User canceled.\n\n"];
	}
	return [pileNames[pileIndex], null];
}

userChooseNewOrExistingPileId = function(requestMessage)
{
	const message =
		"Enter the name for a card-pile " + requestMessage +
		".<br/><br/>The name must either be empty, or must..." +
		"<br/>- Contain no spaces" +
		"<br/>- Start with a letter or underscore" +
		"<br/>- Contain only letters, underscores & numbers";
	const regex_confirm = /^[_a-zA-Z][_a-zA-Z0-9]*$/;
	const redoMessage = "Invalid entry #%1.  Try again.<br/><br/>";
	const options = Object.keys(_inlineScripts.state.sessionState.cards.piles);
	let result = popups.input(message, "", options);
	let redoCount = 0;
	while (result && !result.match(regex_confirm))
	{
		redoCount++;
		result = popups.input(
			redoMessage.replace("%1", redoCount) + message, result, options);
	}
	return result;
};
```
__
Helper scripts

__
```
^ui cards? fromfolder$
```
__
```js
// Choose folder
const folders = Object.keys(app.vault.fileMap)
	.filter(v => app.vault.fileMap[v].children?.length)
	.filter(v =>
	{
		for (const child of app.vault.fileMap[v].children)
		{
			if (child.extension && child.extension !== "md")
			{
				return true;
			}
		}
		return false;
	});
let folder = popups.pick("Choose a folder to take card images from", folders);
if (!folder)
{
	return "Card-pile not created.  User canceled.\n\n";
}
folder = folders[folder];

const pileId = await userChooseNewOrExistingPileId("to hold the new cards");
if (pileId === null)
{
	return "Card-pile not created.  User canceled.\n\n";
}

return expand("cards fromfolder " + pileId + " " + folder);
```
__
ui cards fromfolder - Asks the user to choose one of the non-empty folders.
Asks the user to enter a name to give the new card-pile.
Creates a card-pile based on images in the given folder and remembers it with the given name.


__
```
^ui cards? draw$
```
__
```js
// Source pile option
let srcPile =
	await userChooseExistingPileId("to draw from", "Cards not drawn.");
if (!srcPile[0])
{
	return srcPile[1];
}
srcPile = srcPile[0];

// Destination pile option
const dstPile = await userChooseNewOrExistingPileId("to draw into");
if (dstPile === null)
{
	return "Cards not imported.  User canceled.\n\n";
}

// Picking option
let isPicking =
	popups.confirm("Do you want to pick the cards to draw?", [ "no", "yes" ]);
if (isPicking === null)
{
	return "Cards not drawn.  User canceled.\n\n";
}
isPicking = isPicking ? "n" : "y";

// Non-picking options
let facing = "";
let count = "";
if (isPicking === "n")
{
	// Facing option
	facing = popups.pick(
		"Do you want to flip the cards being drawn?",
		[ "Don't flip", "Flip face-up", "Flip face-down" ], 1);
	if (facing === null)
	{
		return "Cards not drawn.  User canceled.\n\n";
	}
	facing =
		(facing == 1) ? "up" :
		(facing == 2) ? "down" :
		"";

	// Count option
	let countOptions = [];
	const max =
		_inlineScripts.state.sessionState.cards.piles[srcPile].cards.length;
	for (let i = 1; i < max-1; i++)
	{
		countOptions.push(i + "");
	}
	countOptions.push("All");
	count = popups.pick("How many cards do you want to draw?", countOptions);
	if (count === null)
	{
		return "Cards not drawn.  User canceled.\n\n";
	}
	count = countOptions[count];
}

return expand(
	"cards draw " + srcPile + " " + dstPile + " " + count + " " + facing + " " +
	isPicking);
```
__
ui cards draw - Asks the user to choose a source card-pile.
Asks the user to enter a destination card-pile.
Asks the user if they want to pick the drawn cards.    If they want to, lets them pick.  Otherwise...
Asks the user if they want to flip, and which way.
Asks the user how many cards to draw.
Draws the cards.


__
```
^ui cards? show$
```
__
```js
const pile =
	await userChooseExistingPileId("to show", "Cards not shown.");
if (!pile[0])
{
	return pile[1];
}
return expand("cards show " + pile[0]);
```
__
ui cards show - Asks the user to choose a card-pile.
Adds images of the cards in the chosen card-pile to the note.


__
```
^ui cards? properties$
```
__
```js
let pile =
	await userChooseExistingPileId("to set properties for", "Cards not set.");
if (!pile[0])
{
	return pile[1];
}
pile = pile[0];

const result = popups.custom(
	"Choose properties for cards in the <b>" + pile + "</b> card-pile.",
	_inlineScripts.cards_ui.cardPropertiesPopup);
if (!result)
{
	return "Cards not set.  User canceled.";
}

return expand(
	"cards properties " + pile + " " + result.facing + " " + result.width + " " +
	result.allowRotated + " " + result.allowDuplicated + " " + result.setOrigin);
```
__
ui cards properties - Asks the user which card-pile to set properties for.
Shows a popup with all properties, each defaulting to "unchanged".  User can change the wanted properties.
When the user is finished, all chosen properties are changed on all cards in the card-pile.


__
```
^ui cards? shuffle$
```
__
```js
const pile =
	await userChooseExistingPileId("to shuffle", "Cards not shuffled.");
if (!pile[0])
{
	return pile[1];
}
return expand("cards shuffle " + pile[0]);
```
__
ui cards shuffle - Asks the user to choose a card-pile.
Randomizes the card-pile's card order and rotation.


__
```
^ui cards? recall$
```
__
```js
const pileId = await userChooseNewOrExistingPileId("to recall");
if (pileId === null)
{
	return "Cards not recalled.  User canceled.\n\n";
}

return expand("cards recall " + pileId);
```
__
ui cards recall - Asks the user to enter a card-pile.
Moves all cards that have the chosen card-pile as their origin, from their current card-pile back into the chosen card-pile.


__
```
^ui cards? flip$
```
__
```js
// Pile option
let pile =
	await userChooseExistingPileId("to flip", "Cards not flipped.");
if (!pile[0])
{
	return pile[1];
}
pile = pile[0];

// Picking option
let isPicking =
	popups.confirm("Do you want to pick the cards to flip?", [ "no", "yes" ]);
if (isPicking === null)
{
	return "Cards not flipped.  User canceled.\n\n";
}
isPicking = isPicking ? "n" : "y";

// Non-picking options
let facing = "";
let count = "";
if (isPicking === "n")
{
	// Facing option
	facing = popups.confirm(
		"Which way do you want to flip cards?",
		[ "Face-down to face-up", "Face-up to face-down" ]);
	if (facing === null)
	{
		return "Cards not flipped.  User canceled.\n\n";
	}
	facing = facing ? "up" : "down";

	// Count option
	let countOptions = [];
	const cards = _inlineScripts.state.sessionState.cards.piles[pile].cards;
	let counter = 0;
	for (const card of cards)
	{
		if (card.isFaceDown === facing)
		{
			counter++;
			countOptions.push(counter + "");
		}
	}
	countOptions.pop(); // The last option is equivalient to "All" so remove it
	countOptions.push("All");
	count = popups.pick(
		"How many cards do you want to flip " + facing + "?",
		countOptions);
	if (count === null)
	{
		return "Cards not flipped.  User canceled.\n\n";
	}
	count = countOptions[count];
}

// Do flip
return expand(
	"cards flip " + pile + " " + count + " " + facing + " " + isPicking);
```
__
ui cards flip - Asks the user to choose a card-pile.
Asks the user if they want to pick the cards to flip.  If they want to, lets them pick.  Otherwise...
Asks the user which wy they want to flip.
Asks the user how many cards to flip.
Flips the cards.


__
```
^ui cards? destroy$
```
__
```js
const pile =
	await userChooseExistingPileId("to destroy", "Cards not destroyed.");
if (!pile[0])
{
	return pile[1];
}
return expand("cards destroy " + pile[0]);
```
__
ui cards destroy - Asks the user to choose a card-pile.
Destroys the chosen card-pile and all cards within it.


__
```
^ui cards? import$
```
__
```js
const pileId = await userChooseNewOrExistingPileId("to import");
if (pileId === null)
{
	return "Cards not imported.  User canceled.\n\n";
}
let defaultDataString =
	JSON.stringify(_inlineScripts.state.sessionState.cards.piles[pileId]);
const importString =
	popups.input("Enter the data string to import", defaultDataString);
if (importString === null)
{
	return "Cards not imported.  User canceled.\n\n";
}

return expand("cards import " + pileId + " " + importString);
```
__
ui cards import - Asks the user to enter a card-pile.
Asks the user to enter the data string to import.
Imports the data string and assigns the new card-pile  to the entered one.


__
```
^ui cards? export$
```
__
```js
const pile =
	await userChooseExistingPileId("to export", "Cards not exported.");
if (!pile[0])
{
	return pile[1];
}
return expand("cards export " + pile[0]);
```
__
ui cards export - Asks the user to choose a card-pile.
Expands to a data string containing all data for the chosen card-pile.
