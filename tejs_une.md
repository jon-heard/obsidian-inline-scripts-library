Shortcuts for UNE: The Universal NPC Emulator.  This is an excellent character generation system for tabletop role playing and general storytelling.  You can find more info about UNE at its [drivethrurpg page](https://www.drivethrurpg.com/product/134163/UNE-The-Universal-NPC-Emulator-rev).

Zach Best, the writer and publisher of this product has, sadly, passed away.  Proceeds from buying UNE, and other [conjecture games](https://www.drivethrurpg.com/browse/pub/7251/Conjecture-Games) products go to supporting his family.


~~
~~
```js
function roll(max) { return Math.trunc(Math.random() * max + 1); }
function aPick(a) { return a[roll(a.length)-1]; }
function aPickWeight(a, wIndex, theRoll)
{
	wIndex = wIndex || 1;
	theRoll = theRoll || roll(a.last()[wIndex]);
	for (let i = 0; i < a.length; i++)
	{
		if (a[i][wIndex] >= theRoll)
		{
			return a[i];
		}
	}
	return a.last();
}
```
~~
Some useful functions


~~
```
^une((?: [1-5])?)((?: [1-7])?)((?: [1-8])?)$
```
~~
```js
return getExpansion("une character" + $1)[0] + "***\n" + getExpansion("une interact" + $2 + $3);
```
~~
une {randomness} {relationship to pc} {demeanor} - Runs "une character" and "une interact" together.  {randomness} is an optional value for "une character".  {relationship to pc} and {demeanor} are optional values for "une interact".
***


~~
```
^une character((?: [1-5])?)$
```
~~
```js
return ["__Character__\n__Identity__: " + getExpansion("une identity")[1] + "\n__Power__: " + getExpansion("une power" + $1)[1] + "\n__Motive__:\n" + getExpansion("une motive")[1], "\n"];
```
~~
une character {randomness} - Runs "identity", "power" and "motive" together.  {randomness} is an optional value for "power".


~~
```
^une interact((?: [1-7])?)((?: [1-8])?)$
```
~~
```js
return "__Interact__\n__Mood__: " + getExpansion("une mood" + $1)[1] + "\n__Bearing__: " + getExpansion("une bearing" + $2)[1] + "\n__Focus__: " + getExpansion("une focus")[1] + "\n\n";
```
~~
une interact {relationship to pc} {demeanor} - Runs "mood", "bearing" and "focus" together.  {relationship to pc} is an optional value for "mood".  {demeanor} is an optional value for "bearing".
***


~~
```
^une identity$
```
~~
```js
let table1 =
["SUPERFLUOUS","ADDICTED","CONFORMIST","NEFARIOUS","SENSIBLE","UNTRAINED","ROMANTIC","UNREASONABLE","SKILLED","NEGLECTFUL","LIVELY","FORTHRIGHT","IDEALISTIC","UNSUPPORTIVE","RATIONAL","COARSE","FOOLISH","CUNNING","DELIGHTFUL","MISERLY","INEPT","BANAL","LOGICAL","SUBTLE","REPUTABLE","WICKED","LAZY","PESSIMISTIC","SOLEMN","HABITUAL","MEEK","HELPFUL","UNCONCERNED","GENEROUS","DOCILE","CHEERY","PRAGMATIC","SERENE","THOUGHTFUL","HOPELESS","PLEASANT","INSENSITIVE","TITLED","INEXPERIENCED","PRYING","OBLIVIOUS","REFINED","INDISPENSABLE","SCHOLARLY","CONSERVATIVE","UNCOUTH","WILLFUL","INDIFFERENT","FICKLE","ELDERLY","SINFUL","NAIVE","PRIVILEGED","GLUM","LIKABLE","LETHARGIC","DEFIANT","OBNOXIOUS","INSIGHTFUL","TACTLESS","FANATIC","PLEBEIAN","CHILDISH","PIOUS","UNEDUCATED","INCONSIDERATE","CULTURED","REVOLTING","CURIOUS","TOUCHY","NEEDY","DIGNIFIED","PUSHY","KIND","CORRUPT","JOVIAL","SHREWD","LIBERAL","COMPLIANT","DESTITUTE","CONNIVING","CAREFUL","ALLURING","DEFECTIVE","OPTIMISTIC","AFFLUENT","DESPONDENT","MINDLESS","PASSIONATE","DEVOTED","ESTABLISHED","UNSEEMLY","DEPENDABLE","RIGHTEOUS","CONFIDENT"];
let table2 =
["GYPSY","WITCH","MERCHANT","EXPERT","COMMONER","JUDGE","RANGER","OCCULTIST","REVEREND","THUG","DRIFTER","JOURNEYMAN","STATESMAN","ASTROLOGER","DUELIST","JACK-OF-ALL-TRADES","ARISTOCRAT","PREACHER","ARTISAN","ROGUE","MISSIONARY","OUTCAST","MERCENARY","CARETAKER","HERMIT","ORATOR","CHIEFTAIN","PIONEER","BURGLAR","VICAR","OFFICER","EXPLORER","WARDEN","OUTLAW","ADEPT","BUM","SORCERER","LABORER","MASTER","ASCENDANT","VILLAGER","MAGUS","CONSCRIPT","WORKER","ACTOR","HERALD","HIGHWAYMAN","FORTUNE-HUNTER","GOVERNOR","SCRAPPER","MONK","HOMEMAKER","RECLUSE","STEWARD","POLYMATH","MAGICIAN","TRAVELER","VAGRANT","APPRENTICE","POLITICIAN","MEDIATOR","CROOK","CIVILIAN","ACTIVIST","HERO","CHAMPION","CLERIC","SLAVE","GUNMAN","CLAIRVOYANT","PATRIARCH","SHOPKEEPER","CRONE","ADVENTURER","SOLDIER","ENTERTAINER","CRAFTSMAN","SCIENTIST","ASCETIC","SUPERIOR","PERFORMER","MAGISTER","SERF","BRUTE","INQUISITOR","LORD","VILLAIN","PROFESSOR","SERVANT","CHARMER","GLOBETROTTER","SNIPER","COURTIER","PRIEST","TRADESMAN","HITMAN","WIZARD","BEGGAR","TRADESMAN","WARRIOR"];
return ["__Character identity__\n", aPick(table1) + " " + aPick(table2), "\n\n"];
```
~~
une identity - Generates a 2-word description for a character.


~~
```
^une power((?: [1-5])?)$
```
~~
```js
let table3 =
[ ["MUCH WEAKER",2,4,5,8,12],["SLIGHTLY WEAKER",10,15,20,25,30],["COMPARABLE",90,85,80,75,70],["SLIGHTLY STRONGER",98,96,95,92,88],["MUCH STRONGER",100,100,100,100,100] ];
return ["__Character power level__\n", aPickWeight(table3, Number($1) || 3)[0], "\n\n"];
```
~~
une power {randomness} - Generates a character's power level relative to pc's power level, based on {randomness}: an optional number from 1 (order), to 5 (chaos), defaulting to 3 (standard).


~~
```
^une motive$
```
~~
```js
let table4 =
["ADVISE","OBTAIN","ATTEMPT","SPOIL","OPPRESS","INTERACT","CREATE","ABDUCT","PROMOTE","CONCEIVE","BLIGHT","PROGRESS","DISTRESS","POSSESS","RECORD","EMBRACE","CONTACT","PURSUE","ASSOCIATE","PREPARE","SHEPHERD","ABUSE","INDULGE","CHRONICLE","FULFILL","DRIVE","REVIEW","AID","FOLLOW","ADVANCE","GUARD","CONQUER","HINDER","PLUNDER","CONSTRUCT","ENCOURAGE","AGONIZE","COMPREHEND","ADMINISTER","RELATE","TAKE","DISCOVER","DETER","ACQUIRE","DAMAGE","PUBLICIZE","BURDEN","ADVOCATE","IMPLEMENT","UNDERSTAND","COLLABORATE","STRIVE","COMPLETE","COMPEL","JOIN","ASSIST","DEFILE","PRODUCE","INSTITUTE","ACCOUNT","WORK","ACCOMPANY","OFFEND","GUIDE","LEARN","PERSECUTE","COMMUNICATE","PROCESS","REPORT","DEVELOP","STEAL","SUGGEST","WEAKEN","ACHIEVE","SECURE","INFORM","PATRONIZE","DEPRESS","DETERMINE","SEEK","MANAGE","SUPPRESS","PROCLAIM","OPERATE","ACCESS","REFINE","COMPOSE","UNDERMINE","EXPLAIN","DISCOURAGE","ATTEND","DETECT","EXECUTE","MAINTAIN","REALIZE","CONVEY","ROB","ESTABLISH","OVERTHROW","SUPPORT"];
let table5 =
["WEALTH","HARDSHIP","AFFLUENCE","RESOURCES","PROSPERITY","POVERTY","OPULENCE","DEPRIVATION","SUCCESS","DISTRESS","CONTRABAND","MUSIC","LITERATURE","TECHNOLOGY","ALCOHOL","MEDICINES","BEAUTY","STRENGTH","INTELLIGENCE","FORCE","THE_WEALTHY","THE_POPULOUS","ENEMIES","THE_PUBLIC","RELIGION","THE_POOR","FAMILY","THE_ELITE","ACADEMIA","THE_FORSAKEN","THE_LAW","THE_GOVERNMENT","THE_OPPRESSED","FRIENDS","CRIMINALS","ALLIES","SECRET_SOCIETIES","THE_WORLD","MILITARY","THE_CHURCH","DREAMS","DISCRETION","LOVE","FREEDOM","PAIN","FAITH","SLAVERY","ENLIGHTENMENT","RACISM","SENSUALITY","DISSONANCE","PEACE","DISCRIMINATION","DISBELIEF","PLEASURE","HATE","HAPPINESS","SERVITUDE","HARMONY","JUSTICE","GLUTTONY","LUST","ENVY","GREED","LAZINESS","WRATH","PRIDE","PURITY","MODERATION","VIGILANCE","ZEAL","COMPOSURE","CHARITY","MODESTY","ATROCITIES","COWARDICE","NARCISSISM","COMPASSION","VALOR","PATIENCE","ADVICE","PROPAGANDA","SCIENCE","KNOWLEDGE","COMMUNICATIONS","LIES","MYTHS","RIDDLES","STORIES","LEGENDS","INDUSTRY","NEW_RELIGIONS","PROGRESS","ANIMALS","GHOSTS","MAGIC","NATURE","OLD_RELIGIONS","EXPERTISE","SPIRITS"];
let result = "";
let priorNounColumns = [];
for (let i = 0; i < 3; i++)
{
	let roll2;
	do
	{
		roll2 = roll(100);
		let foundColumnMatch = false;
		for (let i = 0; i < priorNounColumns.length; i++)
		{
			if (priorNounColumns[i] == Math.trunc((roll2-1)/20))
			{
				foundColumnMatch = true;
				break;
			}
		}
		if (!foundColumnMatch) break;
	}
	while (true);
	priorNounColumns.push(Math.trunc((roll2-1)/20));
	result += "- " + aPick(table4) + " " + table5[roll2-1] + " " + "\n";
}
return ["__Character motive__\n", result, "\n"];
```
~~
une motive - Generates three 2-word descriptions for a character's motivations.
***


~~
```
^une mood((?: [1-7])?)$
```
~~
```js
let table6 = [
	["WITHDRAWN",1,2,3,5,7,11,15],
	["GUARDED",6,8,11,15,18,24,30],
	["CAUTIOUS",16,20,25,30,46,61,69],
	["NEUTRAL",31,40,55,60,76,81,84],
	["SOCIABLE",70,76,82,85,90,93,94],
	["HELPFUL",85,89,93,95,97,98,99],
	["FORTHCOMING",100,100,100,100,100,100,100] ];
let result = aPickWeight(table6, Number($1) || 4)[0];
return ["__Character mood__\n", result, "\n\n"];
```
~~
une mood {relationship to pc} - Generates a character's willingness to socialize for this interaction, based on {relationship to pc}: an optional number from 1 (love), to 7 (hate), defaulting to 4 (neutral).


~~
```
^une bearing((?: [1-8])?)$
```
~~
```js
let table7a = ["SCHEMING", "INSANE", "FRIENDLY", "HOSTILE", "INQUISITIVE", "KNOWING", "MYSTERIOUS", "PREJUDICED"];
let table7b = [
["INTENT","BARGAIN","MEANS","PROPOSITION","PLAN","COMPROMISE","AGENDA","ARRANGEMENT","NEGOTIATION","PLOT"],
["MADNESS","FEAR","ACCIDENT","CHAOS","IDIOCY","ILLUSION","TURMOIL","CONFUSION","FACADE","BEWILDERMENT"],
["ALLIANCE","COMFORT","GRATITUDE","SHELTER","HAPPINESS","SUPPORT","PROMISE","DELIGHT","AID","CELEBRATION"],
["DEATH","CAPTURE","JUDGMENT","COMBAT","SURRENDER","RAGE","RESENTMENT","SUBMISSION","INJURY","DESTRUCTION"],
["QUESTIONS","INVESTIGATION","INTEREST","DEMAND","SUSPICION","REQUEST","CURIOSITY","SKEPTICISM","COMMAND","PETITION"],
["REPORT","EFFECTS","EXAMINATION","RECORDS","ACCOUNT","NEWS","HISTORY","TELLING","DISCOURSE","SPEECH"],
["RUMOR","UNCERTAINTY","SECRETS","MISDIRECTION","WHISPERS","LIES","SHADOWS","ENIGMA","OBSCURITY","CONUNDRUM"],
["REPUTATION","DOUBT","BIAS","DISLIKE","PARTIALITY","BELIEF","VIEW","DISCRIMINATION","ASSESSMENT","DIFFERENCE"] ];
let demeanor = $1 ? $1 : roll(8);
let bearing = roll(10);
let result = table7a[demeanor-1] + " - " + table7b
[demeanor-1][bearing-1];
return ["__Character bearing__\n", result, "\n\n"];
```
~~
une bearing {demeanor} - Generate a character's attitude for this interaction, based on {demeanor}: an optional number from 1-8, defaulting to random and meaning one of the following:
    1 - sceming       2 - insane       3 - friendly          4 - hostile
    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced


~~
```
^une focus$
```
~~
```js
let table8 = [ ["CURRENT_SCENE", 3],["LAST_STORY",6],["EQUIPMENT",9],["PARENTS",12],["HISTORY",15],["RETAINERS",18],["WEALTH",21],["RELICS",24],["LAST_ACTION",27],["SKILLS",30],["SUPERIORS",33],["FAME",36],["CAMPAIGN",39],["FUTURE_ACTION",42],["FRIENDS",45],["ALLIES",48],["LAST_SCENE",51],["CONTACTS",54],["FLAWS",57],["ANTAGONIST",60],["REWARDS",63],["EXPERIENCE",66],["KNOWLEDGE",69],["RECENT_SCENE",72],["COMMUNITY",75],["TREASURE",78],["THE_CHARACTER",81],["CURRENT_STORY",84],["FAMILY",87],["POWER",90],["WEAPONS",93],["PREVIOUS_SCENE",96],["ENEMY",100] ];
let result = "THE PC'S " + aPickWeight(table8)[0];
return ["__Character focus__\n", result, "\n\n"];
```
~~
une focus - Generate a character's primary interest for this interaction.
