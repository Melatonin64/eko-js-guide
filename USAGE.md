# Eko ESLint Config - Usage Guide

## Install ESLint globally

```bash
$ sudo yarn global add eslint
```

## Add Eko's ESLint config to your project

- Add this repo as a dev dependency:  
  `yarn add --dev https://buildy:buildyinterlude@bitbucket.org/interludedevs/eko-js-guide.git`
- Add a `.eslintrc.json` file to the root of your project with the following contents:  
  ```javascript
  {
      "extends": "./node_modules/eko-js-guide/.eslintrc.json"
  }
  ```

## CLI Usage

- To recursively lint a source folder, simply run `eslint src` (replace `src` with any path/file)
- Some errors/warnings are **automatically fixable** by running `eslint src --fix`

## Gulp integration

Use [gulp-eslint](https://github.com/adametry/gulp-eslint):  
```bash
$ yarn add --dev gulp-eslint
```

Here's a usage example:
```javascript
const gulp            = require('gulp');
const eslint          = require('gulp-eslint');

function lint(glob, options = {}) {
    return gulp.src(glob)

        // Run the linter
        .pipe(eslint())

        // Output lint results to the console
        .pipe(eslint.format())

        // Fail on warnings
        .pipe(eslint.results(results => {
            var maxWarnings = options.maxWarnings || 0;

            if (results.warningCount > maxWarnings) {
                throw new Error(
                    'Failed because of too many ESLint warnings. ' +
                    `Found ${count} ${count === 1 ? 'warning' : 'warnings'}, threshold is ${maxWarnings}.`
                );
            }
        }))

        // Fail on errors
        .pipe(eslint.failAfterError());
}
```

## Webpack integration

Use [eslint-loader](https://github.com/webpack-contrib/eslint-loader):  
```bash
$ yarn add --dev eslint
$ yarn add --dev eslint-loader
```

## Sublime Text Integration

- Install [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter) via package control
- Install [SublimeLinter-eslint](https://github.com/SublimeLinter/SublimeLinter-eslint) via package control

## Disabling\Overriding Rules

The ESLint config is meant to help you by surfacing potential issues early and helping you keep your code tidy, consistent and readable.
In some cases, you may find that a particular rule (or set of rules) is slowing you down, not relevant for your use-case or just annoying.
If after consideration you decide to ignore some rules, there are a few ways of doing so:

### Inline comments

You can disable a rule for a specific line by using `eslint-disable-line` and `eslint-disable-next-line` comments:
```javascript
alert('This is allowed because of the comment to the right --->'); // eslint-disable-line no-alert

// eslint-disable-next-line no-console
console.log('ESLint will no longer complain about this line!');
```
Note that you can specify multiple comma-separated rules to disable.
If no specific rule is given, all rules will be disabled for that line.

### Inline blocks

If you need to disable a rule for more than one line, use `eslint-disable` block comment:

```javascript
/* eslint-disable no-magic-numbers, no-self-compare */
function useMagicNumbers(num) {
    if (num === num) {
        return (num * 7) + 23;
    }

    return (num * 4) / 27;
}
/* eslint-enable no-magic-numbers, no-self-compare */
```

Make sure to re-enable that rule at the end of the block where it's required, using the `eslint-enable` block comment.  
Again, if no specific rules are given, all linting will be disabled for that block.

### File level disables

You can also disable rules (or disable linting entirely) by using an `eslint-disable` block comment at the top of the file, without a matching `eslint-enable` comment.
For instance, if the file you're working on uses many bitwise operators, you can safely disable the [no-bitwise](https://eslint.org/docs/rules/no-bitwise) rule:

```javascript
/* eslint-disable no-bitwise */

var FLAG_A = 1; // 0001
var FLAG_B = 2; // 0010
var FLAG_C = 4; // 0100
var FLAG_D = 8; // 1000

var mask = FLAG_A | FLAG_B | FLAG_D; // 0001 | 0010 | 1000 => 1011

// (0101 & 0010) || (0101 & 0100) => 0000 || 0100 => true
if ((flags & FLAG_B) || (flags & FLAG_C)) {
    // Do stuff
}
```

### Extend/Override the config

This option will recursively apply rule changes for an entire project/folder.
Identify the smallest root folder where the changes are required, and add a `.eslintrc.json` file with your overridden rules, for example:

```javascript
{
    // Make sure to specify a correct relative path to our shared config
    "extends": "./relative/path/to/node_modules/eko-js-guide/.eslintrc.json",

    // Override envs
    "env": {

        // Add AMD environment (treat define() and require() as globals)
        "amd": true,

        // Add mocha globals
        "mocha": true
    },

    // Add some extra globals
    "globals": {
        "InterludePlayer": true,
        "TweenMax": true
    },

    // Override some rules
    "rules": {

        // Disable the "no-console" rule in order to allow using console
        "no-console": "off"
    }
}
```

### Note

Please be mindful and try to choose the most appropriate method of disabling rules, one that affects the minimal required scope.
Prefer to disable specific rules rather than simply disabling all linting.
More info on configuring ESLint can be found [here](https://eslint.org/docs/user-guide/configuring).

## Upgrading

- To upgrade the global eslint to latest version, use:  
  ```bash
  $ sudo yarn global upgrade eslint --latest
  ```
- To upgrade to latest Eko ESLint config, in your project's root, run:  
  ```bash
  $ yarn upgrade --latest eko-js-guide
  ```
