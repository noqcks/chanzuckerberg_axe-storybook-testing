# @chanzuckerberg/axe-storybook-testing

[![Package Status](https://img.shields.io/npm/v/@chanzuckerberg/axe-storybook-testing.svg)](https://www.npmjs.com/package/@chanzuckerberg/axe-storybook-testing) ![Tests](https://github.com/chanzuckerberg/axe-storybook-testing/workflows/Tests/badge.svg)

Command line interface for running accessibility tests (using [axe-core](https://github.com/dequelabs/axe-core)) on your [Storybook stories](https://storybook.js.org/docs/react/api/csf).

If there are any violations, information about them will be printed, and the command will exit with a non-zero exit code. That way, you can use this as automated accessibility tests on CI.

## Table of contents

- [Minimum requirements](#minimum-requirements)
- [Known issues](#known-issues)
- [Installation](#installation)
- [Usage](#usage)
- [Options](#options)
- [Configuring stories](#configuring-stories)
  - [skip](#skip)
  - [disabledRules](#disabledrules)
- [Developing](#developing)
- [Inspiration](#inspiration)

## Minimum requirements

- Node 12
- Storybook 6.4 (for previous versions of Storybook, use axe-storybook-testing v4.1.3)
- axe-core 4.0

## Known issues

- axe-storybook-testing doesn't work when [storyStoreV7](https://storybook.js.org/blog/storybook-on-demand-architecture/) is set to `true`, due to https://github.com/storybookjs/storybook/issues/16967.

## Installation

```sh
# via npm
npm install --save-dev @chanzuckerberg/axe-storybook-testing

# or, if using Yarn
yarn add --dev @chanzuckerberg/axe-storybook-testing
```

## Usage

This package works by analyzing the static files that Storybook produces. Therefore, Storybook's build command must be ran first.

To make this as easy as possible to use, we recommend adding a script to your package.json that builds Storybook and then executes the `axe-storybook` command.

```jsonc
// In package.json
"scripts": {
  "storybook:axe": "build-storybook && axe-storybook"
},
```

Then you can run the tests with

```sh
# If using npm
npm run storybook:axe

# or, if using Yarn
yarn storybook:axe
```

## Options

The command-line interface has the following options.

Option|Default|Type|Description
-|-|-|-
`--browser`|`chromium`|chromium, firefox, or webkit|Which browser to run the tests in
`--build-dir`|`storybook-static`|string|Storybook static build directory
`--storybook-address`||string|Storybook server address to test against instead of using a static build directory. If set, `--build-dir` will be ignored. e.g. `--storybook-address http://localhost:6006`
`--failing-impact`|`all`|minor, moderate, serious, critical, or all|The lowest impact level that should be considered a failure
`--format`|`spec`|string|Format to output test data in. Right now the only option is "spec"
`--headless`|`true`|boolean|Whether to run headlessly or not
`--pattern`|`.*`|string regex|Only run tests that match a component name pattern
`--timeout`|2000|number|Timeout (in milliseconds) for each test

For example, to run non-headlessly on Firefox, you would run

```sh
# If using npm
npm run storybook:axe -- --headless false --browser firefox

# or, if using Yarn
yarn storybook:axe --headless false --browser firefox
```

## Configuring stories

Stories can use parameters to configure how axe-storybook-testing handles them.

### skip

Prevent axe-storybook-testing from running a story by using the `skip` parameter.

```jsx
// SomeComponent.stories.jsx

SomeStory.parameters = {
  axe: {
    skip: true,
  },
};
```

### disabledRules

Prevent axe-storybook-testing from running specific Axe rules on a story by using the `disabledRules` parameter.

```jsx
// SomeComponent.stories.jsx

SomeStory.parameters = {
  axe: {
    disabledRules: ['select-name'],
  },
};
```

Rules can also be disabled globally by [setting this parameter for **all stories** in .storybook/preview.js](https://storybook.js.org/docs/react/writing-stories/parameters#global-parameters).

```jsx
// .storybook/preview.js

export const parameters = {
  axe: {
    disabledRules: ['select-name'],
  },
};
```

### waitForSelector

Wait for an arbitrary CSS selector after rendering before running the Axe checks. Useful if your component takes some time to render and actually display itself on the page.

```jsx
// SomeComponent.stories.jsx

export const parameters = {
  axe: {
    waitForSelector: '#some-component-selector',
  },
};
```

## Developing

If you want to work on this project or contribute back to it, see our [wiki entry on Development setup](https://github.com/chanzuckerberg/axe-storybook-testing/wiki/Development-setup).

## Inspiration

This project was originally based on [@percy/storybook](https://github.com/percy/percy-storybook).
