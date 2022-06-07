Shortcuts for UNE (The Universal NPC Emulator).

Enter the shortcut "une help" for reference.

~~
^une help$
~~
let result = "UNE SHORTCUTS HELP\n";
result += "- une help - Display this help text.\n";
result += "- npc - Create a 2-word description of an npc.\n";
result += "- npc power [randomness] - Determine npc's strength relative to pc's, based on [randomness] from 1-order, to 5-chaos.  [randomness] is optional and defaults to 3.\n";
result += "- npc motive - 3, 2-word descriptions of an npc's motivations.\n";
result += "- npc mood [relationship]- Determine npc's friendliness, based on [relationship] from 1-loved, to 7-hated.  [relationship] is optional and defaults to 4.\n";
result += "- npc focus - What the npc is most interesting in discussing.\n";
result += "- npc bearing - Npc's attitude during this conversation.\n";
result += "- npc bearing [demeanour] - Npc's attitude during this conversation, based on [demeanour], a number from 1-8, meaning one of the following.\n";
result += "    1 - sceming       2 - insane       3 - friendly          4 - hostile\n";
result += "    5 - inquisitive    6 - knowing    7 - mysterious    8 - prejudiced\n";
return result + "\n";

~~
^npc$
~~
let table1 =
["SUPERFLUOUS","ADDICTED","CONFORMIST","NEFARIOUS","SENSIBLE","UNTRAINED","ROMANTIC","UNREASONABLE","SKILLED","NEGLECTFUL","LIVELY","FORTHRIGHT","IDEALISTIC","UNSUPPORTIVE","RATIONAL","COARSE","FOOLISH","CUNNING","DELIGHTFUL","MISERLY","INEPT","BANAL","LOGICAL","SUBTLE","REPUTABLE","WICKED","LAZY","PESSIMISTIC","SOLEMN","HABITUAL","MEEK","HELPFUL","UNCONCERNED","GENEROUS","DOCILE","CHEERY","PRAGMATIC","SERENE","THOUGHTFUL","HOPELESS","PLEASANT","INSENSITIVE","TITLED","INEXPERIENCED","PRYING","OBLIVIOUS","REFINED","INDISPENSABLE","SCHOLARLY","CONSERVATIVE","UNCOUTH","WILLFUL","INDIFFERENT","FICKLE","ELDERLY","SINFUL","NAIVE","PRIVILEGED","GLUM","LIKABLE","LETHARGIC","DEFIANT","OBNOXIOUS","INSIGHTFUL","TACTLESS","FANATIC","PLEBEIAN","CHILDISH","PIOUS","UNEDUCATED","INCONSIDERATE","CULTURED","REVOLTING","CURIOUS","TOUCHY","NEEDY","DIGNIFIED","PUSHY","KIND","CORRUPT","JOVIAL","SHREWD","LIBERAL","COMPLIANT","DESTITUTE","CONNIVING","CAREFUL","ALLURING","DEFECTIVE","OPTIMISTIC","AFFLUENT","DESPONDENT","MINDLESS","PASSIONATE","DEVOTED","ESTABLISHED","UNSEEMLY","DEPENDABLE","RIGHTEOUS","CONFIDENT"];
let table2 =
["GYPSY","WITCH","MERCHANT","EXPERT","COMMONER","JUDGE","RANGER","OCCULTIST","REVEREND","THUG","DRIFTER","JOURNEYMAN","STATESMAN","ASTROLOGER","DUELIST","JACK-OF-ALL-TRADES","ARISTOCRAT","PREACHER","ARTISAN","ROGUE","MISSIONARY","OUTCAST","MERCENARY","CARETAKER","HERMIT","ORATOR","CHIEFTAIN","PIONEER","BURGLAR","VICAR","OFFICER","EXPLORER","WARDEN","OUTLAW","ADEPT","BUM","SORCERER","LABORER","MASTER","ASCENDANT","VILLAGER","MAGUS","CONSCRIPT","WORKER","ACTOR","HERALD","HIGHWAYMAN","FORTUNE-HUNTER","GOVERNOR","SCRAPPER","MONK","HOMEMAKER","RECLUSE","STEWARD","POLYMATH","MAGICIAN","TRAVELER","VAGRANT","APPRENTICE","POLITICIAN","MEDIATOR","CROOK","CIVILIAN","ACTIVIST","HERO","CHAMPION","CLERIC","SLAVE","GUNMAN","CLAIRVOYANT","PATRIARCH","SHOPKEEPER","CRONE","ADVENTURER","SOLDIER","ENTERTAINER","CRAFTSMAN","SCIENTIST","ASCETIC","SUPERIOR","PERFORMER","MAGISTER","SERF","BRUTE","INQUISITOR","LORD","VILLAIN","PROFESSOR","SERVANT","CHARMER","GLOBETROTTER","SNIPER","COURTIER","PRIEST","TRADESMAN","HITMAN","WIZARD","BEGGAR","TRADESMAN","WARRIOR"];
return "__NPC__\n" + table1[roll(100)-1] + " " + table2[roll(100)-1] + "\n\n";


~~
^npc power((?: [1-5])?)$
~~
let table3 = 
[ ["MUCH WEAKER",2,4,5,8,12],["SLIGHTLY WEAKER",10,15,20,25,30],["COMPARABLE",90,85,80,75,70],["SLIGHTLY STRONGER",98,96,95,92,88],["MUCH STRONGER",999,999,999,999,999] ];
$1 = Number($1) || 3;
let result = roll(100);
let details = "\n_roll=" + result + ",randomness=" + $1 + "_\n\n";
for (let i = 0; i < table3.length; i++)
{
	if (table3[i][$1] >= result)
	{
		result = table3[i][0];
		break;
	}
}
return "__Npc power__\n" + result + details;


~~
^npc motive$
~~
let table4 =
["ADVISE","OBTAIN","ATTEMPT","SPOIL","OPPRESS","INTERACT","CREATE","ABDUCT","PROMOTE","CONCEIVE","BLIGHT","PROGRESS","DISTRESS","POSSESS","RECORD","EMBRACE","CONTACT","PURSUE","ASSOCIATE","PREPARE","SHEPHERD","ABUSE","INDULGE","CHRONICLE","FULFILL","DRIVE","REVIEW","AID","FOLLOW","ADVANCE","GUARD","CONQUER","HINDER","PLUNDER","CONSTRUCT","ENCOURAGE","AGONIZE","COMPREHEND","ADMINISTER","RELATE","TAKE","DISCOVER","DETER","ACQUIRE","DAMAGE","PUBLICIZE","BURDEN","ADVOCATE","IMPLEMENT","UNDERSTAND","COLLABORATE","STRIVE","COMPLETE","COMPEL","JOIN","ASSIST","DEFILE","PRODUCE","INSTITUTE","ACCOUNT","WORK","ACCOMPANY","OFFEND","GUIDE","LEARN","PERSECUTE","COMMUNICATE","PROCESS","REPORT","DEVELOP","STEAL","SUGGEST","WEAKEN","ACHIEVE","SECURE","INFORM","PATRONIZE","DEPRESS","DETERMINE","SEEK","MANAGE","SUPPRESS","PROCLAIM","OPERATE","ACCESS","REFINE","COMPOSE","UNDERMINE","EXPLAIN","DISCOURAGE","ATTEND","DETECT","EXECUTE","MAINTAIN","REALIZE","CONVEY","ROB","ESTABLISH","OVERTHROW","SUPPORT"];
let table5 =
["WEALTH","HARDSHIP","AFFLUENCE","RESOURCES","PROSPERITY","POVERTY","OPULENCE","DEPRIVATION","SUCCESS","DISTRESS","CONTRABAND","MUSIC","LITERATURE","TECHNOLOGY","ALCOHOL","MEDICINES","BEAUTY","STRENGTH","INTELLIGENCE","FORCE","THE_WEALTHY","THE_POPULOUS","ENEMIES","THE_PUBLIC","RELIGION","THE_POOR","FAMILY","THE_ELITE","ACADEMIA","THE_FORSAKEN","THE_LAW","THE_GOVERNMENT","THE_OPPRESSED","FRIENDS","CRIMINALS","ALLIES","SECRET_SOCIETIES","THE_WORLD","MILITARY","THE_CHURCH","DREAMS","DISCRETION","LOVE","FREEDOM","PAIN","FAITH","SLAVERY","ENLIGHTENMENT","RACISM","SENSUALITY","DISSONANCE","PEACE","DISCRIMINATION","DISBELIEF","PLEASURE","HATE","HAPPINESS","SERVITUDE","HARMONY","JUSTICE","GLUTTONY","LUST","ENVY","GREED","LAZINESS","WRATH","PRIDE","PURITY","MODERATION","VIGILANCE","ZEAL","COMPOSURE","CHARITY","MODESTY","ATROCITIES","COWARDICE","NARCISSISM","COMPASSION","VALOR","PATIENCE","ADVICE","PROPAGANDA","SCIENCE","KNOWLEDGE","COMMUNICATIONS","LIES","MYTHS","RIDDLES","STORIES","LEGENDS","INDUSTRY","NEW_RELIGIONS","PROGRESS","ANIMALS","GHOSTS","MAGIC","NATURE","OLD_RELIGIONS","EXPERTISE","SPIRITS"];
let result = "__NPC motive__\n";
let priorNounRolls = [];
for (let i = 0; i < 3; i++)
{
	let roll1 = roll(100);
	let roll2 = roll(100);
	while (true)
	{
		let foundMatch = false;
		for (let i = 0; i < priorNounRolls.length; i++)
		{
			if (priorNounRolls[i] == Math.trunc((roll2-1)/20))
			{
				foundMatch = true;
				break;
			}
		}
		if (!foundMatch) break;
		roll2 = roll(100);
	}
	priorNounRolls.push(Math.trunc((roll2-1)/20));
	result += table4[roll1-1] + " " + table5[roll2-1] + " " + roll2 + "\n";
}
return result + "\n";

~~
^npc mood((?: [1-7])?)$
~~
let table6 = 
[ ["WITHDRAWN",1,2,3,5,7,11,15],["GUARDED",6,8,11,15,18,24,30],["CAUTIOUS",16,20,25,30,46,61,69],["NEUTRAL",31,40,55,60,76,81,84],["SOCIABLE",70,76,82,85,90,93,94],["HELPFUL",85,89,93,95,97,98,99],["FORTHCOMING",999,999,999,999,999,999,999] ];
$1 = Number($1) || 4;
let result = roll(100);
let details = "\n_roll=" + result + ",relationship=" + $1 + "_\n\n";
for (let i = 0; i < table6.length; i++)
{
	if (table6[i][$1] >= result)
	{
		result = table6[i][0];
		break;
	}
}
return "__Npc mood__\n" + result + details;


~~
^npc focus$
~~
let table8 = [ [3,"CURRENT_SCENE"],[6,"LAST_STORY"],[9,"EQUIPMENT"],[12,"PARENTS"],[15,"HISTORY"],[18,"RETAINERS"],[21,"WEALTH"],[24,"RELICS"],[27,"LAST_ACTION"],[30,"SKILLS"],[33,"SUPERIORS"],[36,"FAME"],[39,"CAMPAIGN"],[42,"FUTURE_ACTION"],[45,"FRIENDS"],[48,"ALLIES"],[51,"LAST_SCENE"],[54,"CONTACTS"],[57,"FLAWS"],[60,"ANTAGONIST"],[63,"REWARDS"],[66,"EXPERIENCE"],[69,"KNOWLEDGE"],[72,"RECENT_SCENE"],[75,"COMMUNITY"],[78,"TREASURE"],[81,"THE_CHARACTER"],[84,"CURRENT_STORY"],[87,"FAMILY"],[90,"POWER"],[93,"WEAPONS"],[96,"PREVIOUS_SCENE"],[100,"ENEMY"] ];
let result = roll(100);
for (let i = 0; i < table8.length; i++)
{
	if (table8[i][0] >= result)
	{
		result = "THE PC'S " + table8[i][1];
		break;
	}
}
return "__Npc focus__\n" + result + "\n\n";


~~
^npc bearing((?: [1-8])?)$
~~
let table7 = [
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
let result = table7[demeanor-1][bearing-1];
return "__Npc bearing__\n" + result + "\n_demeanor=" + demeanor + ",bearing=" + bearing + "_\n\n";
