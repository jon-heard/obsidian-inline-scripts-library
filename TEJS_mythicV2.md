Shortcuts for Mythic Variations 2.
Uses TEJS_state to save & load this shortcut file's state.

Enter the shortcut "state help" for reference.

~~
^help mythic$
~~
let result = "### MYTHIC SHORTCUTS HELP\n";
result += "- __help mythic__ - Display this help text.\n";
result += "- __reset mythic__ - Reset mythic state to defaults and displays scene heading.\n";
result += "---\n";
result += "- __detail__ - Make a details check.\n";
result += "- __event__ - Make an event check.\n";
result += "- __fate {odds} {wanted}__ - Make a fate check based on {odds}: an optional number from -4 (impossible) to 4 (has to be), defaulting in 0 (50/50).  This is also based on {wanted}: an optional value of either 'n' or 'y', defaulting to 'y'.  The value {wanted} specifies the direct of the chaos modifier.\n";
result += "- __meaning action__ - Roll on the action meaning table.\n";
result += "- __meaning discription__ - Roll on the description meaning table.\n";
result += "- __meaning__ - Roll on the action meaning table. (short for \"meaning action\")\n";
result += "---\n";
result += "- __list__ - Show all lists.\n";
result += "- __list {listName}__ - Show all items in the list {listName}.\n";
result += "- __listadd {listName} {item}__ - Add {item} to the end of the list {listName}.\n";
result += "- __listget {listName}__ - Get a random item from the list named {listName}.\n";
result += "- __listremove {listName} {item}__ - Remove last instance of {item} from the list {listName}.\n";
result += "- __listremoveall {listName}__ - Remove the entire list of {listName}.\n";
result += "---\n";
result += "- __scene__ - Show the current scene.\n";
result += "- __scene {chaosAdjust}__ - Shift the chaos value by {chaosAdjust} (1 or -1), then increment the current scene.\n";
result += "- __chaos__ - Show the current chaos value.\n";
result += "- __chaos++__ - Increase the chaos value by 1 (maximum of 6).\n";
result += "- __chaos--__ - Decrease the chaos value by 1 (minimum of 3).\n";
result += "- __chaos={value}__ - Set the chaos value to {value}.\n";
result += "---\n";
result += "- __descriptor__ - Rolls for a personality and activity.\n";
result += "- __disposition {descriptorCount}__ - Rolls for a disposition, modified by {descriptorCount}.\n";
result += "- __disposition {base} {descriptorCount}__ - Displays a disposition given by the {base} disposition, modified by {descriptorCount}.\n";
result += "- __action {dispositionAdjust}__ - Action check, modified by {dispositionAdjust}.\n";
return result + "\n";

~~
^tejs setup$
~~
window._tejsState ||= {};
window._tejsState.mythic ||= {};
window._tejsState.mythic.chaos ||= 4;
window._tejsState.mythic.scene ||= 1;
window._tejsState.mythic.lists ||= {};
window._tejsState.mythic.lists.pcs ||= [];
window._tejsState.mythic.lists.npcs ||= [];
window._tejsState.mythic.lists.threads ||= [];

~~
^reset mythic$
~~
window._tejsState.mythic.chaos= 4;
window._tejsState.mythic.scene = 1;
window._tejsState.mythic.lists = {};
window._tejsState.mythic.lists.pcs = [];
window._tejsState.mythic.lists.npcs = [];
window._tejsState.mythic.lists.threads = [];
return "─".repeat(20) + "\n\n\n### SCENE 1\n__Setup__: ";

~~
~~
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a[a.length-1][wIndex]);
	for (let i = 0; i < a.length; i++)
	{
		if (a[i][wIndex] >= theRoll)
		{
			return a[i];
		}
	}
	return a[a.length-1];
}

~~
^detail$
~~
let outcomes = [ ["ANGER",4],["SADNESS",5],["FEAR",6],["THREAD NEGATIVE",7,"threads"],["PC NEGATIVE",8,"pcs"],["FOCUS NPC",9,"npcs"],["NPC POSITIVE",10,"npcs"],["FOCUS PC",11,"pcs"],["NPC NEGATIVE",12,"npcs"],["FOCUS THREAD",13,"threads"],["PC POSITIVE",14,"pcs"],["THREAD POSITIVE",15,"threads"],["COURAGE",16],["HAPPINESS",17],["CALM",99] ];
let roll1 = roll(10);
let roll2 = roll(10);
let chaos = window._tejsState.mythic.chaos;
let chaosAdjust = (chaos==3) ? 2 : (chaos==6) ? -2 : 0;
let result = roll1 + roll2 + chaosAdjust;
result = aPickWeight(outcomes, 1, roll1 + roll2 + chaosAdjust);
let list = window._tejsState.mythic.lists[result[2]];
result = result[0] + ((list && list.length) ? (" (" + aPick(list) + ")") : "");
return "__Detail__\n" + result + "\n_roll1=" + roll1 + ",roll2=" + roll2 + ",chaosAdjust=" + chaosAdjust + "_\n\n";


~~
^list$
~~
let listNames = Object.keys(window._tejsState.mythic.lists);
return "__Lists__\n" + (listNames.length ? listNames.join(", ") : "none") + "\n\n";

~~
^list ([a-zA-Z]+)$
~~
let list = window._tejsState.mythic.lists[$1];
return "__List \"" + $1 + "\"__\n" + (list && list.length ? list.join(", ") : "none") + "\n\n";

~~
^listadd ([a-zA-Z]+) ([a-zA-Z ]+)$
~~
window._tejsState.mythic.lists[$1] ||= [];
window._tejsState.mythic.lists[$1].push($2);
return "\"" + $2 + "\" added to list \"" + $1 + "\".\n\n";

~~
^listget ([a-zA-Z]+)$
~~
console.log("wtf");
let list = window._tejsState.mythic.lists[$1];
if (list && list.length)
{
	let result = list[roll(list.length)-1];
	return "\"" + result + "\" picked from list \"" + $1 + "\".\n\n";
}
return "Failed to pick from list \"" + $1 + "\".  List is empty.\n\n";


~~
^listremove ([a-zA-Z]+) ([a-zA-Z ]+)$
~~
if (window._tejsState.mythic.lists[$1])
{
	let i = window._tejsState.mythic.lists[$1].lastIndexOf($2);
	if (i >= 0)
	{
	    window._tejsState.mythic.lists[$1].splice(i, 1);
	    return "\"" + $2 + "\" removed from list \"" + $1 + "\".\n\n";
	}
}
return "Failed to remove \"" + $2 + "\" from list \"" + $1 + "\".  Not found in list.\n\n";


~~
^listremoveall ([a-zA-Z]+)$
~~
if (window._tejsState.mythic.lists[$1])
{
	delete window._tejsState.mythic.lists[$1];
	return "List \"" + $1 + "\" removed.\n\n";
}
return "Failed to remove list \"" + $1 + "\".  List does not exist.\n\n";


~~
^chaos$
~~
return "Chaos is " + window._tejsState.mythic.chaos + ".\n\n";

~~
^chaos=([3-6])$
~~
window._tejsState.mythic.chaos = $1;
return "Chaos set to " + $1 + ".\n\n";

~~
~~
function chaosDown()
{
	window._tejsState.mythic.chaos--;
	if (window._tejsState.mythic.chaos < 3)
	{
		window._tejsState.mythic.chaos = 3;
		return "Chaos remains at 3 (minimum).\n\n";
	}
	return "Chaos lowered to " + window._tejsState.mythic.chaos + ".\n\n";
}
function chaosUp()
{
	window._tejsState.mythic.chaos++;
	if (window._tejsState.mythic.chaos > 6)
	{
		window._tejsState.mythic.chaos = 6;
		return "Chaos remains at 6 (maximum).\n\n";
	}
	return "Chaos raised to " + window._tejsState.mythic.chaos + ".\n\n";
}

~~
^chaos--$
~~
return chaosDown();

~~
^chaos\+\+$
~~
return chaosUp();

~~
~~
function rollAction()
{
	let value1 = ["ATTAINMENT","STARTING","NEGLECT","FIGHT","RECRUIT","TRIUMPH","VIOLATE","OPPOSE","MALICE","COMMUNICATE","PERSECUTE","INCREASE","DECREASE","ABANDON","GRATIFY","INQUIRE","ANTAGONISE","MOVE","WASTE","TRUCE","RELEASE","BEFRIEND","JUDGE","DESERT","DOMINATE","PROCRASTINATE","PRAISE","SEPARATE","TAKE","BREAK","HEAL","DELAY","STOP","LIE","RETURN","IMMITATE","STRUGGLE","INFORM","BESTOW","POSTPONE","EXPOSE","HAGGLE","IMPRISON","RELEASE","CELEBRATE","DEVELOP","TRAVEL","BLOCK","HARM","DEBASE","OVERINDULGE","ADJOURN","ADVERSITY","KILL","DISRUPT","USURP","CREATE","BETRAY","AGREE","ABUSE","OPPRESS","INSPECT","AMBUSH","SPY","ATTACH","CARRY","OPEN","CARELESSNESS","RUIN","EXTRAVAGANCE","TRICK","ARRIVE","PROPOSE","DIVIDE","REFUSE","MISTRUST","DECEIVE","CRUELTY","INTOLERANCE","TRUST","EXCITEMENT","ACTIVITY","ASSIST","CARE","NEGLIGENCE","PASSION","WORK_HARD","CONTROL","ATTRACT","FAILURE","PURSUE","VENGEANCE","PROCEEDINGS","DISPUTE","PUNISH","GUIDE","TRANSFORM","OVERTHROW","OPPRESS","CHANGE"];
	let value2 = ["GOALS","DREAMS","ENVIRONMENT","OUTSIDE","INSIDE","REALITY","ALLIES","ENEMIES","EVIL","GOOD","EMOTIONS","OPPOSITION","WAR","PEACE","THE_INNOCENT","LOVE","THE_SPIRITUAL","THE_INTELLECTUAL","NEW_IDEAS","JOY","MESSAGES","ENERGY","BALANCE","TENSION","FRIENDSHIP","THE_PHYSICAL","A_PROJECT","PLEASURES","PAIN","POSSESSIONS","BENEFITS","PLANS","LIES","EXPECTATIONS","LEGAL_MATTERS","BUREAUCRACY","BUSINESS","A_PATH","NEWS","EXTERIOR_FACTORS","ADVICE","A_PLOT","COMPETITION","PRISON","ILLNESS","FOOD","ATTENTION","SUCCESS","FAILURE","TRAVEL","JEALOUSY","DISPUTE","HOME","INVESTMENT","SUFFERING","WISHES","TACTICS","STALEMATE","RANDOMNESS","MISFORTUNE","DEATH","DISRUPTION","POWER","A_BURDEN","INTRIGUES","FEARS","AMBUSH","RUMOR","WOUNDS","EXTRAVAGANCE","A_REPRESENTATIVE","ADVERSITIES","OPULENCE","LIBERTY","MILITARY","THE_MUNDANE","TRIALS","MASSES","VEHICLE","ART","VICTORY","DISPUTE","RICHES","STATUS_QUO","TECHNOLOGY","HOPE","MAGIC","ILLUSIONS","PORTALS","DANGER","WEAPONS","ANIMALS","WEATHER","ELEMENTS","NATURE","THE_PUBLIC","LEADERSHIP","FAME","ANGER","INFORMATION"];
	return aPick(value1) + " _(of)_ " + aPick(value2);
}

~~
^meaning( action|)$
~~
return "__Meaning (action)__\n" + rollAction() + "\n\n";

~~
~~
function rollDescription()
{
	let value1 = ["ABNORMALLY","ADVENTUROUSLY","AGGRESSIVELY","ANGRILY","ANXIOUSLY","AWKWARDLY","BEAUTIFULLY","BLEAKLY","BOLDLY","BRAVELY","BUSILY","CALMLY","CAREFULLY","CARELESSLY","CAUTIOUSLY","CEASELESSLY","CHEERFULLY","COMBATIVELY","COOLLY","CRAZILY","CURIOUSLY","DAINTILY","DANGEROUSLY","DEFIANTLY","DELIBERATELY","DELIGHTFULLY","DIMLY","EFFICIENTLY","ENERGETICALLY","ENORMOUSLY","ENTHUSIASTICALLY","EXCITEDLY","FEARFULLY","FEROCIOUSLY","FIERCELY","FOOLISHLY","FORTUNATELY","FRANTICALLY","FREELY","FRIGHTENINGLY","FULLY","GENEROUSLY","GENTLY","GLADLY","GRACEFULLY","GRATEFULLY","HAPPILY","HASTILY","HEALTHILY","HELPFULLY","HELPLESSLY","HOPELESSLY","INNOCENTLY","INTENSELY","INTERESTINGLY","IRRITATINGLY","JOVIALLY","JOYFULLY","JUDGEMENTALLY","KINDLY","KOOKILY","LAZILY","LIGHTLY","LOOSELY","LOUDLY","LOVINGLY","LOYALLY","MAJESTICALLY","MEANINGFULLY","MECHANICALLY","MISERABLY","MOCKINGLY","MYSTERIOUSLY","NATURALLY","NEATLY","NICELY","ODDLY","OFFENSIVELY","OFFICIALLY","PARTIALLY","PEACEFULLY","PERFECTLY","PLAYFULLY","POLITELY","POSITIVELY","POWERFULLY","QUAINTLY","QUARRELSOMELY","QUIETLY","ROUGHLY","RUDELY","RUTHLESSLY","SLOWLY","SOFTLY","SWIFTLY","THREATENINGLY","VERY","VIOLENTLY","WILDLY","YIELDINGLY"];
	let value2 = ["ABANDONED","ABNORMAL","AMUSING","ANCIENT","AROMATIC","AVERAGE","BEAUTIFUL","BIZARRE","CLASSY","CLEAN","COLD","COLORFUL","CREEPY","CUTE","DAMAGED","DARK","DEFEATED","DELICATE","DELIGHTFUL","DIRTY","DISAGREEABLE","DISGUSTING","DRAB","DRY","DULL","EMPTY","ENORMOUS","EXOTIC","FADED","FAMILIAR","FANCY","FAT","FEEBLE","FEMININE","FESTIVE","FLAWLESS","FRESH","FULL","GLORIOUS","GOOD","GRACEFUL","HARD","HARSH","HEALTHY","HEAVY","HISTORICAL","HORRIBLE","IMPORTANT","INTERESTING","JUVENILE","LACKING","LAME","LARGE","LAVISH","LEAN","LESS","LETHAL","LONELY","LOVELY","MACABRE","MAGNIFICENT","MASCULINE","MATURE","MESSY","MIGHTY","MILITARY","MODERN","EXTRAVAGANT","MUNDANE","MYSTERIOUS","NATURAL","NONDESCRIPT","ODD","PALE","PETITE","POOR","POWERFUL","QUAINT","RARE","REASSURING","REMARKABLE","ROTTEN","ROUGH","RUINED","RUSTIC","SCARY","SIMPLE","SMALL","SMELLY","SMOOTH","SOFT","STRONG","TRANQUIL","UGLY","VALUABLE","WARLIKE","WARM","WATERY","WEAK","YOUNG"];
	return aPick(value1) + " " + aPick(value2);
}

~~
^meaning description$
~~
return "__Meaning (description)__\n" +rollDescription() + "\n\n";


~~
~~
function eventCheck()
{
	let outcomes = [ ["REMOTE",7],["NPC ACTS",28,"npcs"],["NEW NPC",35,"pc",true],["THREAD ADVANCE",45,"threads"],["THREAD LOSS",52,"threads"],["THREAD END",55,"threads"],["PC NEGATIVE",67,"pcs"],["PC POSITIVE",75,"pcs"],["AMBIGUOUS",83],["NPC NEGATIVE",92,"npcs"],["NPC POSITIVE",100,"npcs"] ];
	let result = aPickWeight(outcomes);
	let meaningType = result[3];
	let list = window._tejsState.mythic.lists[result[2]];
	result = result[0] + ((list && list.length) ? (" (" + aPick(list) + ")") : "");
	return "__Event__\n" + result + " - " + (meaningType ? rollDescription() : rollAction());
}


~~
^event$
~~
return eventCheck() + "\n\n";

~~
^scene$
~~
return "The current scene is " + window._tejsState.mythic.scene + ".\n\n";


~~
^scene (1|-1)$
~~
let result = "";
if ($1 == 1)
{
	result += chaosUp();
}
else if ($1 == -1)
{
	result += chaosDown();
}
result += "─".repeat(20) + "\n\n\n";
window._tejsState.mythic.scene++;
result += "### SCENE " + window._tejsState.mythic.scene;
let chk = roll(10);
if (chk <= window._tejsState.mythic.chaos)
{
	result += "\n" + ((chk % 2) ? "Scene modified" : "Scene replaced\n" + eventCheck());
}
return result + "\n__Setup__: ";


~~
^fate((?: -?[0-4])?)((?: [y|n])?)$
~~
let odds = ["impossible","no way","very unlikely","unlikely","50/50","likely","very likely","sure thing","has to be"];
let aRoll1 = roll(10);
let aRoll2 = roll(10);
let cRoll = roll(10);
$1 = Number($1 || 0);
let oddsLabel = odds[$1+4];
let oddsAdjust = $1 * 2;
let chaos = window._tejsState.mythic.chaos;
let chaosAdjust = ((chaos==3) ? 2 : (chaos==6) ? -2 : 0)
chaosAdjust *=  ($2 == " n" ? -1 : 1);
let answer = (aRoll1+aRoll2+oddsAdjust+chaosAdjust > 10 ? "YES" : "NO");

let isExtreme = (cRoll <= chaos) && !!(aRoll1 % 2) && !!(aRoll2 % 2);
let isEvent = (cRoll <= chaos) && !(aRoll1 % 2) && !(aRoll2 % 2);
if (cRoll < chaos && aRoll1 == aRoll2) isExtreme = isEvent = true;

answer = (isExtreme ? "EXTREME " : "") + answer;
let evtText = isEvent ? ("\n" + eventCheck()) : "";
return "__Fate check (" + oddsLabel + ")__\n" +answer + "\n_fateRoll1=" + aRoll1 + ",fateRoll2=" + aRoll2 + ",chaosRoll=" + cRoll + ",oddsAdjust=" + oddsAdjust + ",chaosAdjust=" + chaosAdjust + "_" + evtText + "\n\n";


~~
^descriptor$
~~
return "__Descriptor__\nPersonality: " + rollDescription() + "\nActivity: " + rollAction() + "\n\n";


~~
^disposition (-?[0-3])$
~~
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let roll1 = roll(10);
let roll2 = roll(10);
let descriptorAdjust = Number($1) * 2;
let result = aPickWeight(outcomes, 1, roll1 + roll2 + descriptorAdjust);
return "__Disposition__\n" + result[0] + "\n_base=" + (roll1+roll2) + ",roll1=" + roll1 + ",roll2=" + roll2 + ",descriptorAdjust=" + descriptorAdjust + "_\n\n";


~~
^disposition ([0-2]?[0-9]) (-?[0-3])$
~~
let outcomes = [ ["PASSIVE (-2)",5],["MODERATE (0)",10],["ACTIVE (+2)",15],["AGGRESSIVE (+4)",99] ];
let descriptorAdjust = Number($1) * 2;
let result = aPickWeight(outcomes, 1, Number($1) + descriptorAdjust);
return "__Disposition__\n" + result[0] + "\n_base=" + $1 + ",descriptorAdjust=" + descriptorAdjust + "_\n\n";


~~
^action (-2|0|2|4)$
~~
let outcomes1 = [ ["NEW ACTION BASED ON THEME",3],["CONTINUE CURRENT ACTION",5],["CONTINUE CURRENT ACTION, +2 DISPOSITION",6],["CONTINUE CURRENT ACTION, -2 DISPOSITION",7],[false,8,0],[false,9,-4],[false,10,4] ];
let outcomes2 = [ ["TALK / EXPOSITION",6],["NEUTRAL, DISCONNECTED ACTION",8],["ACTION BENEFITS THE PC",10],["GIVE SOMETHING TO THE PC",11],["TRY TO END ENCOUNTER",12],["CHANGE THEME",13],["CHANGE \"%1\" DESCRIPTOR. IT IS ACTIVATED & DECIDES NEXT ACTION.",14,true],["ACT OUT OF SELF INTEREST",17],["TAKE SOMETHING",18],["HURT THE PC",99] ];
let descriptors = ["IDENTITY","PERSONALITY","ACTIVITY"];
let result = roll(10);
let details = "table1roll=" + result;
result = aPickWeight(outcomes1, 1, result);
if (!result[0])
{
	let table2roll1 = roll(10);
	let table2roll2 = roll(10);
	let dispositionAdjust = Number($1);
	let table1Adjust = result[2];
	details += ",table2roll1=" + table2roll1 + ",table2roll2=" + table2roll2 + ",table1Adjust=" + table1Adjust + ",dispositionAdjust=" + dispositionAdjust;
	result = table2roll1 + table2roll2 + dispositionAdjust + table1Adjust;
	result = aPickWeight(outcomes2, 1, result);
	if (result[2])
	{
		let descriptorRoll = roll(3);
		details += ",descriptorRoll=" + descriptorRoll;
		result[0] = result[0].replace("%1", descriptors[descriptorRoll-1]);
	}
	result[0] += "\n+2/0/-2 DISPOSITION TO MATCH ACTION.";
}
return "__Action__\n" + result[0] + "\n_" + details + "_\n\n";
