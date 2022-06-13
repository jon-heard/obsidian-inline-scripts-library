Various helper shortcuts of minimal category.

Enter the shortcut "help support" for reference.

~~
^help support$
~~
```js
let result = "### TEJS SUPPORT SHORTCUTS HELP\n";
result += "- __help support__ - Display this help text.\n";
result += "***\n";
result += "- __rngseed {seed}__ - Sets up a consistant random number generator with a seed of {seed} (a non-zero integer).  Useful for testing.\n";
return result + "\n";
```

~~
^rngseed ([1-9][0-9]*)$
~~
```js
Math.rngseed = $1;
Math.random = function() {
    var x = Math.sin(Math.rngseed++) * 10000;
    return x - Math.floor(x);
};
return "Random number generator set up with seed " + $1 + ".\n\n";
```
