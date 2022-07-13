---
obsidianUIMode: preview
---

Shortcuts for Mythic Game Master Emulator.  Mythic GME is an excellent "GM emulator" system for solo and GM'less gaming.  It was designed by Tana Pigeon.  You can find more info about Mythic GME at [wordmill games](http://wordmillgames.com/mythic-game-master-emulator.html).

Incompatible with __tejs_mythicv2__.  If __tejs_mythicv2__ comes before __tejs_mythicgme__ in the shortcut-list, then __tejs_mythicgme__ will be disabled.

Uses __tejs_state__ shortcut-file (optional).
It uses this to save & load the chaos value and the scene count.

Uses __tejs_lists__ shortcut-file (optional).
Adds and uses lists for pcs, npcs and threads.
This requires that __tejs_lists__ comes before __tejs_mythicgme__ in the shortcut-list.
If the pc, npc and thread lists have items, then the __event__ and __detail__ shortcuts will incorporate them into their results.


~~
```
^tejs setup$
```
~~
```js
if (expand("help").contains("\n    - mythicv2"))
{
	print("The mythicgme shortcut-file is disabled as it is incompatible with the mythicv2 shortcut-file.");
	return true;
}

window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.mythicgme ||= {};
window._tejs.state.mythicgme.chaos ||= 5;
window._tejs.state.mythicgme.scene ||= 1;

window._tejs.state.lists ||= {};
window._tejs.state.lists.pcs ||= { type: "basic", content: [] };
window._tejs.state.lists.npcs ||= { type: "basic", content: [] };
window._tejs.state.lists.threads ||= { type: "basic", content: [] };

window._tejs.listeners ||= {};
window._tejs.listeners.state ||= {};
window._tejs.listeners.state.onReset ||= {};
window._tejs.listeners.state.onReset.mythicgme ||= expand =>
{
	expand("reset mythicgme");
};
```
~~
Disables mythicgme if mythicv2 is already registered.  Sets up a state variable for mythicgme.  Sets up lists for mythicgme.  Sets up a callback for the state "reset" event in order to to reset mythicgme.


~~
```
^tejs shutdown$
```
~~
```js
delete window._tejs.listeners?.state?.onReset?.mythicgme;
```
~~
Unregisters event callbacks.


~~
```
^reset mythicgme$
```
~~
```js
window._tejs ||= {};
window._tejs.state ||= {};
window._tejs.state.mythicgme ||= {};
window._tejs.state.mythicgme.chaos = 5;
window._tejs.state.mythicgme.scene = 1;
window._tejs.state.lists ||= {};
window._tejs.state.lists.pcs = { type: "basic", content: [] };
window._tejs.state.lists.npcs = { type: "basic", content: [] };
window._tejs.state.lists.threads = { type: "basic", content: [] };
return "***\n\n\n### SCENE " + window._tejs.state.mythicgme.scene + "\n__Setup__: ";
```
~~
reset mythicgme - Reset mythic state to defaults and displays scene heading.
***


~~
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (const item of a)
	{
		if (item[wIndex] >= theRoll)
		{
			return item;
		}
	}
	return a.last();
}
```
~~
Some useful functions


~~
```
^f(?:ate)?[ ]?(|[0-6]|(?:-[1-4]))$
```
~~
```js
const ODDS = ["impossible","no way","very unlikely","unlikely","50/50","somewhat likely","likely","very likely","near sure thing","sure thing","has to be"];
const FATE_CHART = [
[ [ 10, 50, 91],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81],[  0,  0, 81],[  0,-20, 77] ],
[ [ 15, 75, 96],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82],[  0,  0, 81] ],
[ [ 16, 85, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82],[  1,  5, 82] ],
[ [ 18, 90, 99],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  4, 20, 85],[  3, 15, 84],[  2, 10, 83],[  1,  5, 82] ],
[ [ 19, 95,100],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86],[  3, 15, 84],[  2, 10, 83] ],
[ [ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90],[  5, 25, 86],[  4, 20, 85] ],
[ [ 20,100,  0],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91],[  7, 35, 88],[  5, 25, 86] ],
[ [ 21,105,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 15, 75, 96],[ 13, 65, 94],[ 10, 50, 91],[  9, 45, 90] ],
[ [ 23,115,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 80, 97],[ 15, 75, 96],[ 11, 55, 92],[ 10, 50, 91] ],
[ [ 25,125,  0],[ 22,110,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97],[ 13, 65, 94],[ 11, 55, 92] ],
[ [ 26,145,  0],[ 26,130,  0],[ 20,100,  0],[ 20,100,  0],[ 19, 95,100],[ 19, 95,100],[ 18, 90, 99],[ 16, 85, 97],[ 16, 80, 97] ] ];
$1 = Number($1 || 0);
const r = roll(100);
const ranges = FATE_CHART[$1+4][9-window._tejs.state.mythicgme.chaos];
let result =
	(r <= ranges[0]) ? "EXTEREME YES" :
	(r <= ranges[1]) ? "YES" :
	(r <= ranges[2]) ? "NO" :
	"EXTREME NO";
let eventOutput = "";
if (Math.trunc(r/10) == r%10 &&
    r%10 < window._tejs.state.mythicgme.chaos)
{
	eventOutput =
		"\n    event - " + expand("event")[1];
}
return "Fate check (" + ODDS[$1+4] + ")\n    " + result + eventOutput + "\n\n";
```
~~
fate {odds} - Make a fate check based on {odds}: an optional number from -4 (impossible) to 6 (has to be), defaulting at 0 (50/50).
        Alternative shortcut: __f {odds}__.


~~
```
^scene$
```
~~
```js
return "The current scene is " + window._tejs.state.mythicgme.scene + ".\n\n";
```
~~
scene - Show the current scene.


~~
```
^scene (1|-1)$
```
~~
```js
let result =
	($1 === "1") ? expand("chaos++")[0] :
	($1 === "-1") ? expand("chaos--")[0] :
	"Chaos is unchanged at " + expand("chaos")[1];
result += "\n\n\n***\n\n\n";
window._tejs.state.mythicgme.scene++;
result += "### SCENE " + window._tejs.state.mythicgme.scene;
let chk = roll(10);
if (chk <= window._tejs.state.mythicgme.chaos)
{
	if (chk % 2)
	{
		result += "\n   __Scene replaced__\n       event - " + expand("event")[1];
	}
	else
	{
		result += "\n   __Scene modified__";
	}
}
return result + "\n   __setup__\n        ";
```
~~
scene {chaosAdjust} - Shift the chaos value by {chaosAdjust} (1, 0 or -1), then increment the current scene and run a scene check.
***


~~
```
^event$
```
~~
```js
const FOCUS_TABLE = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];
let result = aPickWeight(FOCUS_TABLE);
let focus = expand("lists pick " + (result[2] || ""));
focus = (focus.length === 1) ? "" : (" (" + focus[1] + ")");
let meaning = expand("meaning")[1];
return [ "Event:\n    ", result[0] + focus + " - " + meaning, "\n\n" ];
```
~~
event - Make an event check.


~~
```
^meaning$
```
~~
```js
const ACTION_TABLE = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
const SUBJECT_TABLE = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
let result = aPick(ACTION_TABLE) + " _(of)_ " + aPick(SUBJECT_TABLE);
return [ "Meaning (action):\n    ", result, "\n\n" ];
```
~~
meaning - Roll on the meaning tables.
***


~~
```
^chaos$
```
~~
```js
return [ "Chaos is __", window._tejs.state.mythicgme.chaos, "__.\n\n" ];
```
~~
chaos - Show the current chaos value.


~~
```
^chaos--$
```
~~
```js
window._tejs.state.mythicgme.chaos--;
if (window._tejs.state.mythicgme.chaos < 1)
{
	window._tejs.state.mythicgme.chaos = 1;
	return [ "Chaos remains at __1__ (hit minimum).", "\n\n" ];
}
return [ "Chaos is lowered to __" + window._tejs.state.mythicgme.chaos + "__.", "\n\n" ];
```
~~
chaos-- - Decrease the chaos value by 1 (minimum of 1).


~~
```
^chaos\+\+$
```
~~
```js
window._tejs.state.mythicgme.chaos++;
if (window._tejs.state.mythicgme.chaos > 9)
{
	window._tejs.state.mythicgme.chaos = 9;
	return [ "Chaos remains at __9__ (hit maximum).", "\n\n" ];
}
return [ "Chaos is raised to __" + window._tejs.state.mythicgme.chaos + "__.", "\n\n" ];
```
~~
chaos++ - Increase the chaos value by 1 (maximum of 9).


~~
```
^chaos=([1-9])$
```
~~
```js
window._tejs.state.mythicgme.chaos = $1;
return "Chaos set to __" + $1 + "__.\n\n";
```
~~
chaos={value} - Set the chaos value to {value}, an integer from 1 to 9.


~~
```
^lists? pick ((?:[_a-zA-Z][_a-zA-Z0-9]*)?)((?: [1-9][0-9]*)?)$
```
~~
```js
return [];
```
~~
hidden - A placeholder shortcut in case the shortcut-file tejs_lists isn't available.
