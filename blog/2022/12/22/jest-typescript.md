---
title: Convert Jest Tests to TypeScript
description: Convert Existing Jest Unit Tests from JavaScript to TypeScript
date: 2022-12-22
readingTime: 2 minutes
tags: ["typescript", "javascript", "jest", "nodejs", "testing"]
---

{% image "./blog/2022/12/22/code.jpg", "Code on computer screen", "(max-width: 640px) 100%, 100%", page.url %}

This post will show you how to convert your existing unit tests written in [Jest](https://jestjs.io/) from JavaScript to TypeScript using [ts-jest](https://kulshekhar.github.io/ts-jest/). It assumes you already have a working Nodejs project with tests written and Jest and TypeScript installed.

## Install Necessary Packages

Install `ts-jest`, `ts-node`, and `@types/jest` as development dependencies in your Nodejs project.

```shell
npm install --save-dev ts-jest ts-node @types/jest
```

## Configure TypeScript

Add Node and Jest to the `types` property in your TypeScript configuration file.

```json
// ./tsconfig.json
{
  "compilerOptions": {
    "types": ["node", "jest"],
    // Lots of other things...
  },
}
```

If you haven't already, you probably also want to exclude your test files from being transpiled by TypeScript alongside your distributable production code.

Add the `exclude` option to your `tsconfig.json` root config. It **does not** go under `compilerOptions`. You can exclude entire directories or pattern match files if your tests are co-located with your code.

```json
// ./tsconfig.json
{
  "compilerOptions": {}, // Most of your TypeScript options are here.
  "exclude": ["./tests", "**/*.test.*"]
}


```

## Configure ts-jest

If you use the `npx ts-jest config:init` wizard it will write a default config to `./jest.config.js`. **Don't do this.** It leaves you with a `.js` file hanging around in your project simply for the Jest config.

Manually create a `jest.config.ts` (note the `.ts` extension) file with the below configuration.

```js
// ./jest.config.ts
import type { Config } from '@jest/types'

const config: Config.InitialOptions = {
  preset: 'ts-jest',
  testEnvironment: 'node',
  testPathIgnorePatterns: [".d.ts", ".js"],
  verbose: true,
}
export default config
```

## Check Your Test Command Script

If you were previously running `tsc && jest` to build your code before running the unit tests you can now skip the build step. Now, you can just directly run Jest against your .ts files and save a bunch of time on each test cycle. Winning!

```json
// ./package.json example
  "scripts": {
    "build": "tsc",
    // "test": "tsc && jest", <-- No more build before testing
    "test": "jest",
  }
```

## Run Tests

You should now be able to run `npm test` at the command line to successfully run your Jest unit tests, just like before.

## Tests Running Twice?

Are you seeing your Test Suites run twice? This will only be a problem if you **have not** excluded your test files from being transpiled by TypeScript as noted above. Otherwise, Jest will find both `*.test.ts` and `*.test.js` files and run them both. You can avoid this duplication at this point by adding the `.js` file extension to `testPathIgnorePatterns` in your `jest.config.ts` file as seen above.

Happy Testing!

## Sources

[Swizec - How to configure Jest with TypeScript](https://swizec.com/blog/how-to-configure-jest-with-typescript/)
[ts-jest Documentation](https://kulshekhar.github.io/ts-jest/docs/)
