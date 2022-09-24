Shortcuts for working with virtual cards.

This shortcut-file has a tutorial video available:
[Using the "cards" shortcut-file to use virtual cards](https://www.youtube.com/watch?v=-m4n7d3aKC8) (runtime 9:01)

Here are some good card images you can download to try this out:
[Playing cards](https://opengameart.org/sites/default/files/Playing%20Cards.zip) - Standard playing cards.  Nice quality, though rather large file-size.
[Tarot cards](https://luciellaes.itch.io/rider-waite-smith-tarot-cards-cc0) - Good quality tarot cards under public domain.  I used these in the video tutorial.

Thanks to [luciellaes](https://luciellaes.itch.io) on itch.io for cleaning up and providing the tarot images.

This shortcut-file has supplementary shortcut files:
__cards_pileviewer.sfile__ - Adds an Obsidian panel to view card-decks.
__cards_ui.sfile__ - Graphical UI versions of all of these shortcuts.


__
__
```js
const isTable = /^{?table}?$/i;
function onPileListChanged()
{
	_inlineScripts.inlineScripts.helperFncs.
		callEventListenerCollection(
			"cards.onPileListChanged",
			_inlineScripts.cards.listeners.onPileListChanged);
}
function onPileChanged(pileName)
{
	_inlineScripts.cards.listeners.changedPile = pileName;
	_inlineScripts.inlineScripts.helperFncs.
		callEventListenerCollection(
			"cards.onPileChanged",
			_inlineScripts.cards.listeners.onPileChanged);
}
function getAbsolutePath(path)
{
	if (path.startsWith("data:image")) { return path; }
	path = app.vault.fileMap[path];
	if (!path) { return ""; }
	return app.vault.getResourcePath(path);
}
function createCardUi(card, id, scale, includeDataSrc)
{
	scale ||= 1.0;
	const front = getAbsolutePath(card.path);
	const back =
		getAbsolutePath(_inlineScripts.state.sessionState.cards.backImage);

	const result = document.createElement("img");
	result.classList.add("cardUi");
	result.src = card.isFaceDown ? back : front;
	const size = _inlineScripts.state.sessionState.cards.size;
	result.style.width = (size * scale) + "px";
	result.style.height = (size * card.aspect * scale) + "px";
	if (id != undefined)
	{
		result.dataset.id = id;
	}
	if (includeDataSrc)
	{
		result.dataset.src = front;
	}
	if (!card.isFaceDown)
	{
		switch (card.rotation)
		{
			case 1: result.classList.add("rotated1"); break;
			case 2: result.classList.add("rotated2"); break;
			case 3: result.classList.add("rotated3"); break;
		}
	}
	return result;
}
function createCardUi_inNote(card)
{
	let path = card.path;
	if (card.isFaceDown)
	{
		path = _inlineScripts.state.sessionState.cards.backImage;
	}
	if (path.startsWith("data:image"))
	{
		return createCardUi(card).outerHTML;
	}
	const alt =
		(!card.rotation || card.isFaceDown) ? "" : "rotated" + card.rotation;
	const width = _inlineScripts.state.sessionState.cards.size;
	const height = Math.trunc(width * card.aspect);
	return "![[" + path + "|" + alt + "|" + width + "x" + height + "]]";
}
```
__
Helper scripts


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;

confirmObjectPath("_inlineScripts.state.sessionState.cards.piles");
confirmObjectPath("_inlineScripts.state.sessionState.cards.size", 150);

confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.cards",
	function()
	{
		expand("cards reset");
	});
confirmObjectPath(
	"_inlineScripts.state.listeners.onLoad.cards",
	function()
	{
		for (const pileName in _inlineScripts.state.sessionState.cards.piles)
		{
			const cards =
				_inlineScripts.state.sessionState.cards.piles[pileName].cards;
			for (const card of cards)
			{
				if (card.hasOwnProperty("isRotated"))
				{
					card.rotation = card.isRotated ? 2 : 0;
				}
				delete card.isRotated;
			}
		}
		onPileListChanged();
		onPileChanged(null);
	});

confirmObjectPath("_inlineScripts.cards.listeners.onPileListChanged");
confirmObjectPath("_inlineScripts.cards.listeners.onPileChanged");

_inlineScripts.inlineScripts.helperFncs.addCss("cards", ".rotated1, img[alt*='rotated1'] { transform: rotate(90deg); } .rotated2, img[alt*='rotated2'] { transform: rotate(180deg); } .rotated3, img[alt*='rotated3'] { transform: rotate(270deg); } .iscript_cardChoice { filter: brightness(75%); cursor: pointer; border-width: 4px; border-style: solid;border-color: black; } .iscript_cardChoice:hover { filter:brightness(100%); } .iscript_cardSelected { filter:brightness(100%); border-color: yellow; }");

confirmObjectPath("_inlineScripts.cards.defaultBackImage",
`data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABkCAYAAAAlg3YKAAAACXBIWXMAAA3XAAAN1wFCKJt4AAAEvElEQVR42u3dP2wTZxjH8e9diD2UYFdWKwuQXGpTEjxYSFkSIcuDJYTqZrmweSrZzixlZ2BPh+J3M52ytMILtVQhPFiQhCUS8uCiCKzUA8hSdaoP6OBE5Dr4znFCYiiY5o+f33TRvXlP/uTx+zqPLL0aPWk2nS9HR7kGZICzwGcMR/4BngIln4+fxsa0v7wbmndhWc4s8DMwxnDnJfB9KKQVu0CW5VwBfgG01dVNlpY2ef58k42N4RAZHYXTp3WmpnTGx3UAB7gSCmlFzX1bPQPG7t9/w8OHb4a6fJLJEdLpEYCXPh8x3V1zxlZXN4ceB+DBgzesrm4CnFhf55oOzAAsLW0i6WR5uWvxnQ58DfDihQB56bGI6cBxgPV1gfHSbncvj+vC0T8CJEACJEACJEACJEACJBEgARIgARIgARIgAZIIkAAJkAAJkAAJkABJBOjAAtnVFEr5USpF1d5lQMNEVe7tO9CxwU11j4qaodb9eQ7TVHuODiQqmIk61eLV3QdEFGaEowR0iZTZ5kI1RZnbGInokXiLHfvkT2iYqDWIWwVqzSnimdukIv3w6lSL51lsAvG7mKlL75zHbpiUSwWaAOE5MoYicqjWIGuCC0YbMzuLtfI7dt/BURJGGzMz937z2HnKKxNMZtuYZptsDFaq9UNUQQChswQAAmcJ8XSw87Se0GwWKC1c3xoXnwGihwioT7UEQ49Ya0HnlX9AghOEw/OkjdwHT7EPu9h51GL/Xay7WV2YZ2XBj2IK06yAnae4cL2znlBA1SB88Q+M4F7bYY70pElZ+d3fcccPaJPQLMtxAG7ckC8p9ubmTZ98kpZ/NQRIgARIgARIgARIIkACNBxAB6QPPVCgRjVFUflRyk+xkqdhf8RkEbXVQXxn6lSLJo1PDHTsY//ipWezZM0KAcC28/zZqhMJRI/MW2wg/aCWXYdAlEAgR8LtWm3rEzNHxnT7xNt6y7h959h79KEhHJ8nncoRaJioUqEzRhXA7SVVi/7OHOyYZ9+AIooseR6Xr2I1H9EMz5M1cgTsPOUSTGbbRHZr89XgTLZNqudewmiTcEG2xYIz6c5Yu5qiXL2MkVCY5g9Uiz8S7GnQL4bc5x+kCgpEcqQiuU7VeC8g+IRmfGZ3HIB+93YmtDU2EIzTXHvGXv3mDHcoF+8AcWJp1a3m/VukG3kqjXp30Wz97V4GJwjX7n7cgt2ttq157FaN8OexbaXY6nlGJFXBMCoYk7D4+N4BqKDIZc5UrqJKjwB3jUhEgRzpjEl5wU9p5xq0W/r1oeOwVvZT6q5BXvVE+SoGCwt+Ft01SCm/e2+Ki1k1EKCD3ZNumKi1mYEstv810pP+P7f5T5YD8AUGqSABEiABEiABEiABEiCJAAmQAAmQAAmQAAmQRIAESIAESIAESIAESCJAAjQAoNcAPp9gePH7u5cvdaAOcPKkFJMXz8JxqOvAbwDT0wLkxbPQNEr6xga3gFfj4zrJ5MjQ4ySTI5w7p0Pn8KNb3vFZs8CvuMdnLS93js8altNafD44dUpnelr3cLaOz/IGWZZj0DmA7cSQF9HbB7B5efXK+WJ9nWuaxreOwze45/4MQV47Dk81jZLPx63eI/z+BcWKtwx4PjyWAAAAAElFTkSuQmCC`);
confirmObjectPath("_inlineScripts.state.sessionState.cards.backImage",
	_inlineScripts.cards.defaultBackImage);

confirmObjectPath("_inlineScripts.cards.cardPickerPopup",
{
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		const d = document.createElement("div");
		parent.append(d);
		data.ui = d;
		const cards =
			_inlineScripts.state.sessionState.cards.piles[data.pileId].cards;
		const onclick = function()
		{
			this.classList.toggle("iscript_cardSelected");
			if (data.onclick)
			{
				data.onclick(this);
			}
		};
		for (let i = 0; i < cards.length; i++)
		{
			const img = createCardUi(cards[i], i, 1.0, true)
			d.append(img);
			img.classList.add("iscript_cardChoice");
			img.addEventListener("pointerdown", onclick);
		}
	},
	onClose: async (data, resolveFnc, buttonId) =>
	{
		if (buttonId !== "Ok") { return; }

		let result = [];
		const children = data.ui.children;
		for (var i = 0; i < children.length; i++)
		{
		  if (children[i].classList.contains("iscript_cardSelected"))
		  {
			  result.push(children[i].dataset.id)
		  }
		}
		resolveFnc(result);
	}
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
_inlineScripts.inlineScripts.helperFncs.removeCss("cards");
delete _inlineScripts.cards;
delete _inlineScripts.state?.sessionState?.cards;
delete _inlineScripts.state?.listeners?.onReset?.cards;
delete _inlineScripts.state?.listeners?.onLoad?.cards;
```
__
Shuts down this shortcut-file


__
```
^cards? reset$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;
confirmObjectPath("_inlineScripts.state.sessionState");
_inlineScripts.state.sessionState.cards = { piles: {} };
_inlineScripts.state.sessionState.cards.size = 150;
onPileListChanged();
_inlineScripts.state.sessionState.cards.backImage =
	_inlineScripts.cards.defaultBackImage;
return "All card-piles cleared.\n\n";
```
__
cards reset - Clears all card-piles.


__
```
^cards? backimage ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
const file = app.vault.fileMap[$1];
if (!file || file.children)
{
	return "Back image not set.  File __" + $1 + "__ not found.";
}
_inlineScripts.state.sessionState.cards.backImage = $1;
onPileChanged();
return "Back image set.\n\n";
```
__
cards backimage {file name: path text} - Sets the image to use for the back-side of all cards to {file name}.


__
```
^cards? size ([1-9][0-9]*)$
```
__
```js
_inlineScripts.state.sessionState.cards.size = $1;
onPileChanged();
return "Cards size set to __" + $1 + "__ (pixels).\n\n";
```
__
cards size {card size: >0} - Sets the width for all cards in pixels.  Card height follows.


__
```
^cards?$
```
__
```js
let result = "Card-piles:\n";
const piles = _inlineScripts.state.sessionState.cards.piles;
const names = Object.keys(piles);
if (names.length)
{
	result +=
		". " + names.map(v => (v || "{table}") + " _(" + piles[v].cards.length +
		" cards)_").join("\n. ");
}
else
{
	result += "NONE";
}
return result + "\n\n";
```
__
cards - Lists all card-piles.
***


__
__
```js
function pile_toString(pileId, useHtml)
{
	if (!pileId)
	{
		pileId = "{table}";
	}

	if (!useHtml)
	{
		return " __" + pileId + "__";
	}
	else
	{
		return " <b>" + pileId + "</b>";
	}
}
```
__
Helper scripts


__
```
^cards pile ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
if ($1.match(isTable))
{
	return "Card-Pile not defined.  " +
		"Card-pile __table__, and it's variants, are reserved.\n\n";
}
if (_inlineScripts.state.sessionState.cards.piles[$1])
{
	return "Card-Pile not defined.  " +
		"Card-pile __" + $1 + "__ already exists.\n\n";
}
_inlineScripts.state.sessionState.cards.piles[$1] = { cards: [] };
onPileListChanged();
return "Card-pile __" + $1 + "__ is defined.\n\n";
```
__
cards pile {pile id: name text} - Creates an empty pile {pile id}.

__
```
^cards? fromfolder ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?(up|down|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const folder = app.vault.fileMap[$2];
if (!folder?.children)
{
	return "Cards not created.  Folder __" + $2 + "__ is not valid.\n\n";
}

let cards = [];
for (const child of folder.children)
{
	if (child.children) { continue; }

	// Get image size
	let size = null;
	try
	{
		const buf = await app.vault.readBinary(child);
		let binary = '';
		const bytes = new Uint8Array(buf);
		bytes.forEach(b => binary += String.fromCharCode(b));
		const base64Data = window.btoa(binary);
		size = await new Promise((onDone) =>
		{
			const i = new Image(1,1);
			i.src = `data:${i.type};base64,${base64Data}`;
			i.onload = () =>
			{
				onDone({ w: i.naturalWidth, h: i.naturalHeight });
			}
			i.onerror = () =>
			{
				onDone(null);
			}
		});
	}
	catch(e) {}

	if (size == null)
	{
		return "Cards not created.  Failed on file __" +
			child.name + "__.\n\n";
	}

	const card =
	{
		path: child.path,
		origin: $1,
		aspect: size.h / size.w,
		isFaceDown: ($3 === "down"),
		rotation: 0,
		allowRotated: false,
		allowDuplicate: false
	};
	cards.push(card);
}

let pileModified = false;
if (!_inlineScripts.state.sessionState.cards.piles[$1])
{
	_inlineScripts.state.sessionState.cards.piles[$1] = { cards: [] };
}
else
{
	pileModified = true;
}
_inlineScripts.state.sessionState.cards.piles[$1].cards.push(...cards);
expand("cards shuffle " + $1 + " y");

if (pileModified)
{
	onPileChanged($1);
}
else
{
	onPileListChanged();
}

return "__" +
	cards.length + "__ cards added to the" + pile_toString($1) + " card-pile.\n\n";
```
__
cards fromfolder {pile id: name text, default: table} {folder: path text} {facing: up OR down, default: up} - Creates cards based on images in {folder} and puts them into the {pile id} pile facing {facing}.


__
```
^cards? draw ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?([1-9][0-9]*|all|) ?(up|down|) ?(y|n|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
if ($2.match(isTable)) { $2 = ""; }
const srcPile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!srcPile)
{
	return "Cards not drawn.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
const count = ($3 === "all") ? srcPile.cards.length : (Number($3) || 1);

// Determine which cards to draw
let drawIndices = [];
if ($5 === "y")
{
	let choice = popups.custom(
		"Choose which cards to draw.",
		_inlineScripts.cards.cardPickerPopup,
		{ pileId: $1 });
	if (!choice)
	{
		return "Cards not drawn.  Canceled by user.\n\n";
	}
	drawIndices = choice;
}
else if (count >= srcPile.cards.length)
{
	for (let i = 0; i < srcPile.cards.length; i++)
	{
		drawIndices.push(i);
	}
}
else
{
	let picks = new Set();
	while (picks.size < count)
	{
		picks.add(Math.trunc(Math.random() * srcPile.cards.length));
	}
	drawIndices = [...picks].sort().reverse();
}
if (!drawIndices.length)
{
	return "Cards not drawn.  No cards chosen.\n\n";
}
// Sort numbers
drawIndices.sort((a, b) => a - b);

// Get/create destination pile
let createdDstPile = false;
if (!_inlineScripts.state.sessionState.cards.piles[$2])
{
	_inlineScripts.state.sessionState.cards.piles[$2] = { cards: [] };
	createdDstPile = true;
}
const dstPile = _inlineScripts.state.sessionState.cards.piles[$2];

// Draw all selected cards
let cardDisplay = "";
for (let i = drawIndices.length-1; i >= 0; i--)
{
	let drawnCard = srcPile.cards[drawIndices[i]];
	if (!drawnCard.allowDuplicate)
	{
		srcPile.cards.splice(drawIndices[i], 1);
	}
	else
	{
		drawnCard = Object.assign({}, drawnCard);
		drawnCard.origin = $2;
	}
	if ($4)
	{
		drawnCard.isFaceDown = ($4 === "down");
	}
	dstPile.cards.unshift(drawnCard);
	cardDisplay =
		createCardUi_inNote(drawnCard) + " " + cardDisplay;
}

if ($2 || $3 === "all") { cardDisplay = ""; }
if (cardDisplay) { cardDisplay = "\n" + cardDisplay; }

if (createdDstPile)
{
	onPileListChanged();
}
else
{
	onPileChanged($2);
}
onPileChanged($1);
return "__" +
	drawIndices.length + "__ cards drawn from the" + pile_toString($1) +
	" card-pile to the" + pile_toString($2) + " card-pile." + cardDisplay +
	"\n\n";
```
__
cards draw {source pile id: name text, default: table} {destination pile id: name text, default: table} {count: >0 OR "all", default: 1} {facing: up OR down, default: current} {pick: y OR n, default: n} - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.  If {facing} is given, all moved cards are set to {facing}.  If {pick} is "y", then the user chooses which cards to draw.
	- If a card has "allowDuplicate" turned on, then it is not moved, but copied.  The newly created card has its origin set to {destination pile id}.


__
```
^cards show ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not shown.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
let result = "Card-pile" + pile_toString($1) + ":\n";
for (const card of pile.cards)
{
	result += createCardUi_inNote(card) + " ";
}
return result + "\n\n";
```
__
cards show {pile id: name text, default: table} - Displays all cards in the {pile id} card-pile.
***


__
```
^cards? properties ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?(up|down|) ?(y|n|) ?(y|n|) ?(y|n|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not changed.  " +
		"The" + pile_toString($1) + " card-pile was not found.\n\n";
}
for (let card of pile.cards)
{
	if ($2) { card.isFaceDown = ($2 === "down"); }
	if ($3)
	{
		if (card.allowRotated !== ($3 === "y"))
		{
			card.allowRotated = !card.allowRotated;
			if (card.allowRotated)
			{
				card.rotation =
					(card.aspect == 1) ?
					Math.trunc(Math.random() * 4) :
					Math.trunc(Math.random() * 2) * 2;
			}
			else
			{
				card.rotation = 0;
			}
		}
	}
	if ($4) { card.allowDuplicate = ($4 === "y"); }
	if ($5 === "y")
	{
		card.origin = $1;
	}
}
let changes = [];
if ($2) { changes.push("- facing - " + $2); }
if ($3) { changes.push("- allow rotated - " + ($3 === "y")); }
if ($4) { changes.push("- allow duplicate - " + ($4 === "y")); }
if ($5 === "y") { changes.push("- origin - " + $1); }
if (changes.length)
{
	onPileChanged($1);
	changes = changes.join("\n");
}
else
{
	changes = "NONE";
}
return "All __" + pile.cards.length + "__ cards in the" +
	pile_toString($1) + " card-pile are changed.  Changed properties:\n" +
	changes + "\n\n";
```
__
cards properties {pile id: name text, default: table} {facing: up OR down, default: current} {allow rotated: y OR n, default: current} {allow duplicate: y OR n, default: current} {set origin: y OR n, default: n} - Changes the entered properties for all cards in the {pile id} card-pile.
	- facing - Set all cards to be face-up or face-down.
	- allow rotated -If allowed, each card has a 50/50 chance of being upside-down.
	- allow duplicate - If allowed, cards are copied, instead of moved when drawn.
	- set origin - If true, the origin of each card is set to THIS card-pile.  This is used in the "cards recall" shortcut.
***


__
```
^cards shuffle ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?(y|n|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not shuffled.  The" + pile_toString($1) + " card-pile was not found.\n\n";
}
for (let i = pile.cards.length - 1; i > 0; i--)
{
	const j = Math.floor(Math.random() * (i + 1));
	[pile.cards[i], pile.cards[j]] = [pile.cards[j], pile.cards[i]];
}
for (let card of pile.cards)
{
	if (card.allowRotated)
	{
		card.rotation =
			(card.aspect == 1) ?
			Math.trunc(Math.random() * 4) :
			Math.trunc(Math.random() * 2) * 2;
	}
}
if ($2 !== "y")
{
	onPileChanged($1);
}
return "The" + pile_toString($1) + " card-pile is shuffled.\n\n";
```
__
cards shuffle {pile id: name text, default: table} - Randomizes the card order and rotation for the {pile id} card-pile.


__
```
^cards reverse ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not reversed.  The" + pile_toString($1) + " card-pile was not found.\n\n";
}
pile.cards.reverse();
onPileChanged($1);
return "Cards in the" + pile_toString($1) + " card-pile are reversed.\n\n";
```
__
cards reverse {pile id: name text, default: table} - Reverses the order of the cards in card-pile {pile id}.


__
```
^cards flip ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?([1-9][0-9]*|all|) ?(up|down|) ?(y|n|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not flipped.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
$2 = ($2 === "all") ? pile.cards.length : (Number($2) || 1);
const flipDown = ($3 === "down");
let flipCount = 0;
if ($4 === "y")
{
	let choice = popups.custom(
		"Choose which cards to flip.",
		_inlineScripts.cards.cardPickerPopup,
		{
			pileId: $1,
			onclick: cardUi =>
			{
				if (cardUi.classList.contains("iscript_cardSelected") ===
				    pile.cards[cardUi.dataset.id].isFaceDown)
				{
					cardUi.src = cardUi.dataset.src;
					if (pile.cards[cardUi.dataset.id].rotation)
					{
						cardUi.classList.add(
							"rotated" + pile.cards[cardUi.dataset.id].rotation);
					}
				}
				else
				{
					cardUi.src = getAbsolutePath(
						_inlineScripts.state.sessionState.cards.backImage);
					cardUi.classList.remove("rotated");
				}
			}
		});
	if (!choice)
	{
		return "Cards not flipped.  Canceled by user.\n\n";
	}
	if (!choice.length)
	{
		return "Cards not flipped.  No cards chosen.\n\n";
	}
	flipCount = choice.length;
	for (index of choice)
	{
		pile.cards[index].isFaceDown = !pile.cards[index].isFaceDown;
	}
}
else
{
	for (let i = (flipDown ? pile.cards.length-1 : 0);
	     (flipDown ? i >= 0 : i < pile.cards.length);
	     (flipDown ? i-- : i++))
	{
		const card = pile.cards[i];
		if (card.isFaceDown !== flipDown)
		{
			card.isFaceDown = flipDown;
			flipCount++;
			if (flipCount >= $2)
			{
				break;
			}
		}
	}
}
if (flipCount)
{
	onPileChanged($1);
}

const flipType = ($4 === "y") ? "" : flipDown ? " face-down" : " face-up";
return "__" +
	flipCount + "__ cards flipped" + flipType + " in the" + pile_toString($1) +
	" card-pile.\n\n";
```
__
cards flip {pile id: name text, default: table} {count: >0 OR "all", default: 1} {facing: up OR down, default: up} {pick: y OR n, default: n} - Flips {count} face-down cards to face-up in the {pile id} card-pile.  If {facing} is "down", then flipping is from face-up to face-down.  If {pick} is "y", then the user chooses which cards to flip.


__
```
^cards recall ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?(up|down|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
let result = "";
let moveCount = 0;
let piles = _inlineScripts.state.sessionState.cards.piles;
let pile = piles[$1];
for (const key in piles)
{
	if (piles[key] === pile) { continue; }
	for (let k = piles[key].cards.length - 1; k >= 0; k--)
	{
		if (piles[key].cards[k].origin === $1)
		{
			// We conditionally create the pile HERE because if we did it
			// above, we might create it without ever add a card to it.
			if (!pile)
			{
				pile = piles[$1] = { cards: [] };
			}
			const card = piles[key].cards[k];
			piles[key].cards.splice(k, 1);
			pile.cards.push(card);
			moveCount++;
		}
	}
}
let cardsWereFlipped = false;
if ($2 && pile)
{
	for (let card of pile.cards)
	{
		if (card.isFaceDown !== ($2 === "down"))
		{
			cardsWereFlipped = true;
			card.isFaceDown = ($2 === "down");
		}
	}
}
onPileListChanged();
if (moveCount || cardsWereFlipped)
{
	onPileChanged(null);
}
return result +
	"__" + moveCount + "__ cards were returned to the" + pile_toString($1) +
	" card-pile.\n\n";
```
__
cards recall {pile id: name text, default: table} {facing: up OR down, default: current} - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.  If {facing} is specified, all cards in {pile id} are then put to face {facing}.


__
```
^cards destroy ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) ?(y|n|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not destroyed.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
if ($2 === "y" || popups.confirm(
	"Confirm destroying the" + pile_toString($1, true) + " card-pile."))
{
	delete _inlineScripts.state.sessionState.cards.piles[$1];
	onPileListChanged();
	return "The" + pile_toString($1) + " card-pile is destroyed.\n\n";
}
else
{
	return "Cards not destroyed.  User canceled.\n\n";
}
```
__
cards destroy {pile id: name text, default: table} - Removes the {pile id} card-pile, including all cards within it.
***


__
```
^cards import ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|) (.+)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
let data = null;
try
{
	data = JSON.parse($2);
}
catch (e)
{
	return "Cards not imported.  Failed to parse data string.\n\n";
}
const isNewPile = (!_inlineScripts.state.sessionState.cards.piles[$1]);
_inlineScripts.state.sessionState.cards.piles[$1] = data;
for (let card of _inlineScripts.state.sessionState.cards.piles[$1].cards)
{
	card.isFaceDown ||= false;
	card.allowRotated ||= false;
	card.allowDuplicate ||= false;
	card.rotation ||= 0;
	if (card.hasOwnProperty("isRotated"))
	{
		card.rotation = card.isRotated ? 2 : 0;
	}
	delete card.isRotated;
}
if (isNewPile)
{
	onPileListChanged();
}
else
{
	onPileChanged($1);
}
return "The" + pile_toString($1) + " card-pile was imported.\n\n";
```
__
cards import {pile id: name text, default: table} {data: text} - Makes a card-pile from the data string {data} and remembers it as {pile id}.


__
```
^cards export ?([_a-zA-Z][_a-zA-Z0-9]*|{table}|)$
```
__
```js
if ($1.match(isTable)) { $1 = ""; }
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not exported.  The" + pile_toString($1) + " card-pile was not found.\n\n";
}
let result = JSON.stringify(pile);

// minify
result = result
	.replaceAll(",\"isFaceDown\":false", "")
	.replaceAll(",\"rotation\":0", "")
	.replaceAll(",\"allowRotated\":false", "")
	.replaceAll(",\"allowDuplicate\":false", "")
return "Card-pile" + pile_toString($1) + " export:\n" + result + "\n\n";
```
__
cards export {pile id: name text, default: table} - Expands to a data string containing all date for the {pile id} card-pile.
