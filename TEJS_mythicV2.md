Shortcuts for Mythic Variations 2.

Uses __TEJS_state__ shortcut-list (optional) to save & load this shortcut file's state.
Uses __TEJS_lists__ shortcut-list (optional) for pcs, npcs and threads lists.
Both of those shortcut-files should be placed before this one in the settings.

Enter the shortcut "help mythic" for reference.


~~
```
^help mythic$
```
~~
```js
let result = "### MYTHIC SHORTCUTS HELP\n";
result += "- __help mythic__ - Display this help text.\n";
result += "- __reset mythic__ - Reset mythic state to defaults and displays scene heading.\n";
result += "- __mythic details__ - Displays \"details\" mode state.  If \"details\" mode is enabled, expansions include the rolls behind the results.\n";
result += `- __mythic details {state}__ - Sets \"details\" mode based on {state}.
    - If {state} is 'y' then \"details\" mode is enabled
	- if {state} is 'n' then \"details\" mode is disabled.
`;
result += "***\n";
result += "- __detail__ - Make a detail check.\n";
result += "- __event__ - Make an event check.\n";
result += "- __fate {odds} {wanted}__ - Make a fate check based on {odds}: an optional number from -4 (impossible) to 4 (has to be), defaulting to 0 (50/50).  This is also based on {wanted}: an optional value of either 'n' or 'y', defaulting to 'y'.  The value {wanted} specifies the direction of the chaos modifier.\n";
result += "- __f {odds} {wanted}__ - Shorthand for \"fate  {odds} {wanted}\".\n";
result += "- __meaning action__ - Roll on the action meaning table.\n";
result += "- __meaning description__ - Roll on the description meaning table.\n";
result += "- __meaning__ - Shorthand for \"meaning action\".\n";
result += "***\n";
result += "- __listsget {listName}__ - A stub in case TEJS_lists shortcut-file isn't available.\n";
result += "***\n";
result += "- __scene__ - Show the current scene.\n";
result += "- __scene {chaosAdjust}__ - Shift the chaos value by {chaosAdjust} (1, 0 or -1), then increment the current scene.\n";
result += "- __chaos__ - Show the current chaos value.\n";
result += "- __chaos++__ - Increase the chaos value by 1 (maximum of 6).\n";
result += "- __chaos--__ - Decrease the chaos value by 1 (minimum of 3).\n";
result += "- __chaos={value}__ - Set the chaos value to {value}.\n";
result += "***\n";
result += "- __descriptor__ - Generates a personality and activity descriptor for an NPC.\n";
result += "- __disposition {descriptorCount}__ - Rolls for an NPC's disposition, modified by {descriptorCount}.\n";
result += "- __disposition {base} {descriptorCount}__ - Displays the NPC disposition determined by the {base} disposition, modified by {descriptorCount}.\n";
result += "- __action {dispositionAdjust}__ - Makes an NPC behavior check, modified by {dispositionAdjust}, the NPC's disposition.\n";
return result + "\n";
```


~~
```
^tejs setup$
```
~~
```js
window._tejsState ||= {};
window._tejsState.mythic ||= {};
window._tejsState.mythic.chaos ||= 4;
window._tejsState.mythic.scene ||= 1;
window._tejsState.mythic.showDetails ||= false;
window._tejsState.lists ||= {};
window._tejsState.lists.pcs ||= { type: "basic", content: [] };
window._tejsState.lists.npcs ||= { type: "basic", content: [] };
window._tejsState.lists.threads ||= { type: "basic", content: [] };
window._tejsMythicDetails = [];
```


~~
```
^reset mythic$
```
~~
```js
window._tejsState.mythic.chaos= 4;
window._tejsState.mythic.scene = 1;
window._tejsState.lists ||= {};
window._tejsState.lists.pcs = { type: "basic", content: [] };
window._tejsState.lists.npcs = { type: "basic", content: [] };
window._tejsState.lists.threads = { type: "basic", content: [] };
return [ "***\n\n\n### SCENE ", window._tejsState.mythic.scene, "\n__Setup__: " ];
```


~~
```
^mythic details((?: [y|n])?)$
```
~~
```js
if ($1)
{
	window._tejsState.mythic.showDetails = ($1==" y");
}
return [ "Mythic details are ", (window._tejsState.mythic.showDetails ? "ENABLED" : "DISABLED"), "\n\n" ];
```


~~
~~
```js
function roll(name, max)
{
	return addDetails(name, Math.trunc(Math.random() * max + 1));
}
function aPick(name, a) { return a[roll(name, a.length)-1]; }
function aPickWeight(name, a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll("",a[a.length-1][wIndex]);
	addDetails(name, theRoll);
	for (let i = 0; i < a.length; i++)
	{
		if (a[i][wIndex] >= theRoll)
		{
			return a[i];
		}
	}
	return a[a.length-1];
}
function addDetails()
{
	for (let i = 0; i < arguments.length; i+= 2)
	{
		if (!arguments[i]) { continue; }
		window._tejsMythicDetails.push(arguments[i] + "=" + arguments[i+1]);
	}
	return arguments[arguments.length - 1];
}
function getDetails(sameLine)
{
	if (!window._tejsState.mythic.showDetails) { return ""; }
	if (window._tejsMythicDetails.length == 0) { return ""; }
	return (sameLine?"":"\n") + "_" + window._tejsMythicDetails.join(" ") + "_";
}
clearDetailsIfUserTriggered = () =>
{
	if (isUserTriggered) { window._tejsMythicDetails = []; }
}
function getChaosAdjust(multiplier)
{
	let chaos = window._tejsState.mythic.chaos;
	let result = ( (chaos==3) ? 2 : (chaos==6) ? -2 : 0 ) * (multiplier || 1);
	return addDetails("chaosAdjust", result);
}
```


~~
```
^detail$
```
~~
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["ANGER",4],["SADNESS",5],["FEAR",6],["THREAD NEGATIVE",7,"threads"],["PC NEGATIVE",8,"pcs"],["FOCUS NPC",9,"npcs"],["NPC POSITIVE",10,"npcs"],["FOCUS PC",11,"pcs"],["NPC NEGATIVE",12,"npcs"],["FOCUS THREAD",13,"threads"],["PC POSITIVE",14,"pcs"],["THREAD POSITIVE",15,"threads"],["COURAGE",16],["HAPPINESS",17],["CALM",99] ];
let result = roll("roll1",10) + roll("roll2",10) + getChaosAdjust();
result = aPickWeight("", outcomes, 1, result);
let focus = getExpansion("listsget " + (result[2] || ""));
focus = (focus.length <= 3) ? "" : (" (" + focus[1] + ")");
return [ "__Detail__\n", result[0], focus, getDetails(), "\n\n" ];
```


~~
```
^listsget ([a-zA-Z]*)$
```
~~
```js
return [];
```


~~
```
^chaos$
```
~~
```js
return [ "Chaos is ", window._tejsState.mythic.chaos, ".\n\n" ];
```


~~
```
^chaos=([3-6])$
```
~~
```js
window._tejsState.mythic.chaos = $1;
return [ "Chaos set to ", $1, ".\n\n" ];
```


~~
```
^chaos--$
```
~~
```js
window._tejsState.mythic.chaos--;
if (window._tejsState.mythic.chaos < 3)
{
	window._tejsState.mythic.chaos = 3;
	return [ "Chaos remains at 3 (minimum).", "\n\n" ];
}
return [ "Chaos is lowered to " + window._tejsState.mythic.chaos + ".", "\n\n" ];
```


~~
```
^chaos\+\+$
```
~~
```js
window._tejsState.mythic.chaos++;
if (window._tejsState.mythic.chaos > 6)
{
	window._tejsState.mythic.chaos = 6;
	return [ "Chaos remains at 6 (maximum).", "\n\n" ];
}
return [ "Chaos is raised to " + window._tejsState.mythic.chaos + ".", "\n\n" ];
```


~~
```
^meaning(| action)$
```
~~
```js
clearDetailsIfUserTriggered();
let value1 = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
let value2 = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
let result = aPick("actionRoll1", value1) + " _(of)_ " + aPick("actionRoll2", value2);
return [ "__Meaning (action)__\n", result, getDetails(), "\n\n" ];
```


~~
```
^meaning description$
```
~~
```js
clearDetailsIfUserTriggered();
let value1 = ["ABNORMALLY","ADVENTUROUSLY","AGGRESSIVELY","ANGRILY","ANXIOUSLY","AWKWARDLY","BEAUTIFULLY","BLEAKLY","BOLDLY","BRAVELY","BUSILY","CALMLY","CAREFULLY","CARELESSLY","CAUTIOUSLY","CEASELESSLY","CHEERFULLY","COMBATIVELY","COOLLY","CRAZILY","CURIOUSLY","DAINTILY","DANGEROUSLY","DEFIANTLY","DELIBERATELY","DELIGHTFULLY","DIMLY","EFFICIENTLY","ENERGETICALLY","ENORMOUSLY","ENTHUSIASTICALLY","EXCITEDLY","FEARFULLY","FEROCIOUSLY","FIERCELY","FOOLISHLY","FORTUNATELY","FRANTICALLY","FREELY","FRIGHTENINGLY","FULLY","GENEROUSLY","GENTLY","GLADLY","GRACEFULLY","GRATEFULLY","HAPPILY","HASTILY","HEALTHILY","HELPFULLY","HELPLESSLY","HOPELESSLY","INNOCENTLY","INTENSELY","INTERESTINGLY","IRRITATINGLY","JOVIALLY","JOYFULLY","JUDGEMENTALLY","KINDLY","KOOKILY","LAZILY","LIGHTLY","LOOSELY","LOUDLY","LOVINGLY","LOYALLY","MAJESTICALLY","MEANINGFULLY","MECHANICALLY","MISERABLY","MOCKINGLY","MYSTERIOUSLY","NATURALLY","NEATLY","NICELY","ODDLY","OFFENSIVELY","OFFICIALLY","PARTIALLY","PEACEFULLY","PERFECTLY","PLAYFULLY","POLITELY","POSITIVELY","POWERFULLY","QUAINTLY","QUARRELSOMELY","QUIETLY","ROUGHLY","RUDELY","RUTHLESSLY","SLOWLY","SOFTLY","SWIFTLY","THREATENINGLY","VERY","VIOLENTLY","WILDLY","YIELDINGLY"];
let value2 = ["ABANDONED","ABNORMAL","AMUSING","ANCIENT","AROMATIC","AVERAGE","BEAUTIFUL","BIZARRE","CLASSY","CLEAN","COLD","COLORFUL","CREEPY","CUTE","DAMAGED","DARK","DEFEATED","DELICATE","DELIGHTFUL","DIRTY","DISAGREEABLE","DISGUSTING","DRAB","DRY","DULL","EMPTY","ENORMOUS","EXOTIC","FADED","FAMILIAR","FANCY","FAT","FEEBLE","FEMININE","FESTIVE","FLAWLESS","FRESH","FULL","GLORIOUS","GOOD","GRACEFUL","HARD","HARSH","HEALTHY","HEAVY","HISTORICAL","HORRIBLE","IMPORTANT","INTERESTING","JUVENILE","LACKING","LAME","LARGE","LAVISH","LEAN","LESS","LETHAL","LONELY","LOVELY","MACABRE","MAGNIFICENT","MASCULINE","MATURE","MESSY","MIGHTY","MILITARY","MODERN","EXTRAVAGANT","MUNDANE","MYSTERIOUS","NATURAL","NONDESCRIPT","ODD","PALE","PETITE","POOR","POWERFUL","QUAINT","RARE","REASSURING","REMARKABLE","ROTTEN","ROUGH","RUINED","RUSTIC","SCARY","SIMPLE","SMALL","SMELLY","SMOOTH","SOFT","STRONG","TRANQUIL","UGLY","VALUABLE","WARLIKE","WARM","WATERY","WEAK","YOUNG"];
let result = aPick("descriptorRoll1", value1) + " " + aPick("descriptorRoll2", value2);
return [ "__Meaning (description)__\n", result, getDetails(), "\n\n" ];
```


~~
```
^event$
```
~~
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35,"pcs",true],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];
let result = aPickWeight("eventRoll", outcomes);
let focus = getExpansion("listsget " + (result[2] || ""));
focus = (focus.length <= 3) ? "" : (" (" + focus[1] + ")");
let meaning = getExpansion("meaning " + (result[3] ? "description" : "action"))[1];
return [ "__Event__\n", result[0], focus, " - ", meaning, getDetails(), "\n\n"];
```


~~
```
^scene$
```
~~
```js
return [ "The current scene is ", window._tejsState.mythic.scene, ".\n\n" ];
```


~~
```
^scene (1|-1|0)$
```
~~
```js
clearDetailsIfUserTriggered();
let result = "";
if ($1 == 1)
{
	result += getExpansion("chaos++")[0] + "\n";
}
else if ($1 == -1)
{
	result += getExpansion("chaos--")[0] + "\n";
}
result += "***\n\n\n\n";
window._tejsState.mythic.scene++;
result += "### SCENE " + window._tejsState.mythic.scene;
let sceneCheckResults = "";
let chk = roll("sceneCheck", 10);
if (chk <= window._tejsState.mythic.chaos)
{
	sceneCheckResults =
		((chk % 2) ?
		 "__Scene modified__" :
		 ("__Scene replaced__\n__Event__ - " +
		  getExpansion("event")[1])) +
		sceneCheckResults;
}
let details = getDetails(!sceneCheckResults);
if (details)
{
	sceneCheckResults += details;
}
if (sceneCheckResults)
{
	sceneCheckResults = "\n***\n" + sceneCheckResults;
}
return [ result, sceneCheckResults, "\n***\n__Setup__: " ];
```


~~
```
^f(?:ate)?[ ]?(|[0-4]|(?:-[0-4]))[ ]?([y|n]?)$
```
~~
```js
clearDetailsIfUserTriggered();
let odds = ["impossible","no way","very unlikely","unlikely","50/50","likely","very likely","sure thing","has to be"];
$1 = Number($1 || 0);
let fateRoll1 = roll("fateRoll1", 10);
let fateRoll2 = roll("fateRoll2", 10);
let chaosRoll = roll("chaosRoll", 10);
let result =
	fateRoll1 + fateRoll2 +
	addDetails("oddsAdjust", $1 * 2) +
	getChaosAdjust($2 == "n" ? -1 : 1);
result = result > 10 ? "YES" : "NO";

let chaos = window._tejsState.mythic.chaos;
let isExtreme = (chaosRoll <= chaos) && !!(fateRoll1 % 2) && !!(fateRoll2 % 2);
let isEvent = (chaosRoll <= chaos) && !(fateRoll1 % 2) && !(fateRoll2 % 2);
if (chaosRoll < chaos && fateRoll1 == fateRoll2) isExtreme = isEvent = true;

result = (isExtreme ? "EXTREME " : "") + result;
let evtText = isEvent ? ( "\n__Event__ - " + getExpansion("event")[1] ) : "";
return [ "__Fate check (", odds[$1+4], ")__\n", result, evtText, getDetails(), "\n\n" ];
```


~~
```
^descriptor$
```
~~
```js
clearDetailsIfUserTriggered();
return [ "__Descriptor__\n", "Personality: ", getExpansion("meaning description")[1], "\nActivity: ", getExpansion("meaning action")[1], getDetails(), "\n\n" ];
```


~~
```
^disposition (-?[0-3])$
```
~~
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let base = roll("roll1", 10) + roll("roll2", 10);
let result = base + addDetails("descriptorAdjust", Number($1) * 2);
result = aPickWeight("", outcomes, 1, result)[0] + " - BASE=" + base;
return [ "__Disposition__\n", result, getDetails(), "\n\n" ];
```


~~
```
^disposition ([0-2]?[0-9]) (-?[0-3])$
```
~~
```js
clearDetailsIfUserTriggered();
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let result =
		addDetails("base", Number($1)) +
		addDetails("descriptorAdjust", Number($2) * 2);
result = aPickWeight("", outcomes, 1, result);
return [ "__Disposition__\n", result[0], getDetails(), "\n\n" ];
```


~~
```
^action (-2|0|2|4)$
```
~~
```js
clearDetailsIfUserTriggered();
let outcomes1 = [ ["NEW ACTION BASED ON THEME",3],["CONTINUE CURRENT ACTION",5],["CONTINUE CURRENT ACTION, +2 DISPOSITION",6],["CONTINUE CURRENT ACTION, -2 DISPOSITION",7],[false,8,0],[false,9,-4],[false,10,4] ];
let outcomes2 = [ ["TALK / EXPOSITION",6],["NEUTRAL, DISCONNECTED ACTION",8],["ACTION BENEFITS THE PC",10],["GIVE SOMETHING TO THE PC",11],["TRY TO END ENCOUNTER",12],["CHANGE THEME",13],["CHANGE \"%1\" DESCRIPTOR. IT IS ACTIVATED & DECIDES NEXT ACTION.",14,true],["ACT OUT OF SELF INTEREST",17],["TAKE SOMETHING",18],["HURT THE PC",99] ];
let descriptors = ["IDENTITY","PERSONALITY","ACTIVITY"];
result = aPickWeight("table1Roll", outcomes1);
if (!result[0])
{
	result =
		roll("table2Roll1", 10) + roll("table2Roll2", 10) +
		addDetails("dispositionAdjust", Number($1)) +
		addDetails("tableAdjust", result[2]);
	result = aPickWeight("", outcomes2, 1, result);
	if (result[2])
	{
		result[0] = result[0].replace("%1", aPick("descriptorRoll", descriptors));
	}
	result[0] += "\n+2/0/-2 DISPOSITION TO MATCH ACTION.";
}
return [ "__Action__\n", result[0], getDetails(), "\n\n" ];
```
