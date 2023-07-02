---
title: Fix missing Cypress type definitions  
description: How-to fix missing Cypress type definitions in VS Code
date: 2023-07-01
readingTime: 1 minute
tags: ["cypress", "typescript"]
---

{% image "./blog/2023/07/01/dead-guy-at-computer.jpg", "Skeleton working on a computer", "(max-width: 640px) 100%, 100%", page.url %}

This issue was just annoying enough to warrant highlighting the fix.

## TL;DR

Add Cypress to the "include" property in your tsconfig.json file.

```json
// tsconfig.json
  "compilerOptions": {
    // The "include" option goes outside of the compilerOptions
  },
  "include": ["src", "cypress"], // <------
  "exclude": ["./tests", "**/*.test.*"],
```

The other hack is to add this comment to the top of your spec files. This halfway worked for me but still didn't recognize `cy.mount()`.

```js
/// <reference types="Cypress" />
```

{% image "./blog/2023/07/01/almost-there.png", "Code error in editor, almost fixed.", "(max-width: 640px) 100%, 100%", page.url %}

## The Problem

I'm setting up Cypress testing on my React apps and VS Code was not recognizing the Cypress type definitions. The code still compiled and worked fine.

This is not what you want to see.

{% image "./blog/2023/07/01/missing-type-defs.png", "Code error in editor", "(max-width: 640px) 100%, 100%", page.url %}

{% image "./blog/2023/07/01/ts-error.png", "TypeScript error message", "(max-width: 640px) 100%, 100%", page.url %}

Installing types for Jest and Mocha as per the warning message doesn't work and isn't what you want anyway. We want Cypress types. Installing types for Cypress is just a stub and does nothing.

Adding Cypress to `types` under `compilerOptions` in tsconfig.json just gives another error.

```json
{
  "compilerOptions": {
    //...options...
    "types": ["cypress"]
  }
}
```

{% image "./blog/2023/07/01/tsconfig-error.png", "TypeScript configuration error message", "(max-width: 640px) 100%, 100%", page.url %}

The solution above gets rid of all the errors but took way too much messing around and Googling to find. I hope this helps some other poor sap who just wants to write some tests!

## Sources

[The Solution](https://blog.bitsrc.io/how-to-solve-property-mount-does-not-exist-in-cypress-and-also-fix-custom-commands-errors-2ebfd093c32a)
[GitHub Issue 1236](https://github.com/cypress-io/cypress/issues/1236)
[GitHub Issue 1152](https://github.com/cypress-io/cypress/issues/1152)
