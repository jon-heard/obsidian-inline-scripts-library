Shortcuts for working with virtual cards.  Almost all of these shortcuts work on piles of cards, rather than invidiual cards.


__
```
^sfile setup$
```
__
```js
const confirmObjectPath =
	_inlineScripts.inlineScripts.helperFncs.confirmObjectPath;

confirmObjectPath("_inlineScripts.state.sessionState.cards.piles");

confirmObjectPath(
	"_inlineScripts.state.listeners.onReset.cards",
	function()
	{
		expand("cards reset");
	});

_inlineScripts.inlineScripts.helperFncs.addCss("cards", "img[alt*=', rotated']{ transform: scaleX(-1) scaleY(-1); } .cardChoice { opacity: .75; cursor: pointer; border-width: 4px; border-style: solid;border-color: black; } .cardChoice:hover { opacity: 1; } .cardSelected { opacity: 1; border-color: yellow; }");

confirmObjectPath("_inlineScripts.cards.backImage",
`data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAEgAAABkCAYAAAAlg3YKAAAACXBIWXMAAA3XAAAN1wFCKJt4AAAEvElEQVR42u3dP2wTZxjH8e9diD2UYFdWKwuQXGpTEjxYSFkSIcuDJYTqZrmweSrZzixlZ2BPh+J3M52ytMILtVQhPFiQhCUS8uCiCKzUA8hSdaoP6OBE5Dr4znFCYiiY5o+f33TRvXlP/uTx+zqPLL0aPWk2nS9HR7kGZICzwGcMR/4BngIln4+fxsa0v7wbmndhWc4s8DMwxnDnJfB9KKQVu0CW5VwBfgG01dVNlpY2ef58k42N4RAZHYXTp3WmpnTGx3UAB7gSCmlFzX1bPQPG7t9/w8OHb4a6fJLJEdLpEYCXPh8x3V1zxlZXN4ceB+DBgzesrm4CnFhf55oOzAAsLW0i6WR5uWvxnQ58DfDihQB56bGI6cBxgPV1gfHSbncvj+vC0T8CJEACJEACJEACJEACJBEgARIgARIgARIgAZIIkAAJkAAJkAAJkABJBOjAAtnVFEr5USpF1d5lQMNEVe7tO9CxwU11j4qaodb9eQ7TVHuODiQqmIk61eLV3QdEFGaEowR0iZTZ5kI1RZnbGInokXiLHfvkT2iYqDWIWwVqzSnimdukIv3w6lSL51lsAvG7mKlL75zHbpiUSwWaAOE5MoYicqjWIGuCC0YbMzuLtfI7dt/BURJGGzMz937z2HnKKxNMZtuYZptsDFaq9UNUQQChswQAAmcJ8XSw87Se0GwWKC1c3xoXnwGihwioT7UEQ49Ya0HnlX9AghOEw/OkjdwHT7EPu9h51GL/Xay7WV2YZ2XBj2IK06yAnae4cL2znlBA1SB88Q+M4F7bYY70pElZ+d3fcccPaJPQLMtxAG7ckC8p9ubmTZ98kpZ/NQRIgARIgARIgARIIkACNBxAB6QPPVCgRjVFUflRyk+xkqdhf8RkEbXVQXxn6lSLJo1PDHTsY//ipWezZM0KAcC28/zZqhMJRI/MW2wg/aCWXYdAlEAgR8LtWm3rEzNHxnT7xNt6y7h959h79KEhHJ8nncoRaJioUqEzRhXA7SVVi/7OHOyYZ9+AIooseR6Xr2I1H9EMz5M1cgTsPOUSTGbbRHZr89XgTLZNqudewmiTcEG2xYIz6c5Yu5qiXL2MkVCY5g9Uiz8S7GnQL4bc5x+kCgpEcqQiuU7VeC8g+IRmfGZ3HIB+93YmtDU2EIzTXHvGXv3mDHcoF+8AcWJp1a3m/VukG3kqjXp30Wz97V4GJwjX7n7cgt2ttq157FaN8OexbaXY6nlGJFXBMCoYk7D4+N4BqKDIZc5UrqJKjwB3jUhEgRzpjEl5wU9p5xq0W/r1oeOwVvZT6q5BXvVE+SoGCwt+Ft01SCm/e2+Ki1k1EKCD3ZNumKi1mYEstv810pP+P7f5T5YD8AUGqSABEiABEiABEiABEiCJAAmQAAmQAAmQAAmQRIAESIAESIAESIAESCJAAjQAoNcAPp9gePH7u5cvdaAOcPKkFJMXz8JxqOvAbwDT0wLkxbPQNEr6xga3gFfj4zrJ5MjQ4ySTI5w7p0Pn8KNb3vFZs8CvuMdnLS93js8altNafD44dUpnelr3cLaOz/IGWZZj0DmA7cSQF9HbB7B5efXK+WJ9nWuaxreOwze45/4MQV47Dk81jZLPx63eI/z+BcWKtwx4PjyWAAAAAElFTkSuQmCC`);

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
			this.classList.toggle("cardSelected");
			if (data.onclick)
			{
				data.onclick(this);
			}
		};
		for (let i = 0; i < cards.length; i++)
		{
			const card = cards[i];
			const img = document.createElement("img");
			d.append(img);
			img.classList.add("cardChoice");
			img.dataset.id = i;
			img.dataset.src =
				"app://local/" + app.vault.adapter.basePath + "/" + card.path;
			img.addEventListener("pointerdown", onclick);
			img.src =
				card.isFaceDown ?
				_inlineScripts.cards.backImage :
				img.dataset.src;
			img.style.width = (card.width + 8) + "px";
			img.style.height = ((card.width + 8) * card.aspect) + "px";
		}
	},
	onClose: async (data, resolveFnc, buttonText) =>
	{
		if (buttonText === "Ok")
		{
			let result = [];
			const children = data.ui.children;
			for (var i = 0; i < children.length; i++)
			{
			  if (children[i].classList.contains("cardSelected"))
			  {
				  result.push(children[i].dataset.id)
			  }
			}
			resolveFnc(result);
		}
		else
		{
			resolveFnc(null);
		}
		resolveFnc((buttonText === "Ok") ? [ 1, 3, 5 ] : null);
	}
});
```
__
Setup this shortcut-file


__
```
^sfile shutdown$
```
__
```js
_inlineScripts.inlineScripts.helperFncs.removeCss("cards");
delete _inlineScripts.state.sessionState.cards;
delete _inlineScripts.cards;
delete _inlineScripts.state?.listeners?.onReset?.cards;
```
__
Shutdown this shortcut-file


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
return "All card-piles cleared.\n\n";
```
__
cards reset - Clear all card-piles.


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
		". " + names.map(v => v + " _(" + piles[v].cards.length +
		" cards)_").join("\n. ");
}
else
{
	result += "NONE";
}
return result + "\n\n";
```
__
cards - List all card-piles.
***


__
__
```js
function pile_toString(pileId, useHtml)
{
	if (!pileId)
	{
		return "";
	}
	else if (!useHtml)
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
Useful functions.


__
```
^cards? fromfolder ?([_a-zA-Z][_a-zA-Z0-9]*|) ("[^ \t\\:*?"<>|][^\t\\:*?"<>|]*"|[^ \t\\:*?"<>|]+) ?(up|down|)$
```
__
```js
const folder = app.vault.fileMap[$2];
if (!folder?.children)
{
	return "Card-pile not created.  Folder __" + $2 + "__ is not valid.\n\n";
}

let cards = [];
for (const child of folder.children)
{
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
		});
	}
	catch(e) {}

	if (size == null)
	{
		return "Card-pile not created.  Failed on file __" +
			child.name + "__.\n\n";
	}

	const card =
	{
		path: child.path,
		origin: $1,
		width: size.w,
		aspect: size.h / size.w,
		isFaceDown: ($3 === "down"),
		isRotated: false,
		allowRotated: false,
		allowDuplicate: false
	};
	cards.push(card);
}

_inlineScripts.state.sessionState.cards.piles[$1] = { cards };
expand("cards shuffle " + $1);

return "The" + pile_toString($1) + " card-pile is created.\n\n";
```
__
cards fromfolder {pile id: name text, default: ""} {folder: path text} {facing: up OR down, default: up} - Creates a card-pile based on images in {folder} and remembers it as {pile id}.


__
```
^cards? draw ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([1-9][0-9]*|all|) ?(up|down|) ?(y|n|)$
```
__
```js
const srcPile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!srcPile)
{
	return "No cards drawn.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
$3 = ($3 === "all") ? srcPile.cards.length : (Number($3) || 1);
let drawIndices = [];
if ($5 === "y")
{
	let choice = popups.custom(
		"Choose which cards to draw.",
		_inlineScripts.cards.cardPickerPopup,
		{ pileId: $1 });
	if (!choice)
	{
		return "No cards drawn.  Canceled by user.";
	}
	if (!choice.length)
	{
		return "No cards drawn.  No cards chosen.";
	}
	for (index of choice)
	{
		drawIndices.add(index);
	}
}
else if ($3 >= srcPile.cards.length)
{
	for (let i = 0; i < srcPile.cards.length; i++)
	{
		drawIndices.add(i);
	}
}
else
{
	let picks = new Set();
	while (picks.size < $3)
	{
		picks.add(Math.trunc(Math.random() * srcPile.cards.length));
	}
	drawIndices = [...picks].sort().reverse();
}
const dstPile =
	_inlineScripts.state.sessionState.cards.piles[$2] ||= { cards: [] };
for (let i = 0; i < drawIndices.length; i++)
{
	let drawing = srcPile.cards[drawIndices[i]];
	if (!drawing.allowDuplicate)
	{
		srcPile.cards.splice(drawIndices[i], 1);
	}
	else
	{
		drawing = Object.assign({}, drawing);
		drawing.origin = $2;
	}
	dstPile.cards.push(drawing);
	if ($5)
	{
		drawing.isFaceDown = ($5 === "down");
	}
}

if (!srcPile.cards.length)
{
	delete _inlineScripts.state.sessionState.cards.piles[$1];
	return "The" +
		pile_toString($1) + " card-pile was entirely drawn into the" +
		pile_toString($3) + " card-pile.  It was then removed as empty.\n\n";
}
else
{
	return "__" +
		drawIndices.length + "__ cards draw from the" + pile_toString($1) +
		" card-pile to the" + pile_toString($3) + " card-pile.\n\n";
}
```
__
cards draw {source pile id: name text, default: ""} {destination pile id: name text, default: ""} {count: >0 OR "all", default: 1} {facing: up OR down, default: current} {pick: y OR n, default: n} - Removes {count} cards from the {source pile id} card-pile and adds them to the {destination pile id} card-pile.  If {facing} is given, all moved cards are set to {facing}.  If {pick} is "y", then the user chooses which cards to draw.
	- If a card has "allowDuplicate" turned on, then it is not moved, but copied.  The newly created card has its origin set to {destination pile id}.


__
```
^cards show ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "No cards shown.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
let result = "Card-pile" + pile_toString($1) + ":\n";
for (const card of pile.cards)
{
	if (!card.isFaceDown)
	{
		result += "![[" + card.path + "|";
		result +=
			card.path.slice(
			card.path.lastIndexOf("/")+1, card.path.lastIndexOf("."));
		if (card.isRotated)
		{
			result += ", rotated";
		}
		result += "|" + card.width + "]] ";
	}
	else
	{
		result +=
			"<img src='" + _inlineScripts.cards.backImage + "' style='width:" +
			card.width + ";height:" + (card.width * card.aspect) + ";'/> ";
	}
}
return result + "\n\n";
```
__
cards show {pile id: name text, default: ""} - Displays all cards in the {pile id} card-pile.
***


__
```
^cards? properties ?([_a-zA-Z][_a-zA-Z0-9]*|) ?(up|down|) ?([1-9][0-9]*|) ?(y|n|) ?(y|n|) ?(y|n|)$
```
__
```js
$3 = Number($3);
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "No cards changed.  " +
		"The" + pile_toString($1) + " card-pile was not found.\n\n";
}
for (let card of pile.cards)
{
	if ($2) { card.isFaceDown = ($2 === "down"); }
	if ($3) { card.width = $3; }
	if ($4)
	{
		if (card.allowRotated !== ($4 === "y"))
		{
			card.allowRotated = !card.allowRotated;
			if (card.allowRotated)
			{
				card.isRotated = (Math.random() > .5);
			}
			else
			{
				card.isRotated = false;
			}
		}
	}
	if ($5) { card.allowDuplicate = ($5 === "y"); }
	if ($6 === "y")
	{
		card.origin = $1;
	}
}
let changes = [];
if ($2) { changes.push("- facing - " + $2); }
if ($3) { changes.push("- size - " + $3); }
if ($4) { changes.push("- allow rotationed - " + ($4 === "y")); }
if ($5) { changes.push("- allow duplicate - " + ($5 === "y")); }
if ($6 === "y") { changes.push("- origin - " + $1); }
changes = changes.length ? changes.join("\n") : "NONE";
return "All Cards in the" +
	pile_toString($1) + " card-pile are changed.  Changed properties:\n" +
	changes + "\n\n";
```
__
cards properties {pile id: name text, default: ""} {facing: up OR down, default: current} {width: >0, default: current} {allow rotated: y OR n, default: current} {allow duplicate: y OR n, default: current} {reset origin: y OR n, default: n} - Changes the entered properties for all cards in the {pile id} card-pile.
	- facing - Determines whether the card is shown face-up or face-down.
	- width - Sets the size of the card by it's width.  The height adjusts to match the width.
	- allow rotated - If true, the card has a 50/50 chance of being upside down.
	- allow duplicate - If true, this card is copied when "drawn", rather than moved.
	- reset origin - If true, the "origin" of each card is set to THIS card-pile.  The origin is used by "cards reset" to determine what cards are moved where.
***


__
```
^cards shuffle ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "No cards shown.  The" + pile_toString($1) + " card-pile was not found.\n\n";
}
for (let i = pile.cards.length - 1; i > 0; i--) {
	const j = Math.floor(Math.random() * (i + 1));
	[pile.cards[i], pile.cards[j]] = [pile.cards[j], pile.cards[i]];
}
for (let card of pile.cards)
{
	if (card.allowRotated)
	{
		card.isRotated = (Math.random() > .5);
	}
}
return "The" + pile_toString($1) + " card-pile is shuffled.\n\n";
```
__
cards shuffle {pile id: name text, default: ""} - Randomizes the card order in the {pile id} card-pile.


__
```
^cards reset ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
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
	if (!piles[key].cards.length)
	{
		result += "The" + pile_toString(key) +
		" card-pile was entirely drawn into the" + pile_toString($1) +
		" card-pile, then removed as empty.\n";
		delete piles[key];
	}
}
return result +
	"__" + moveCount + "__ cards were returned to the" + pile_toString($1) +
	" card-pile.\n\n";
```
__
cards reset {pile id: name text, default: ""} - Moves all cards that have the {pile id} card-pile as their origin, from their current card-piles back into the {pile id} card-pile.


__
```
^cards flip ?([_a-zA-Z][_a-zA-Z0-9]*|) ?([1-9][0-9]*|all|) ?(up|down|) ?(y|n|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "No cards flipped.  The" +
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
				if (cardUi.classList.contains("cardSelected") ===
				    pile.cards[cardUi.dataset.id].isFaceDown)
				{
					cardUi.src = cardUi.dataset.src;
				}
				else
				{
					cardUi.src = _inlineScripts.cards.backImage;
				}
			}
		});
	if (!choice)
	{
		return "No cards flipped.  Canceled by user.";
	}
	if (!choice.length)
	{
		return "No cards flipped.  No cards chosen.";
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
const flipType = ($4 === "y") ? "" : flipDown ? " face-down" : " face-up";
return "__" +
	flipCount + "__ cards flipped" + flipType + " in the" + pile_toString($1) +
	" card-pile.\n\n";
```
__
cards flip {pile id: name text, default: ""} {count: >0 OR "all", default: 1} {facing: up OR down, default: up} {pick: y OR n, default: n} - Flips {count} face-down cards to face-up in the {pile id} card-pile.  If {facing} is "down", then flipping is from face-up to face-down.  If {pick} is "y", then the user chooses which cards to flip.


__
```
^cards destroy ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Pile not destroyed.  The" +
		pile_toString($1) + " card-pile was not found.\n\n";
}
if (popups.confirm(
	"Confirm destroying the" + pile_toString($1, true) + " card-pile."))
{
	delete _inlineScripts.state.sessionState.cards.piles[$1];
	return "The" + pile_toString($1) + " card-pile is destroyed.\n\n";
}
else
{
	return "Card-pile not destroyed.  User canceled.\n\n";
}
```
__
cards destroy {pile id: name text, default: ""} - Remove the {pile id} card-pile, including all cards within it.
***


__
```
^cards import ?([_a-zA-Z][_a-zA-Z0-9]*|) (.+)$
```
__
```js
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
for (let card of _inlineScripts.state.sessionState.cards.piles[$1].cards)
{
	card.isFaceDown ||= false;
	card.isRotated ||= false;
	card.allowRotated ||= false;
	card.allowDuplicate ||= false;
}
return "The" + pile_toString($1) + " card-pile was imported.\n\n";
```
__
cards import {pile id: name text, default: ""} {data: text} - Make a card-pile from the data string {data} and remember it as {pile id}.


__
```
^cards export ?([_a-zA-Z][_a-zA-Z0-9]*|)$
```
__
```js
const pile = _inlineScripts.state.sessionState.cards.piles[$1];
if (!pile)
{
	return "Card-pile not exported.  The" + pile_toString($1) + " card-pile was not found.\n\n";
}
let result = JSON.stringify(pile);

// minify
result = result
	.replaceAll(",\"isFaceDown\":false", "")
	.replaceAll(",\"isRotated\":false", "")
	.replaceAll(",\"allowRotated\":false", "")
	.replaceAll(",\"allowDuplicate\":false", "")
return "Card-pile" + pile_toString($1) + " export:\n" + result + "\n\n";
```
__
cards export {pile id: name text, default: ""} - Create a data string containing all date for the {pile id} card-pile.
