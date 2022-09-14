A single shortcut to run commands for the Dice Roller plugin.

__
```
^diceroller (.+)$
```
__
```js
const diceRollerPlugin = app.plugins.getPlugin("obsidian-dice-roller");
const diceRoller = await diceRollerPlugin.getRoller($1);
return await diceRoller.roll();
```
__
diceroller {command: text} - Runs {command} through the diceroller system.
