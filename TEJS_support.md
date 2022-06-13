Various helper shortcuts of minimal category

~~
^help support$
~~
```js
let result = "### TEJS SUPPORT SHORTCUTS HELP\n";
result += "- __help support__ - Display this help text.\n";
result += "***\n";
result += "- __rngSeed {seed}__ - Sets up a consistant random number generator with a seed of {seed} (a non-zero integer).  Useful for testing.\n";
return result + "\n";
```

~~
^rngSeed ([1-9][0-9]*)$
~~
```js
Math.rngSeed = $1;
Math.random = function() {
    var x = Math.sin(Math.rngSeed++) * 10000;
    return x - Math.floor(x);
};
return "Random number generator set up with seed " + $1 + "\n\n";
```
