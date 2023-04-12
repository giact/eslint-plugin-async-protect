# `@giact/eslint-plugin-async-protect`
Async functions are great, especially when combined with the `await` keyword. That being said it isn't always obvious whether a function is asynchronous or not. It is also relatively easy to forget to stick an `await` on a function. These can cause dome difficult to locate bugs and can waste significant time.

This plugin tries to prevent some of these problems. The other major benefit is because this encourages a naming convention, these rules can apply across different files.

This ESLint plugin enforces the following:

 - functions defined with the `async` keyword should have an `Async` suffix on the name
 - calls to functions with an `Async` suffix on the name should be called with `await`

## Installation
Package: https://www.npmjs.com/package/@giact/eslint-plugin-async-protect

Assuming that you already have ESLint installed, simply run:

```
npm install --save-dev @giact/eslint-plugin-async-protect
```

## Configuration
Within your `.eslintrc` file you need to include the plugin, and specify the level the rules should apply:
```json
{
   "plugins": [
      "async-protect"
   ],
   "rules": {
       "async-protect/async-suffix": "error",
       "async-protect/async-await": ["warn", { "checkMissingAwait": true, "checkExtraAwait": true }],
   }
}
```
The general recommendation is to use `warn` for the `async-await` rule. This is because for 3rd party code you do not have control over function names, and the plugin may complain at extraneous `await` keywords.

The `async-await` rule has two options:

* `checkMissingAwait`: Checks if functions with an `Async` suffix are called with an `await` or `return` (default: `true`).
* `checkExtraAwait`: Checks if functions without an `Async` suffix are called without an `await` or `return` (default: `true`).

You can set these options to `true` or `false` according to your requirements.

## Rules

### async-suffix
This rule enforces that `async` functions have an `Async` suffix to their name. Functions not defined as `async` should not have the suffix. Here are some examples:

##### valid
```js
const fooAsync = async function() {}
async function fooAsync() {}
class Bar {
   async fooAsync() {}
   foo() {}
}

function foo() {}
const foo = function() {}
```

##### invalid
```js
const foo = async function() {}
async function foo() {}
class Bar {
    async foo() {} // invalid
    fooAsync() {}  // invalid
}

function fooAsync() {}
const fooAsync = function() {}
```

### async-await
This rule enforces that all functions with an `Async` suffix on their name should be called with an `await` or `return` if `checkMissingAwait` is enabled, and that functions without the suffix should not be called with an `await` or `return` if `checkExtraAwait` is enabled.

There are times when you may wish for an `async` function to run in the background, or where you need to call 3rd party code that doesn't follow the `async-suffix` naming convention. In that case use `// eslint-disable-line async-protect/async-await` to disable the rule for that line.

##### valid
```js
await fooAsync();
return fooAsync();
const result = await fooAsync();
const result = (await fooAsync()).result;
const result = await foo().bar().bazAsync();

const result = foo();
```

##### invalid
```js
fooAsync();
const result = fooAsync();
const result = fooAsync().result;
const result = foo().bar().bazAsync();

const result = await foo();
```

Remember that the valid and invalid examples may vary based on the `checkMissingAwait` and `checkExtraAwait` options provided in the configuration.