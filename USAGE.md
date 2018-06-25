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

- To lint a source folder, simply run `eslint src` (replace `src` with any path/file)
- Some errors/warnings are automatically fixable by running `eslint src --fix`

## Gulp integration

Use [gulp-eslint](https://github.com/adametry/gulp-eslint):  
```bash
$ yarn add --dev gulp-eslint
```

## Webpack integration

Use [eslint-loader](https://github.com/webpack-contrib/eslint-loader):  
```bash
$ yarn add --dev eslint
$ yarn add --dev eslint-loader
```

## Upgrading

- To upgrade the global eslint to latest version, use:  
  ```bash
  $ sudo yarn global upgrade eslint --latest
  ```
- To upgrade to latest Eko ESLint config, in your project's root, run:  
  ```bash
  $ yarn upgrade --latest https://buildy:buildyinterlude@bitbucket.org/interludedevs/eko-js-guide.git
  ```

## Sublime Text Integration

- Install [SublimeLinter](https://github.com/SublimeLinter/SublimeLinter) via package control
- Install [SublimeLinter-eslint](https://github.com/SublimeLinter/SublimeLinter-eslint) via package control
