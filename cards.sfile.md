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
function getBackImage()
{
	return _inlineScripts.state.sessionState.cards.backImage ||
	       _inlineScripts.cards.defaultBackImage;
}
function getAbsolutePath(path)
{
	if (path.startsWith("data:image")) { return path; }
	path = app.vault.fileMap[path];
	if (!path) { return ""; }
	return app.vault.getResourcePath(path);
}
function createCardUi(isFaceUp, card, id, scale, includeDataSrc)
{
	scale ||= 1.0;
	const front = getAbsolutePath(card.path);
	const back =
		getAbsolutePath(getBackImage());

	const result = document.createElement("img");
	result.classList.add("cardUi");
	result.src = isFaceUp ? front : back;
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
	if (isFaceUp)
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
function createCardUi_inNote(isFaceUp, card)
{
	let path = card.path;
	if (!isFaceUp)
	{
		path = getBackImage();
	}
	if (path.startsWith("data:image"))
	{
		return createCardUi(isFaceUp, card).outerHTML;
	}
	const alt =
		(!card.rotation || !isFaceUp) ? "" : "rotated" + card.rotation;
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

confirmObjectPath(
	"_inlineScripts.state.sessionState.cards",
	{ piles: {}, size: 150, priorShowMoved: true });

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
		onPileListChanged();
		onPileChanged(null);
	});

confirmObjectPath("_inlineScripts.cards.listeners.onPileListChanged");
confirmObjectPath("_inlineScripts.cards.listeners.onPileChanged");

_inlineScripts.inlineScripts.helperFncs.addCss("cards", ".rotated1, img[alt*='rotated1'] { transform: rotate(90deg); } .rotated2, img[alt*='rotated2'] { transform: rotate(180deg); } .rotated3, img[alt*='rotated3'] { transform: rotate(270deg); } .rotated, img[alt*='rotated'] { transform: rotate(180deg); } .iscript_cardChoice { filter: brightness(75%); cursor: pointer; border-width: 4px; border-style: solid;border-color: black; } .iscript_cardChoice:hover { filter:brightness(100%); } .iscript_cardSelected { filter:brightness(100%); border-color: yellow; }");

confirmObjectPath("_inlineScripts.cards.defaultBackImage",
`data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABkCAYAAAAlg3YKAAAACXBIWXMAAA3XAAAN1wFCKJt4AAAEvElEQVR42u3dP2wTZxjH8e9diD2UYFdWKwuQXGpTEjxYSFkSIcuDJYTqZrmweSrZzixlZ2BPh+J3M52ytMILtVQhPFiQhCUS8uCiCKzUA8hSdaoP6OBE5Dr4znFCYiiY5o+f33TRvXlP/uTx+zqPLL0aPWk2nS9HR7kGZICzwGcMR/4BngIln4+fxsa0v7wbmndhWc4s8DMwxnDnJfB9KKQVu0CW5VwBfgG01dVNlpY2ef58k42N4RAZHYXTp3WmpnTGx3UAB7gSCmlFzX1bPQPG7t9/w8OHb4a6fJLJEdLpEYCXPh8x3V1zxlZXN4ceB+DBgzesrm4CnFhf55oOzAAsLW0i6WR5uWvxnQ58DfDihQB56bGI6cBxgPV1gfHSbncvj+vC0T8CJEACJEACJEACJEACJBEgARIgARIgARIgAZIIkAAJkAAJkAAJkABJBOjAAtnVFEr5USpF1d5lQMNEVe7tO9CxwU11j4qaodb9eQ7TVHuODiQqmIk61eLV3QdEFGaEowR0iZTZ5kI1RZnbGInokXiLHfvkT2iYqDWIWwVqzSnimdukIv3w6lSL51lsAvG7mKlL75zHbpiUSwWaAOE5MoYicqjWIGuCC0YbMzuLtfI7dt/BURJGGzMz937z2HnKKxNMZtuYZptsDFaq9UNUQQChswQAAmcJ8XSw87Se0GwWKC1c3xoXnwGihwioT7UEQ49Ya0HnlX9AghOEw/OkjdwHT7EPu9h51GL/Xay7WV2YZ2XBj2IK06yAnae4cL2znlBA1SB88Q+M4F7bYY70pElZ+d3fcccPaJPQLMtxAG7ckC8p9ubmTZ98kpZ/NQRIgARIgARIgARIIkACNBxAB6QPPVCgRjVFUflRyk+xkqdhf8RkEbXVQXxn6lSLJo1PDHTsY//ipWezZM0KAcC28/zZqhMJRI/MW2wg/aCWXYdAlEAgR8LtWm3rEzNHxnT7xNt6y7h959h79KEhHJ8nncoRaJioUqEzRhXA7SVVi/7OHOyYZ9+AIooseR6Xr2I1H9EMz5M1cgTsPOUSTGbbRHZr89XgTLZNqudewmiTcEG2xYIz6c5Yu5qiXL2MkVCY5g9Uiz8S7GnQL4bc5x+kCgpEcqQiuU7VeC8g+IRmfGZ3HIB+93YmtDU2EIzTXHvGXv3mDHcoF+8AcWJp1a3m/VukG3kqjXp30Wz97V4GJwjX7n7cgt2ttq157FaN8OexbaXY6nlGJFXBMCoYk7D4+N4BqKDIZc5UrqJKjwB3jUhEgRzpjEl5wU9p5xq0W/r1oeOwVvZT6q5BXvVE+SoGCwt+Ft01SCm/e2+Ki1k1EKCD3ZNumKi1mYEstv810pP+P7f5T5YD8AUGqSABEiABEiABEiABEiCJAAmQAAmQAAmQAAmQRIAESIAESIAESIAESCJAAjQAoNcAPp9gePH7u5cvdaAOcPKkFJMXz8JxqOvAbwDT0wLkxbPQNEr6xga3gFfj4zrJ5MjQ4ySTI5w7p0Pn8KNb3vFZs8CvuMdnLS93js8altNafD44dUpnelr3cLaOz/IGWZZj0DmA7cSQF9HbB7B5efXK+WJ9nWuaxreOwze45/4MQV47Dk81jZLPx63eI/z+BcWKtwx4PjyWAAAAAElFTkSuQmCC`);
confirmObjectPath("_inlineScripts.state.sessionState.cards.backImage", null);

//confirmObjectPath("_inlineScripts.cards.cardPickerPopup",
_inlineScripts.cards.cardPickerPopup =
{
	onOpen: async (data, parent, firstButton, SettingType) =>
	{
		data.ui = parent;
		parent.style["overflow-y"] = "scroll";
		parent.parentNode.style.display = "flex";
		parent.parentNode.style["flex-direction"] = "column";
		parent.nextElementSibling.style["margin-top"] = ".5em"
		const onSelected = function()
		{
			this.classList.toggle("iscript_cardSelected");
		};
		const pile = _inlineScripts.state.sessionState.cards.piles[data.pileId];
		for (let i = pile.cards.length - 1; i >= 0; i--)
		{
			const img = createCardUi(pile.isFaceUp, pile.cards[i], i, 1.0, true)
			parent.append(img);
			img.classList.add("iscript_cardChoice");
			img.addEventListener("pointerdown", onSelected);
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
}//);
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
_inlineScripts.state.sessionState.cards =
	{ piles: {}, size: 150, backImage: null, priorShowMoved: true };
onPileListChanged();
return "All card-piles cleared.\n\n";
```
__
cards reset - Clears all card-piles.


__
```
^cards? settings ?([1-9][0-9]*|) ?("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+|default|)$
```
__
```js
// Heading
let result = "The card system's settings are updated:\n";

// Size
let sizeChanged = false;
if ($1 && $1 != _inlineScripts.state.sessionState.cards.size)
{
	_inlineScripts.state.sessionState.cards.size = $1;
	result += ". __Card size__ is changed to __" + $1 + "__ _(pixels)_.\n";
	sizeChanged = true;
}
else
{
	$1 = _inlineScripts.state.sessionState.cards.size;
	result += ". __Card size__ remains __" + $1 + "__ _(pixels)_.\n";
}

// Back image
let backImageFailedToChange = false;
let backImageChanged = false;
if ($2)
{
	if ($2.toLowerCase() === "default")
	{
		if (_inlineScripts.state.sessionState.cards.backImage)
		{
			_inlineScripts.state.sessionState.cards.backImage = null;
			result += ". __Back-image__ reset to default.\n";
			backImageChanged = true;
		}
	}
	else if ($1 !== _inlineScripts.state.sessionState.cards.backImage)
	{
		const file = app.vault.fileMap[$2];
		if (!file)
		{
			result +=
				". __Back-image__ not changed.  File __" + $2 +
				"__ was not found.\n";
			backImageFailedToChange = true;
		}
		else if (file.children)
		{
			result +=
				". __Back-image__ not changed.  __" + $2 + "__ is not a file.\n";
			backImageFailedToChange = true;
		}
		else
		{
			_inlineScripts.state.sessionState.cards.backImage = $2;
			result += ". __Back-image__ changed to __" + $2 + "__.\n";
			backImageChanged = true;
		}
	}
}
if (!backImageChanged && !backImageFailedToChange)
{
	$2 = _inlineScripts.state.sessionState.cards.backImage;
	$2 = $2 ? ("__" + $2 + "__") : "at default";
	result += ". __Back-image__ remains " + $2 + ".\n";
}

// React to changes
if (sizeChanged || backImageChanged) { onPileChanged(); }

// Return
return result + "\n";
```
__
cards settings {size: >0, default: ""} {back-image: path text, default: ""} - Updates the settings for the cards system.
    - __{size}__ - The width for all cards, in pixels.  Card height scales to match.
    - __{back-image}__ - The path to an image file to represent face-down cards, or "default" to reset to the default back-image.
***


__
```
^cards? pile ([_a-zA-Z][_a-zA-Z0-9]*) ?(up|down|) ?(y|n|)$
```
__
```js
if (_inlineScripts.state.sessionState.cards.piles[$1])
{
	return "Card-Pile not created.  " +
		"The __" + $1 + "__ card-pile already exists.\n\n";
}
if ($3)
{
	_inlineScripts.state.sessionState.cards.priorShowMoved = ($3 === "y");
}
const showMoved = _inlineScripts.state.sessionState.cards.priorShowMoved;
_inlineScripts.state.sessionState.cards.piles[$1] =
	{ cards: [], isFaceUp: ($2 === "up"), showMoved };
onPileListChanged();
return "The __" + $1 + "__ card-pile is created.\n\n";
```
__
cards pile {pile id: name text} {facing: up OR down, default: down} {show moved: y OR n, default: prior} - Creates an empty card-pile {pile id} with all cards facing {facing} (face-up or face-down).  If {show moved}, cards that are drawn or picked into the {pile id} card-pile are also printed to the note.


__
```
^cards? remove ([_a-zA-Z][_a-zA-Z0-9]*) ?(y|n|)$
```
__
```js
// Get pile to remove
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Card-pile not removed.  The __" +
		$1 + "__ card-pile was not found.\n\n";
}
// Confirm with user
if (!popups.confirm(
	"Confirm destroying the <b>" + $1 + "</b> card-pile."))
{
	return "Card-pile not removed.  User canceled.\n\n";
}
let result = "";
// Recall cards (if user said to)
if ($2 === "y")
{
	const cardCount = pile.cards.length;
	let recallCount = 0;
	for (let i = pile.cards.length - 1; i >= 0; i--)
	{
		const card = pile.cards[i];
		if (card.origin === $1) { continue; }
		const originPile =
			_inlineScripts.state.sessionState.cards.piles[card.origin];
		if (!originPile) { continue; }
		originPile.cards.push(card);
		pile.cards.splice(i, 1);
		recallCount++;
	}
	result +=
		"__" + recallCount + " / " + cardCount + "__ cards in the __" + $1 +
		"__ card-pile are recalled to other card-piles.\n";
}
//Remove the pile
const cardRemoveCount =
	_inlineScripts.state.sessionState.cards.piles[$1].cards.length;
delete _inlineScripts.state.sessionState.cards.piles[$1];
result += "The __" +
	$1 + "__ card-pile is removed along with __" + cardRemoveCount +
	"__ images.\n";
// Re-origin orphaned cards
let reoriginCount = 0;
for (const key in _inlineScripts.state.sessionState.cards.piles)
{
	const chkPile = _inlineScripts.state.sessionState.cards.piles[key];
	for (const card of chkPile.cards)
	{
		if (card.origin === $1)
		{
			card.origin = key;
			reoriginCount++;
		}
	}
}
if (reoriginCount)
{
	result += "__" + reoriginCount + "__ orphaned cards were re-origined.\n";
}
// Wrapup
onPileListChanged();
return result + "\n";
```
__
cards remove {pile id: name text} {recall: y OR n, default: n} - Removes the {pile id} card-pile, including all cards within it.  If {recall}, all cards in {pile id} are recalled before the {pile id} card-pile is removed.  If the {pile id} card-pile is the origin for any cards, then they are orphaned, and immediately re-origined to their current card-pile.


__
```
^cards? pilesettings ([_a-zA-Z][_a-zA-Z0-9]*) ?(up|down|) ?(y|n|)$
```
__
```js
// Get pile
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Card-Pile not changed.  " +
		"The __" + $1 + "__ card-pile was not found.\n\n";
}

// Heading
let result = "The __" + $1 + "__ card-pile's settings are updated:\n";

// Facing
if ($2 && pile.isFaceUp !== ($2 === "up"))
{
	pile.isFaceUp = !pile.isFaceUp;
	result += ". __Facing__ is changed to __" + $2 + "__.\n";
	onPileChanged();
}
else
{
	result += ". __Facing__ remains __" + (pile.isFaceUp ? "up": "down") + "__.\n";
}

// Show moved
if ($3 && pile.showMoved !== ($3 === "y"))
{
	pile.showMoved = !pile.showMoved;
	result += ". __ShowMoved__ is set to __" + pile.showMoved + "__.\n"; 
}
else
{
	result += ". __ShowMoved__ remains __" + pile.showMoved + "__.\n";
}

// Return
return result + "\n";
```
__
cards pilesettings {pile id: name text} {facing: up OR down, default: current} {show moved: y OR n, default: current} - Updates the settings for the {pile id} card-pile.
    - __{facing}__ - Are the {pile id} card-pile's cards shown face-up or face-down?
    - __{show moved}__ - Are cards that are drawn or picked into the {pile id} card-pile also printed to the note?
***


__
```
^cards? fromfolder ?([_a-zA-Z][_a-zA-Z0-9]*) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not created.  The __" + $1 + "__ card-pile was not found.\n\n";
}
const folder = app.vault.fileMap[$2];
if (!folder)
{
	return "Cards not created.  Folder __" + $2 + "__ was not found.\n\n";
}
if (!folder.children)
{
	return "Cards not created.  __" + $2 + "__ is not a folder.\n\n";
}

async function getImageSize_fast(file)
{
	//file = app.vault.fileMap[file]; // Testing line

	function bytesToInt(v1, v2, v3, v4)
	{
		return (v1 << 24) + (v2 << 16) + (v3 << 8) + v4;
	}

	let result = null;
	const buf = await app.vault.readBinary(file);
	const b = new Uint8Array(buf);
	// PNG file
	if (b[1] === 80 && b[2] === 78 && b[3] === 71)
	{
		result = [ bytesToInt(b[16], b[17], b[18], b[19]),
		           bytesToInt(b[20], b[21], b[22], b[23]) ];
	}
	// GIF file
	else if (b[0] === 71 && b[1] === 73 && b[2] === 70)
	{
		result = [ bytesToInt(0, 0, b[7], b[6]),
		           bytesToInt(0, 0, b[9], b[8]) ];
	}
	// BMP file
	else if (b[0] === 66 && b[1] === 77)
	{
		result = [ bytesToInt(b[21], b[20], b[19], b[18]),
		           bytesToInt(b[25], b[24], b[23], b[22]) ];
	}
	// JPG file (JFIF) - https://stackoverflow.com/a/48488655 & further answers
	else if (b[0] === 255 && b[1] === 216 && b[2] === 255 && b[3] === 224 &&
	         b[6] === 74 && b[7] === 70 && b[8] === 73 && b[9] === 70 && b[10]===0)
	{
		let i = 0;
		while (i < b.length)
		{
			if (b[i] !== 255) { break; }
			while (b[i] === 255) { i++; }
			const mrk = b[i];
			i++;

	        if(mrk === 1) { continue; }   // TEM
	        if(208 <= mrk && mrk <= 215) continue;
	        if(mrk === 216) { continue; } // SOI
	        if(mrk === 217) { break; }    // EOI

	        const len = bytesToInt(0, 0, b[i], b[i+1]);
	        if (192 <= mrk && mrk <= 207) // C0 to CF
	        {
		        i += 3;
				result = [ bytesToInt(0, 0, b[i+2], b[i+3]),
				           bytesToInt(0, 0, b[i+0], b[i+1]) ];
				break;
	        }
	        i += len;
		}
	}
	return { w: result[0], h: result[1] };
}

async function getImageSize_reliable(file)
{
	let result = null;
	try
	{
		const buf = await app.vault.readBinary(file);
		let binary = '';
		const bytes = new Uint8Array(buf);
		bytes.forEach(b => binary += String.fromCharCode(b));
		const base64Data = window.btoa(binary);
		result = await new Promise((onDone) =>
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
	return result;
}

let createCount = 0;
for (let i = folder.children.length - 1; i >= 0; i--)
{
	const child = folder.children[i];
	if (child.children) { continue; }

	// Get image size
	let size = await getImageSize_fast(child);
	if (!size)
	{
		size = await getImageSize_reliable(child);
	}
	if (size == null)
	{
		return "Cards not created.  Failed on file __" +
			child.name + "__.\n\n";
	}

	const card =
	{
		path: child.path,
		rotation: 0,
		aspect: size.h / size.w,
		origin: $1
	};
	pile.cards.push(card);
	createCount++;
}
onPileChanged($1);
return "__" +
	createCount + "__ cards added to the __" + $1 + "__ card-pile.\n\n";
```
__
cards fromfolder {pile id: name text} {folder: path text} - Creates cards based on images in {folder} and puts them into the {pile id} card-pile.
	- Note that this does not randomize the new cards.  Call the "cards shuffle" shortcut to do that.
***


__
```
^cards? list?$
```
__
```js
let result = "Card-piles:\n";
const piles = _inlineScripts.state.sessionState.cards.piles;
const names = Object.keys(piles);
if (names.length)
{
	result +=
		". " + names.map(v => v + " _(" + piles[v].cards.length + " cards)_")
		.join("\n. ");
}
else
{
	result += "NONE";
}
return result + "\n\n";
```
__
cards list - Lists all card-piles.


__
```
^cards? peek ?([1-9][0-9]*|all|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(y|n|)$
```
__
```js
if (!$2 && !_inlineScripts.state.sessionState.cards.priorPeekPile)
{
	return "Cards not peeked.  The card-pile to peek into is not defined.\n\n";
}
if (!$3 && !_inlineScripts.state.sessionState.cards.priorPeekIsBottom)
{
	$3 = "n";
}
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorPeekPile; }
if (!$3) { $3 = _inlineScripts.state.sessionState.cards.priorPeekIsBottom; }
const pile = _inlineScripts.state.sessionState.cards.piles[$2];
if (!pile)
{
	return "Cards not peeked.  The card-pile __" +
		$1 + "__ was not found.\n\n";
}
_inlineScripts.state.sessionState.cards.priorPeekPile = $2;
_inlineScripts.state.sessionState.cards.priorPeekIsBottom = $3;
$3 = ($3 === "y");
const count = ($1.toLowerCase() === "all") ? pile.cards.length : (Number($1) || 1);

let cardView = "\n";
for (let i = 0; i < count; i++)
{
	const index = ($3 ? i : (pile.cards.length - i - 1));
	cardView += createCardUi_inNote(true, pile.cards[index]);
}
return "Cards peeked from the " +
	($3 ? "bottom" : "top") + " of the __" + $2 + "__ card-pile:" + cardView +
	"\n\n";
```
__
cards peek {count: >0 OR "all", default: 1} {pile id: name text, default: prior} {from the bottom: y OR n, default: prior OR n} - Displays the first {count} cards in the {pile id} card-pile, or ALL cards if {count} is "all".  If {from the bottom}, displays the LAST {count} cards instead.
***


__
```
^cards? draw ?([1-9][0-9]*|all|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
if (!$2 && !_inlineScripts.state.sessionState.cards.priorDrawDst)
{
	return "Cards not drawn.  The destination card-pile is not defined.\n\n";
}
if (!$3 && !_inlineScripts.state.sessionState.cards.priorDrawSrc)
{
	return "Cards not drawn.  The source card-pile is not defined.\n\n";
}
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorDrawDst; }
if (!$3) { $3 = _inlineScripts.state.sessionState.cards.priorDrawSrc; }
const dstPile = _inlineScripts.state.sessionState.cards.piles[$2];
if (!dstPile)
{
	return "Cards not drawn.  The destination card-pile __" +
		$2 + "__ was not found.\n\n";
}
const srcPile = _inlineScripts.state.sessionState.cards.piles[$3];
if (!srcPile)
{
	return "Cards not drawn.  The source card-pile __" +
		$3 + "__ was not found.\n\n";
}
_inlineScripts.state.sessionState.cards.priorDrawDst = $2;
_inlineScripts.state.sessionState.cards.priorDrawSrc = $3;
const count =
	($1.toLowerCase() === "all") ? srcPile.cards.length : (Number($1) || 1);

let drawnCount = 0;
let cardView = "\n";
let transfer = [];
for (let i = 0; i < count; i++)
{
	if (!srcPile.cards.length) { break; }
	const drawnCard = srcPile.cards.pop();
	transfer.push(drawnCard);
	drawnCount++;
	cardView += createCardUi_inNote(dstPile.isFaceUp, drawnCard);
}
transfer.reverse();
dstPile.cards.push(...transfer);
cardView = (dstPile.showMoved && dstPile.isFaceUp) ? cardView : "";
const faceDownMsg = dstPile.isFaceUp ? "" : " _(face-down)_"
onPileChanged($2);
onPileChanged($3);
return "__" +
	drawnCount + "__ card(s) drawn from the __" + $3 + "__ card-pile to the __" +
	$2 + "__ card-pile" + faceDownMsg + "." + cardView + "\n\n";
```
__
cards draw {count: >0 OR "all", default: 1} {destination pile id: name text, default: prior} {source pile id: name text, default: prior} - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.


__
```
^cards? pick ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
if (!$1 && !_inlineScripts.state.sessionState.cards.priorPickDst)
{
	return "Cards not picked.  The destination card-pile is not defined.\n\n";
}
if (!$2 && !_inlineScripts.state.sessionState.cards.priorPickSrc)
{
	return "Cards not picked.  The source card-pile is not defined.\n\n";
}
if (!$1) { $1 = _inlineScripts.state.sessionState.cards.priorPickDst; }
if (!$2) { $2 = _inlineScripts.state.sessionState.cards.priorPickSrc; }
const dstPile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!dstPile)
{
	return "Cards not picked.  The destination card-pile __" +
		$1 + "__ was not found.\n\n";
}
const srcPile = _inlineScripts.state.sessionState.cards.piles[$2];
if (!srcPile)
{
	return "Cards not picked.  The source card-pile __" +
		$2 + "__ was not found.\n\n";
}
_inlineScripts.state.sessionState.cards.priorPickDst = $1;
_inlineScripts.state.sessionState.cards.priorPickSrc = $2;

let picks = popups.custom(
	"Pick cards from the <b>" + $2 + "</b> card-pile.",
	_inlineScripts.cards.cardPickerPopup, { pileId: $2 });
if (!picks)
{
	return "Cards not picked.  Canceled by user.\n\n";
}
let cardView = "\n";
let transfer = [];
for (let i = 0; i < picks.length; i++)
{
	if (!srcPile.cards.length) { break; }
	const drawnCard = srcPile.cards.splice(picks[i], 1)[0];
	transfer.push(drawnCard);
	cardView += createCardUi_inNote(dstPile.isFaceUp, drawnCard);
}
transfer.reverse();
dstPile.cards.push(...transfer);
cardView = (dstPile.showMoved && dstPile.isFaceUp) ? cardView : "";
const faceDownMsg = dstPile.isFaceUp ? "" : " _(face-down)_"
onPileChanged($1);
onPileChanged($2);
return "__" +
	picks.length + "__ card(s) picked from the __" + $2 + "__ card-pile to the __" +
	$1 + "__ card-pile" + faceDownMsg + "." + cardView + "\n\n";
```
__
cards pick {destination pile id: name text, default: prior} {source pile id: name text, default: prior} - Has the user choose cards from the {source pile id} card-pile.  Moves the chosen cards into the {destination pile id} card-pile.
***


__
```
^cards? shuffle ?([_a-zA-Z][_a-zA-Z0-9]*) ?(y|n|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not shuffled.  The __" + $1 + "__ card-pile was not found.\n\n";
}
for (let i = pile.cards.length - 1; i > 0; i--)
{
	const j = Math.floor(Math.random() * (i + 1));
	[pile.cards[i], pile.cards[j]] = [pile.cards[j], pile.cards[i]];
}
if ($2 === "y")
{
	for (let card of pile.cards)
	{
		card.rotation =
			(card.aspect == 1) ?
			Math.trunc(Math.random() * 4) :
			Math.trunc(Math.random() * 2) * 2;
	}
}
onPileChanged($1);
return "The __" + $1 + "__ card-pile is shuffled.\n\n";
```
__
cards shuffle {pile id: name text} {rotate: y OR n, default: n} - Randomizes the card order for the {pile id}.  If {rotate}, then card rotations are also randomized.
	- Rotation typically means 0 or 180 degrees (right-side-up or up-side-down), but can also mean 90 or 270 degrees if the card is square.


__
```
^cards? unrotate ?([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not unrotated.  The __" + $1 + "__ card-pile was not found.\n\n";
}
for (let card of pile.cards)
{
	card.rotation = 0;
}
onPileChanged($1);
return "The __" +
	$1 + "__ card-pile's cards are all rotated to 0 degrees (right-side-up).\n\n";
```
__
cards unrotate {pile id: name text} - Sets the rotation for all cards in the {pile id} card-pile to 0 (right-side-up).


__
```
^cards? reverse ?([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not reversed.  The __" + $1 + "__ card-pile was not found.\n\n";
}
pile.cards.reverse();
onPileChanged($1);
return "Cards in the __" + $1 + "__ card-pile have been reversed.\n\n";
```
__
cards reverse {pile id: name text} - Reverses the order of the cards in the {pile id} card-pile.


__
```
^cards? recall ?([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
const piles = _inlineScripts.state.sessionState.cards.piles;
const pile = piles[$1];
if (!pile)
{
	return "Cards not recalled.  The __" + $1 + "__ card-pile was not found.\n\n";
}
let result = "";
let moveCount = 0;
let transfer = [];
for (const key in piles)
{
	if (piles[key] === pile) { continue; }
	for (let k = piles[key].cards.length - 1; k >= 0; k--)
	{
		if (piles[key].cards[k].origin === $1)
		{
			const recalledCard = piles[key].cards.splice(k, 1)[0];
			transfer.push(recalledCard);
			moveCount++;
		}
	}
}
transfer.reverse();
pile.cards.push(...transfer);
if (moveCount)
{
	onPileChanged(null);
}
return result +
	"__" + moveCount + "__ cards were returned to the __" + $1 +
	"__ card-pile.\n\n";
```
__
cards recall {pile id: name text} - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.  If {facing} is specified, all cards in {pile id} are then put to face {facing}.


__
```
^cards? reorigin ?([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Cards not re-origined.  The __" +
		$1 + "__ card-pile was not found.\n\n";
}
for (let card of pile.cards)
{
	card.origin = $1;
}
return "Cards in the __" + $1 + "__ card-pile have been re-origined.\n\n";
```
__
cards reorigin {pile id: name text} - Sets the origin of all cards in the {pile id} card-pile to {pile id}.
***


__
```
^cards? import ([_a-zA-Z][_a-zA-Z0-9]*) (.+)$
```
__
```js
if (!_inlineScripts.state.sessionState.cards.piles[$1])
{
	return "Card-pile not imported.  The __" +
		$1 + "__ card-pile was not found.\n\n";
}
let data = null;
try
{
	data = JSON.parse($2);
}
catch (e)
{
	return "Card-pile not imported.  Failed to parse data string.\n\n";
}
_inlineScripts.state.sessionState.cards.piles[$1] = data;
onPileChanged($1);
return "The __" + $1 + "__ card-pile was imported.\n\n";
```
__
cards import {pile id: name text, default: table} {data: text} - Imports the {data} into the {pile id} card pile.


__
```
^cards? export ([_a-zA-Z][_a-zA-Z0-9]*)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Card-pile not exported.  The __" +
		$1 + "__ card-pile was not found.\n\n";
}
let result = JSON.stringify(pile);
return "Card-pile __" + $1 + "__ export:\n" + result + "\n\n";
```
__
cards export {pile id: name text} - Expands to a data string containing all date for the {pile id} card-pile.
