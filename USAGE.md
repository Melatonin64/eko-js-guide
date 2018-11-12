# Eko ESLint Config - Usage Guide

## Install ESLint globally

```bash
sudo yarn global add eslint@5.1.0
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
yarn add --dev gulp-eslint
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
yarn add --dev eslint
yarn add --dev eslint-loader
```

## Visual Studio Code Integration

- Install [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) VSCode extension from the marketplace.

The following are some useful VSCode settings, that should help ease some of the pain of conforming with our style:

```javascript
{
    // Configure file associations to languages (e.g. `"*.extension": "html"`).
    // These have precedence over the default associations of the languages installed.
    "files.associations": {

        // Allow comments in ESLint JSON config files
        ".eslintrc.json": "jsonc"
    },

    // The number of spaces a tab is equal to.
    // This setting is overridden based on the file contents when `editor.detectIndentation` is on.
    "editor.tabSize": 4,

    // Insert spaces when pressing `Tab`.
    // This setting is overridden based on the file contents when `editor.detectIndentation` is on.
    "editor.insertSpaces": true,

    // When enabled, insert a final new line at the end of the file when saving it.
    "files.insertFinalNewline": true,

    // The default end of line character.
    //  - \n: LF
    //  - \r\n: CRLF
    "files.eol": "\n"
}
```

## Sublime Text Integration

- Install [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter) via package control
- Install [SublimeLinter-eslint](https://github.com/SublimeLinter/SublimeLinter-eslint) via package control

The following are some useful Sublime Text settings, that should help ease some of the pain of conforming with our style:

```javascript
{
  // The number of spaces a tab is considered equal to
  "tab_size": 4,

  // Set to true to insert spaces when tab is pressed
  "translate_tabs_to_spaces": true,

  // Set to true to ensure the last line of the file ends in a newline
  // character when saving
  "ensure_newline_at_eof_on_save": true,

  // Determines what character(s) are used to terminate each line in new files.
  // Valid values are 'system' (whatever the OS uses), 'windows' (CRLF) and
  // 'unix' (LF only).
  "default_line_ending": "unix"
}
```

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
  sudo yarn global upgrade eslint --latest
  ```

- To upgrade to latest Eko ESLint config, in your project's root, run:  

  ```bash
  yarn upgrade --latest eko-js-guide
  ```

## Troubleshooting

### I need to re-apply the fix for line endings whenever I switch git branches

This is probably because you're using Windows and git is configured to automatically use CRLF line endings.
You can disable this git behavior by opening a `cmd` prompt and running `git config --global core.autocrlf false`.
