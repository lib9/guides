# Lib9 TypeScript Style Guides

This guide adds rules that aren't usually enforced by a formatter or a linter.
It is largely inspired by the [ts.dev Style Guide](https://ts.dev/style/).

For any style questions not covered in this guide,
do what the code in the same file or project already does: _be consistent_.

## File encoding: UTF-8

Files must be encoded in UTF-8.
However, you should restrict the use of _UTF-8_ characters to the _ASCII_ subset.

## Copyright Headers

[Choose a license](https://choosealicense.com/) for your project if you have not already done so.
The _MIT license_ is a good choice, as it is the [most used license among packages published on _NPM_](https://libraries.io/search?platforms=npm).
Ensure that the license is [compatible](https://en.wikipedia.org/wiki/License_compatibility) with the licenses of your dependencies.

Every file should have a copyright header in the following form:

```ts
//! Copyright (c) <file-creation-year> <authors>
//! Licensed under the <license-name> (<license-link>)
```

In the previous code, the exclamation mark signals a _legal comment_ `//! <legal comment>`.
Some bundlers such as _esbuild_ are able to preserve legal comments.

Note that you [should not modify the date of the copyright on change](https://matija.suklje.name/how-and-why-to-properly-write-copyright-statements-in-your-code#why-not-bump-the-year-on-change/).
For the same reasons, you [should not use a range of years](https://matija.suklje.name/how-and-why-to-properly-write-copyright-statements-in-your-code#why-not-use-a-year-range).

Note also that we use `Copyright (c)` over `©` to avoid using a non-ASCII character.

## Naming convention

Identifiers must use only _ASCII_ letters `A-Za-z`, digits `0-9`, underscore `_`, and dollar sign `$`.

| Naming style    | Category                                                             |
| --------------- | -------------------------------------------------------------------- |
| `PascalCase`    | class / decorator / enum / enum value / function / interface / types |
| `camelCase`     | function / module alias / function parameter / property / variable   |
| `CONSTANT_CASE` | module-level constant / static readonly property                     |
| `a-file.ts`     | filename                                                             |

-   Abbreviations in `PascalCase`

    Use `loadHttp` instead of ~~`loadHTTP`~~,
    unless required by a platform name (e.g. `XMLHttpRequest`).

-   No boolean variables with negative names

    ```diff
    - const isNotEnabled = true
    + const isEnabled = false
    ```

-   A prefix like `is`, `are`, `has`, `have` helps to identify boolean variables

    ```diff
    - const enabled = false
    + const isEnabled = false
    ```

-   Use infinitive verbs for mutating functions, past participle and nouns for pure functions

    For _API_ familiarity, you can deviate from this rule.

-   Module alias in `camelCase`

    ```js
    import * as awesomeModule from "./awesome-module.js"
    ```

    Some libraries use a module alias that deviates from this naming convention.
    The only libraries that currently fall under this exception are:

    -   [jQuery](https://jquery.com/), using `$`
    -   [Lodash](https://lodash.com/), using `_`
    -   [Underscore.js](https://underscorejs.org/), using `_`

-   functions in `camelCase`

    It can be practical to give a factory the same name as its return type.
    In this exceptional case, you can use a function name in `PascalCase`.

    ```ts
    type Person = { name: string }
    function Person(name: string): Person { return { name } }
    ```

-   `CONSTANT_CASE`

    `CONSTANT_CASE` indicates that a value is intended to be **Immutable**.

    ```js
    // Even though per the rules of JavaScript UNIT_SUFFIXES is mutable,
    // the uppercase indicates that users must not modify it.
    const UNIT_SUFFIXES = {
        milliseconds: "ms",
        seconds: "s",
    }
    ```

    In _TypeScript_, use `const` assertions to prevent accidental mutations:

    ```ts
    const UNIT_SUFFIXES = {
        milliseconds: "ms",
        seconds: "s",
    } as const
    ```

    Only symbols declared at module level or static property of a class can use `CONSTANT_CASE`.
    Local variables declared within a function must use `lowerCamelCase`.

-   Use single upper case character for type parameters

    Commonly used type parameters:

    | type parameter | meaning |
    | -------------- | ------- |
    | E              | Element |
    | K              | Key     |
    | T              | Type    |
    | V              | Value   |

-   No underscore `_` prefix and suffix

    Exceptions:

    -   Unused identifiers may be prefixed by an underscore (this may be required by some linters)
    -   Test utility functions
    -   Code generators may use underscore prefix and suffix for ensuring that the identifier is not a [_reserved word_](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#reserved_words)

-   No `$` prefix and suffix

    Except when aligning with naming conventions of third party frameworks.

-   No redundancy

    As TypeScript expresses information in types,
    names should not be decorated with information that is included in the type.

    Some concrete examples of this rule:

    -   No trailing or leading underscores for private properties or methods.
    -   No `opt_` prefix for optional parameters.
    -   No special prefix for interfaces, unless it's idiomatic in its context.
    -   Suffixing `Observables` with `$` is a common external convention.
        This avoids any confusion between `Observables` and concrete values.

-   React Components

    When required by _JSX_, components must use `UpperCamelCase`.

    ```diff
    - function goLink(props: GoLinkProps) { return <a href={...}>... }
    + function GoLink(props: GoLinkProps) { return <a href={...}>... }
    ```

## Make documentation that actually adds information

Use [`/** TSDoc */` comments](https://tsdoc.org/) to document your code.
You should document all properties and methods that are not self-explanatory.

-   Omit `@param` and `@returns` tags

    A summary is often enough.
    You can use these tags to add information.

    ```diff
     /**
      * Average of `x` and `y`.
    - *
    - * @param x - first value
    - * @param y - second value
    - * @returns Average of `x` and `y`.
      */
     function average(x: number, y: number): number { ... }
    ```

-   Don't declare types in `@param` or `@returns` blocks,
    don't write `@implements`, `@enum`, `@private`, `@override` etc.
    on code that uses the `implements`, `enum`, `private`, `override` etc.

-   Use `@throws`

    If your function may throw an exception,
    then you should document it with the `@throws` tag.

    ```ts
    /**
     * @throws {ParserError}
     */
    function parse(source: string): Ast {}
    ```

-   Use `@deprecated`

    ```ts
    /**
     * @deprecated Use {@link parse2} instead.
     */
    function parse1(source: string): Ast {}
    ```

-   No `@override`, use the `override` keyword

    `@override` is not enforced by the compiler.
    This causes the annotations and the implementation to be out of sync.

    ```diff
    class A extends B {
    -   /**
    -    * @override
    -    */
    -   f() { ... }
    +   override f() { ... }
    }
    ```

-   Use _Markdown_

    See the [Markdown style guide](./lib9-versioning-style-guide.md).
    You should not use headings `#`, thematic breaks `---`, and images `![]()` in documentation comments.

-   Use `{@link symbol}` to reference an exported symbol

    Otherwise, use _Markdown_ inline code.

-   Code examples

    You may explicitly separate an example from the summary thanks to `@example`.

    ````ts
     /**
      * Average of `x` and `y`.
      *
      * @example
      * ```js
      * average(4, 6)
      * 5
      * ```
      */
     function average(x: number, y: number): number { ... }
    ````

    Code examples should not contain additional comments:
    It is already inside a comment.
    If it needs further comments, then it is not a good example.

-   Use _ASCII_ art if it improves your documentation

    Some editors such as [_ASCII flow_](https://asciiflow.com/) can help to create diagrams.
    You can use standardized _ASCII_ art like [_Svgbob_](https://ivanceras.github.io/svgbob-editor/):

    ````md
    ```svgbob
    ┌──────┐     ┌──────┐
    │step 1├────►│step 2│
    └──────┘     └──────┘
    ```
    ````

    You can also generate [_ASCII_ diagrams from other textual formats](https://metacpan.org/dist/Graph-Easy/view/bin/graph-easy).

## Make informative comments

Comments have a bad reputation.
According to their detractors, they are redundant with good code.
While this is true for comments that restate what the code does,
it is not true for comments that explain why the code is in the way it is.
To find out whether a code has enough comments, read it after a few days and request reviews.

-   Explain high-level purpose

    It is often useful to add a comment that explains the purpose of a module.
    This can help readers to find what they search.
    You can also add keywords to ease searching.

-   Document implementation choice

    Every time you hesitate between several choices,
    tell about the alternatives and why you choose a solution over another one.
    This is a really valuable information for your readers and future self.

-   Document fixes

    A code added for fixing an issue should be documented.
    You can also provide the issue reference.

-   Highlight non-idiomatic code

    You should comment all codes that are not conventional or derive from the style guide.

    For instance, add the comment `// fallthrough` when a switch clause falls through:

    ```js
    switch (x) {
        case 1: {
            f()
            // fallthrough
        }
        case 2:
            g()
    }
    ```

-   Use tagged comments such as `TODO` and `FIXME`

    A tagged comment is a comment starting with a word in uppercase.
    This makes easier to search code for deficiencies or remarks.
    You should document which tags are usable in your codebase.

    ```js
    // SAFETY: this type assertion is ok because it is checked by ...
    ```

    You can also link an issue:

    ```ts
    // FIXME(#<issue>): <desc>
    // TODO(#<issue>): <desc>
    ```

    Suggested tags:

    -   **FIXME**: problematic or ugly code that needs refactoring or cleanup
    -   **HACK**: workaround to a known problem
    -   **SAFETY**: code that bypasses the type checker, but is safe
    -   **TODO**: task or feature to complete
    -   **WARNING**: code that could be misused

-   Use inline `// Comment.` instead of multi-line `/* Comment. */`

    You can use `/* Comment. */` to add information in an expression.
    For example:

    ```ts
    delay(5 /* ms */)
    ```

-   Add a space at the start of the comment

    ```diff
    - //Comment
    + // Comment
    ```

## Expressions and statements

-   Variables

    Prefer `const` over `let`.
    Don't use `var`.

    Declare and initialize each variable on their own line.

    ```diff
    - let foo, bar = 5
    + let foo
    + const bar = 5
    ```

-   No assignment in expressions

    Expressions are often considered as pure.
    i.e. without mutations and side effects.
    Thus, don't use assignment in expressions.

    ```diff
    - const y = (x += 1)*2
    + x += 1
    + const y = x*2
    ```

-   No comma expressions

    ```diff
    - const y = x++, x*2
    + x++
    + const y = x*2
    ```

-   Use `===` and `!==`

    The double equality operators cause an [error-prone type coercion](https://dorey.github.io/JavaScript-Equality-Table/).
    It makes code harder to understand and potentially slower to execute.

    However, you may use `== null` and `!= null` for comparing against `null` and `undefined`.

-   Use boolean expressions in conditions

    The type of all conditions must conform to `boolean|null|undefined`.
    This restriction avoids common pitfalls with implicit type coercion.
    The inclusion of `null` and `undefined` allows the use of optional chaining:

    ```ts
    if (o?.isEnabled) { ... }
    ```

-   Avoid `default` switch clauses

    Default switch clauses prevent TypeScript from performing an exhaustiveness check.

-   No labeled statements

    Labeled statements are remnants of `goto`.
    They allow writing spaghetti code and make harder flow analysis.

## Primitive types

-   `null` vs `undefined`

    Use either `null` or `undefined` to denote absence of a value.
    Nullable types can be constructed as a union type `string|null` or `string|undefined`.
    Avoid unions with both such as `string|null|undefined`.

    Favor _API_ familiarity.
    For instance, `Map.get` may return `undefined`.
    If you create a map-like structure, then use the same _API_ as `Map`.

    Otherwise, choose according to your use-case and the following differences:

    -   `null` is serializable in JSON.

        ```js
        JSON.parse(JSON.stringify({ prop: null })).prop === null
        ```

        This can simplify the writing of unit tests:

        ```js
        const data = JSON.stringify({ prop: null })
        assert.deepEqual(data, { prop: null })
        ```

    -   An `undefined` property or parameter is often treated as an omitted property or parameter.

        The following codes are equivalent:

        ```js
        const { prop = 5 } = {}
        const { prop = 5 } = { prop: undefined }

        function f(param = 5) {}
        f()
        f(undefined)

        JSON.stringify({}) === "{}"
        JSON.stringify({ prop: undefined }) === "{}"
        ```

    -   Optional chaining can return `undefined`.

        ```js
        const x = null
        const y = undefined
        x?.prop === undefined
        y?.prop === undefined
        ```

-   Strings and Unicode

    For non-ASCII characters, use the actual Unicode character (e.g., `∞`).
    For non-printable characters, use a hex or Unicode escapes (e.g., `\u221e`).

    ```js
    // Perfectly clear, even without a comment.
    const units = "μs"

    // Use escapes for non-printable characters.
    const output = "\ufeff" + content // byte order mark
    ```

-   No primitive wrapper instantiation

    Don't instantiate the wrapper classes `String`, `Boolean`, and `Number`.
    Wrapper classes have surprising behavior.
    For example, `new Boolean(false)` evaluates to `true`.

    ```diff
    - new String("hello")
    + "hello"
    ```

-   Type coercion

    You can use wrapper classes without `new` for explicit type conversion:

    ```js
    Number("2e1") === 2e1
    Number(1n) === 1
    String(5) === "5"
    String(false) === "false"
    ```

    Avoid string concatenation to cast to string.
    Use string templates or explicit conversions:

    ```diff
    - "count: " + 5
    + `count: ${5}`
    + "count: " + String(5)
    ```

    `Number`, `parseInt`, and `parseFloat` have subtle differences.
    Know the difference between `Number`, `parseInt`, and `parseFloat` to choose the one that fits your needs.

    `Number` takes any type of value and attempts to coerce it to a Number.
    All falsy values are converted to `0`.
    Strings containing spaces are also converted to `0`.
    `parseInt` and `parseFloat` return `NaN` when the parsing fails.

    ```js
    Number(false) === 0
    Number("") === 0
    Number(" \t\n\v") === 0
    Number("Infinity") === Number.POSITIVE_INFINITY
    Number("-Infinity") === Number.NEGATIVE_INFINITY
    Number.isNaN(Number("NaN"))

    Number.isNaN(parseInt(""))
    ```

    `Number` and `parseFloat` are able to parse scientific notations.
    `parseInt` and `parseFloat` truncate the string as soon as they encounter a non-parsable character.

    ```js
    Number("7e2") === 700
    parseFloat("7e2") === 700
    parseInt("7e2") === 7 // "7e2" is truncated to "7"
    ```

    `Number` and `parseInt` are able to parse hexadecimal numbers prefixed with `0x`.
    `parseInt` can also parse hexadecimal without the prefix `0x`.

    ```js
    Number("0xf") === 15
    parseInt("0xf") === 15
    parseInt("0xf", 16) === 15
    parseInt("f", 16) === 15
    ```

    To avoid these pitfalls, you should validate the parsed string and the returned value:

    ```js
    // parse decimals
    if (/^[-+]\d+$/.test(s)) {
        const n = parseInt(s, 16)
        if (Number.isSafeInteger(n)) { ... }
    }
    // parse hexadecimals
    if (/^(0x)?[a-f\d]+$/i.test(someString)) {
        const n = parseInt(someString, 16)
        if (Number.isSafeInteger(n)) { ... }
    }
    // parse floats
    if (/^[-+]?\d*(\d\.|\.\d)\d*(e[-+]?\d+)?$/i.test(someString)) {
        const n = parseFloat(someString)
        if (n === Number.POSITIVE_INFINITY || n === Number.NEGATIVE_INFINITY) { ... }
    }
    // parse hexadecimals and floats
    if (/^(0x)?[a-f\d]+|[-+]?\d*(\d\.|\.\d)\d*(e[-+]?\d+)?$/i.test(someString)) {
        Number(someString)
        ...
    }
    ```

-   No `isNaN` and `isFinite`, use `Number.isNaN` and `Number.isFinite`

    The global functions `isNaN` and `isFinite` are unsafe: they attempt a type coercion.

## Arrays and tuples

-   No `new Array(...)`

    `new Array()` has confusing and contradictory usages.
    Use literal arrays instead.

    ```diff
    - const a = new Array(2) // [undefined, undefined]
    - const b = new Array(2, 3) // [2, 3]
    + const c = [2, 3]
    ```

-   No sparse arrays

    Don't create sparse arrays — i.e., arrays with holes:

    ```diff
    - const sparseArray = [1, , 2]
    + const sparseArray = [1, null, 2]
    ```

    Note that this does not prevent you to use destructuring assignment with a hole:

    ```js
    const [, b] = f()
    ```

-   Use `push`

    ```diff
    - arr[arr.length] = 5
    + arr.push(5)
    ```

-   Use `T[]` over `Array<T>`

    And `readonly T[]` over `ReadonlyArray<T>`.

-   Use `readonly` tuples

    Tuples are generally considered immutable in other languages.
    You should always make `readonly` tuples.

    If you are tempted to create a type _Pair_, use a tuple type instead.

    ```diff
    - type Pair<T, U> = { readonly first: T, readonly second: U }
    - function split(address: string): Pair<string, string> { ... }
    + function split(address: string): readonly [host: string, port: string] { ... }
    ```

    If your tuple has three or more elements, then consider using an object literal instead.

    ```diff
    - function split(address: string): readonly [
    + function split(address: string): {
        protocol: string,
        host: string,
        port: string
    + } { ... }
    - ] { ... }
    ```

## Functions

-   Use top-level named functions

    ```diff
    - const f = function() { ... }
    - const f = () => { ... }
    + function f() { ... }
    ```

    You may use an arrow function when you wish assigning to a typed constant.

    ```js
    const f: FunctionSignature = () => { ... }
    ```

-   Use arrow functions for anonymous functions

    ```diff
    -  bar(function() { ... })
    +  bar(() => { ... })
    ```

-   Use default parameters over optional parameters

    ```diff
    - function ident(s: string, n?: number): string { ... }
    + function ident(s: string, n = 0): string { ... }
    ```

-   No mutable value for default parameters

    The value of a default parameter is shared between all function calls.
    Thus, you must not mutate the value.
    Use `readonly` types for protecting the value.

    ```diff
    + function f(arr: readonly string[] = []): string[] {
    - function f(arr: string[] = []): string[] {
    -     return arr
    +     return arr.slice()
      }
    ```

-   Group default and optional parameters into an option object.

    An optional parameter that's not in an options object might be acceptable if there is only one,
    and it seems inconceivable that we would add more optional parameters in the future.

    ```diff
    - function connect(hostname = "localhost", port = 8080): void { ... }
    + function connect({ hostname = "localhost", port = 8080 } = {}): void { ... }
    ```

-   No property addition

    Don't add properties to a function.

    ```diff
      function f() {}
    - f.id = (x) => x
    ```

-   No `.length`, `.name`

    `f.length` returns the number of parameters of a function `f`.
    It is not reliable because default and optional parameters are not counted.
    Moreover, some transpilers and bundlers can change the number of parameters.

    `f.name` returns the name of the function `f`.
    You should not rely on `f.name` because a minifier can change the name of a function.

-   Avoid overloaded functions

    ```diff
    - function f(a: number): void
    - function f(a: string): void
      function f(a: number | string): void { ... }
    ```

    The only valid use case for overloading is when the return type depends on the parameter types:

    ```ts
    - function f(a: number): number
    - function f(a: string): string
      function f(a: number | string): number | string { ... }
    ```

    Even in this case, you can create two functions to avoid overloading:

    ```ts
      function f1(a: number): number { ... }
      function f2(a: string): string { ... }
    ```

-   Avoid nested named functions

    Nested named function that are not closures - i.e. function that capture a variable of the super scope - should be extracted at the top-level of the module.

    ```diff
    + function g() { return 1 }
    function f() {
    -     function g() { return 1 }
        return g();
    }
    ```

-   No `this`

    `this` should only be used inside a method.
    It is error-prone to use `this` in functions.

-   Use early returns

    Early returns should highlight exceptions,
    while the main return should highlight the happy path.
    This improves the code readability and avoids too many nesting.

    ```diff
    function f() {
        if (a) {
            // exception 1
            return ...
        }
    -   else if (b) {
    +   if (b) {
            // exception 2
            return ...
        }
    -   else { return ... }
    +   return ...
    }
    ```

-   `void` vs `undefined`

    `void` should only be used as return type, including in generics that appear in return types.

    A function with `void` as return type should not have `return <value>` instructions.
    A `return` without a value is allowed as control flow instruction.

    In contrast, a function with `undefined` as return type must explicitly return `undefined`.

-   No side effects

    All functions should only mutate their parameters.
    For instance, the following function has a side effect that modifies `env`.

    ```ts
    const env = { name: "" }
    function modifyEnv(): void {
        env.name = "Lea"
    }
    ```

    However, side effects for logging purpose are allowed:

    ```ts
    function logSideEffect(): void {
        console.log("side effect")
    }
    ```

    Note that methods can mutate their implicit parameter `this`.

## Exceptions

-   Only `throw` instances of `Error` or derived classes

    Literal values don't get a stack trace filled in, making debugging hard.

    ```diff
    - throw "message"
    + throw new Error("message")
    ```

    Consider writing your own error type:

    ```ts
    class CustomError extends Error {
        override name = "CustomError"
        readonly data: Data
        constructor(data: Data) {
            super("message")
            this.data = data
        }
    }
    ```

    Don't forget to set the `name` property to the name of your class.

-   Catching and re-throwing

    Use a type narrowing to handle an error.
    And re-throw all other kind of errors.
    This allows to forward unknown errors.

    ```js
    try {
        canThrow()
    } catch(e) {
        if (e instanceof CustomError) { ... }
        throw e
    }
    ```

-   Don't catch all kind of errors

    Some errors are not meant to be caught.
    They signal a misused_API_.
    For instance:

    -   `AssertionError`
    -   `InternalError`
    -   `RangeError`
    -   `ReferenceError`
    -   `SyntaxError`
    -   `TypeError`

    You should distinguish errors that signal _API_ misuse or unrecoverable errors from recoverable and expected errors.
    The former group should not be caught, while the later should be caught and handled.

## Enums

-   Use `enum` initializers

    All `enum` values must be initialized either with integers, or strings that match the `enum` name.

    ```diff
    enum Gender {
    -   Female,
    -   Fluid,
    -   Male,
    +   Female = 0,
    +   Fluid = 1,
    +   Male = 2,
    // or
    +   Female = "Female",
    +   Fluid = "Fluid",
    +   Male = "Male",
    }
    ```

    In some rare cases, you can use string values that don't match the enum name:

    ```ts
    enum Punctuation {
        Comma = ",",
        Dot = ".",
    }
    ```

    Flags are allowed:

    ```ts
    enum Flags {
        A = 1,
        B = 1 << 1,
        AB = A | B,
    }
    ```

-   No negative `enum` values

    Negative `enum` value produce deoptimizations.
    Fallback to constants for using negative integers:

    ```ts
    type Order = -1 | 0 | 1
    const LOWER = -1
    const EQUAL = 0
    const UPPER = 1
    ```

-   No computed access on `enum`

    This implies you cannot look up the name of a member from its value.

    ```diff
      enum Gender {
         Female = 0,
         Fluid = 1,
         Male = 2,
      }
    - Gender["FEMALE"]
    + Gender.FEMALE
    - Gender[0]
    ```

-   No `const enum`

    `const enum` forces the _TypeScript Compiler_ to inline the `enum`.
    `const enum` is not supported by bundlers and is incompatible with the `isolatedModules` _TypeScript_ mode.
    Their use can lead to import non-existent values (because `const enum` is erased).
    Moreover, some bundlers, such as _esbuild_, are able to inline `enum` anyway.

    ```diff
    - export const enum Gender {
    + export enum Gender {
        Female = "Female"
        Fluid = "Fluid"
        Male = "Male"
    }
    ```

## Object literals

-   Assume prototype-less object literal

    It is considered as a bad practice to call an inherited method of the `Object` prototype,
    as this method can be overridden.
    Moreover, an object literal can be created with a prototype set to null `Object.create(null)`.
    Thus, you should assume that an object literal doesn't inherit of the methods of `Object`.

    ```diff
      const x = {}
    - x.hasOwnProperty("a")
    + Object.hasOwnProperty.call(x, "a")
    + // or in ES2022:
    + Object.hasOwn(x, "a")
    ```

-   No methods, getters, and setters on object literals

    If you really need methods, getters, or setters, then use a class.

    ```diff
    - const x = {
    -     method() { ... }
    -     get x() { ... }
    -     set x(val) { ... }
    - }
    + const x = new X()
    + class X {
    +     method() { ... }
    +     get x() { ... }
    +     set x(val) { ... }
    + }
    ```

-   Use object destructuring assignment

    An object destructuring assignment can be easier to read.

    ```diff
    - const a = x.a
    - const b = x.b
    + const { a, b } = x
    ```

    Sometimes, this allows removing a variable:

    ```diff
    + function f({ a, b }) {
    - function f(x) {
    -     const a = x.a
    -     const b = x.b
          ...
    }
    ```

-   `.dotted` vs `[computed]` property access

    Use `[computed]` for dynamic access:

    ```js
    function(o) {
        for(const p in o) {
            const val = o[p]
        }
    }
    ```

    Otherwise, use `.dotted`.

-   Use fixed object shapes

    _JavaScript_ engines optimize code path that use the same object shapes.
    i.e. object with the same property names initialized in the same order.
    You should ensure a consistent order in the declaration and initialization of properties:

    ```diff
    type ServerConfig = {
        readonly host: string,
        readonly port: number,
    }
    const configs = [
        { host: "localhost", port: 8080 },
    -   { port: 8081, host: "localhost" },
    +   { host: "localhost", port: 8081 },
    ]
    ```

    You should only use optional properties at the boundary of your library where _API_ ergonomics is important.
    In this case, you can rely on the `Partial<T>` type:

    ```js
    function ServerConfig({
        host = "localhost",
        port = 8080,
    }: Partial<ServerConfig>): ServerConfig {
        return { host, port }
    }
    ServerConfig({})
    ServerConfig({ port: 8081 })
    ```

    If you use unions of object literal types, ensure they have the same shape:

    ```ts
    type EmptyTree = { readonly value: null; readonly next: null }
    type Tree<V> = { value: V; next: Tree<V> } | EmptyTree
    ```

    To create union of object literals with distinct shapes, you can use tagged unions:

    ```ts
    type AB = { tag: "TypeA"; val: TypeA } | { tag: "TypeB"; val: TypeB }
    const ab: AB = ...
    switch (ab.tag) {
        case "TypeA": ...
            break
        case "TypeB": ...
            break
    }
    ```

-   No `delete`

    Deleting a property changes the shape of the object.

-   Avoid spreading objects

    Copying an object with an object spread [doesn't preserve its shape for now](https://bugs.chromium.org/p/v8/issues/detail?id=9343#c13).
    For a simple copy, always copy all properties explicitly:

    ```diff
      const x = { a: 1, b: 2 }
    - const y = { ...x, b: 3 }
    + const y = { a: x.a, b: 3 }
    ```

    For more complex copying, you can resort to object spread.
    If the copy causes too much deoptimization and hurts performance,
    then you can use a trick to preserve the shape of the object:

    ```js
    function spread(x) {
        return { a: 0, b: 0, ...x }
    }
    const x = { a: 1, b: 2 }
    const y = spread({ ...x, b: 3 })
    // or
    const y = spread({ b: 3 })
    ```

## Classes

-   Use `implements <interfaces>`

    Although TypeScript is structurally typed, you should implement an `interface` explicitly.
    This ensures that your `class` implements the `interface` correctly.

    A class must only explicitly implement interfaces.
    Don't explicitly implement a type alias.
    Similarly, an interface must only extend other interfaces.

-   Composition over inheritance

    You should avoid inheritance and extension.
    Make sure you only extend classes that are designed to be extended.
    For example, an `abstract class` is expected to be extended.

    When extending a class, avoid overriding methods and properties.
    Ensure that an instance of your class respects the invariants of the extended class.
    In other words, you must respect the [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle/).

    Don't extend standard types such as `Boolean`, `BigInt`, `Number`, `String`, `Array`, `Map`, `Set`, `Symbol`, `Function`, etc.

-   Use consistent declaration order of `class` members

    1.  static properties (constants)
    2.  static getter and paired static setters
    3.  static methods (factory methods)
    4.  properties
    5.  constructor
    6.  getter and paired setters
    7.  methods

-   No `static` initialization blocks, avoid `static` members

    Use top-level declarations instead.
    You can use static members for factory methods.

    ```diff
    + const CONSTANT = 5
    class A {
    -   static { ... }
    -   static CONSTANT = 5
        static createA(): A { ... }
    }
    }
    ```

-   No `public`, avoid `protected`, use `#private` instead of `private`

    Restricting the visibility of properties, methods, and entire types helps to keep code decoupled.

    Members are public by default.
    Thus, don't use `public`.

    Avoid `protected`: it is often used in an architecture that favors inheritance.
    You should favor composition over inheritance.

    If you target ES2022 or above, then use `#private` instead of `private`.
    Otherwise, use `private` to avoid substantial emit size and performance degradation.

    ```diff
    class AwesomeClass {
    -    private property: number
    +    #property: number
    }
    ```

    You should not mix both `#private` and `private`.
    You should also avoid using public and private identifiers with the same name.
    This is confusing.
    This also simplifies switching from `private` to `#private` and vice-versa.

    ```diff
    class AwesomeClass {
    -    property: number
    -    #property: number
    +    public_property: number
    +    #private_property: number
    }
    ```

    Consider converting `private` methods to non-exported named functions:

    ```diff
    class A {
    -     #f() {}
    }
    + function f(a: A) {}
    ```

-   Use property initializers

    If a class property is initialized with a constant, initialize it where it's declared.
    Sometimes this allows you to omit the constructor altogether.

    ```diff
    class Foo {
    -    private readonly userList: string[]
    -    constructor() { this.userList = [] }
    +    private readonly userList: string[] = []
    }
    ```

-   No useless `constructor`

    It is unnecessary to declare an empty `constructor` or one that delegates into its parent class.
    _JavaScript_ provides a default class constructor if one is not declared.

    ```diff
    class UnnecessaryConstructor {
    -   constructor() {}
    }
    ```

    ```diff
    class UnnecessaryConstructorOverride extends Base {
    -   constructor(value: number) { super(value) }
    }
    ```

-   No optional properties

    An instance should have a fixed object shape.

    ```diff
    class AwesomeClass {
    -   name?: string
    +   name: string | null
    }
    ```

-   No parameter properties

    [Parameter properties](https://www.typescriptlang.org/docs/handbook/2/classes.html#parameter-properties/) may be unfamiliar to newcomers.
    Moreover, they are not compatible with the new syntax for private members `#property`.

    ```diff
    class Awesome {
    +   readonly property: number;

    -   constructor(readonly property: number) {}
    +   constructor(property: number) {
    +       this.property = property;
    +   }
    }
    ```

-   Avoid getters and setters

    Use regular properties and methods instead.

    You can use getters and setters if it makes the _API_ more familiar,
    or if a user might expect a property.

    ```ts
    class LinkedList {
        get length(): number { ... }
    }
    ```

    Getters and setters should mimic a property.
    In particular:

    -   A getter must be a pure (no side effects) and deterministic function.
    -   The setter should modify the value returned by the getter with the value passed to it.

    Getters and setters are notably useful for modifying the _API_ in a backward-compatible way.
    For example, computing a property instead of storing it:

    ```diff
    class List {
    -    isEmpty: boolean // Version 1
    +    get isEmpty(): boolean { ... } // Version 1.1
    }
    ```

-   Use `this` in methods, constructors, getters, and setters

    All non-static class methods should use `this`.
    Otherwise, the method should be extracted into a top-level named function.

-   Avoid `@Decorator`

    Don't define new decorators.
    Only use the decorators defined by frameworks.

    -   Angular: `@Component`, `@NgModule`, etc.
    -   Polymer: `@property`, etc.

-   Assume immutable prototypes

    The methods of a class must not be re-assigned.

## Source organization

-   Place imports at the top of the file after the [copyright header](#Copyright-Headers)

    Imports should be sorted.

-   Use relative import paths with extensions

    Use relative imports `./foo` rather than absolute imports `path/to/foo`
    when referring to files within the same (logical) project.
    Consider limiting the number of parent steps `../../../`
    as those can make module and path structures difficult to understand.

    Use relative imports with extensions.
    The extension should end with `.js` or `.ts` (_Deno_).

-   No circular imports

    This prevents partial evaluation and some hard-to-catch errors.
    Module dependencies should form a Directed Acyclic Graph.

-   Module imports vs destructuring imports

    Despite the `*`, a module import is not like a "wildcard" import as seen in other languages.
    Instead, module imports give a name to the whole module.
    All symbols of the module are thus qualified with the attached module name.
    This makes the code more readable and provides autocompletion on all symbols in a module.
    They also require less import churn (all symbols are available), fewer name collisions,
    and allow shorter names in the imported module.
    Module imports are particularly useful when using many different symbols from large APIs.

    ```js
    import * as utils from "./utils.js"
    ```

    Destructuring imports give local names to every imported symbol.
    They allow for more concise code when using the imported symbol,
    which is particularly useful for very commonly used symbols.

    ```js
    import { test, expect } from "vitest"
    ```

    Use module imports instead of destructuring imports to resolve naming conflicts:

    ```diff
    - import { x as y } from "./utils.js"
    - const x = y
    + import * as utils from "./utils.js"
    + const x = utils.x
    ```

-   Use `import type`

    All values that are only types (e.g. `type` and `interface`)
    must be imported with `import type` or `import { type ... }`.
    This allows the use of modern bundlers such as _esbuild_.

-   No `namespace`

    `namespace` and `module` are legacy _TypeScript_ constructs.
    Use _ECMAScript Modules_ (_ESM_) instead.
    In contrast to TypeScript `namespace` and `module`, _ESM_ can be optimized by bundlers.

    ```diff
    - namespace AwesomeModule { ... }
    - module AwesomeModule { ... }
    ```

    Note that you can use [ambient modules](https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html) for typing third-party modules.

-   Treat modules, `enum`, and `class` like namespaces

    When importing a module, access its properties directly rather than passing the module around.
    This ensures that a module can be analyzed and optimized.

    ```diff
    import * as utils from "./utils.js"
    - f(utils)
    + f({ a: utils.a, b: utils.b })
    ```

    Enums and classes should also be treated like namespaces.
    Don't pass an `enum` or a `class` to a function and don't assign them to variables.

-   Constant declarations

    You should declare constants at the beginning of a module without any imports.
    Some bundlers, such as _esbuild_, take advantage of this to inline constants.

    ```diff
    - import ...
    export const MAX_U32 = 2**32 - 1
    ```

-   No `export default`

    This ensures import uniformity and avoids historical incompatibilities between transpilers.

    ```diff
    -  export default function f() {}
    +  export function f() {}
    ```

-   Use inline exports

    ```diff
    - const A = ...
    - export { A }
    + export const A = ...
    ```

-   No `export * as`

    This type of export is confusing because this makes a namespace looks like an exported object.
    Also, some bundlers such as _esbuild_ are not able to optimize this kind of exports.

    You can freely use `export * from`, `export { X } from`, and `export { X as Y } from`.

-   Write side-effect-free modules

    All exports must be free of side effects.
    This is a requirement for dead code elimination.

    If a constant is built from a function call,
    the function must be preceded by the comment [`/*@__PURE__*/`](https://esbuild.github.io/api/#pure).
    This informs the bundler that the call is pure.

    ```diff
    - export const X = createX()
    + export const X = /*@__PURE__*/ createX()
    ```

-   Place non-exported functions and classes at the end of the file

    This will allow the reader of your code to quickly scan for exported functions.

## Type system

-   Type inference

    Rely on type inference as implemented by the TypeScript compiler for all expressions.
    This simplifies code refactoring.

    However, an `export` must be explicitly typed if its type cannot be syntactically inferred.
    This allows every file to be type-checked separately.
    An `export` is syntactically inferred if it is initialized with a literal value
    that is recursively composed only of literal values.

    ```diff
    // Type of `f()` cannot be syntactically inferred.
    - export const X = { a: f() }
    + export const X: { readonly a: number } = { a: /* @__PURE__ */ f() }

    export const Y = { b: 5 } as const
    ```

    All named functions, even those that are not exported, must be explicitly typed.
    This improves code documentation.

    ```diff
    - function doNothings(s) { ... }
    + function doNothings(s: string): void { ... }
    ```

    Similarly, class members must be explicitly typed.

    ```diff
    class Person {
    -   name
    +   name: string

    -   method() { ... }
    +   method(): void { ... }
    }
    ```

-   Nullable type aliases

    Type aliases should not include `|null` or `|undefined` in a union type.
    This typically indicates that null values are being passed around through too many layers.
    They also make it unclear when certain values may be missing from a type.

    Instead, code should only add `|null` or `|undefined` when the alias is actually used.
    Code should deal with null values close to where they occur, using the techniques above.

    ```diff
    - type Coffee = Latte | Americano | null
    + type Coffee = Latte | Americano

    - function drink(): Coffee { ... }
    + function drink(): Coffee | null { ... }
    ```

-   Optional `?` vs `|undefined` type

    Optional marker `?` indicates that a property or a parameter can be omitted.
    `|undefined` indicates that `undefined` is a valid value for the property or parameter.
    TypeScript implicitly adds `|undefined` to optional properties.
    You should not rely on this implicit behavior
    and always add `|undefined` when it is a valid value for the property.

    ```diff
    type Named = { name?: string }
    - const x: Named = { name: undefined }
    + const x: Named = {}
    ```

-   Assume nominal typing for `class`

    TypeScript does not support nominal typing.
    That is, you should not assign object literals where `class` instances are expected.

    ```diff
    class Person { name: number ... }
    class MoralPerson { name: number ... }
    function f(a: Person): void { ... }
    - f({ name: "Eva" })
    - f(new MoralPerson("AwesomeCoop"))
    + f(new Person("Eva"))
    ```

-   `interface` vs object `type` alias

    You should treat `interface` and object `type` alias [like _Flow_](https://flow.org/en/docs/types/interfaces):

    -   Object `type` for object literals
    -   `interface` for object literals and `class` instances

    ```diff
    interface Named { name: string }
    type Person = { name: string }
    class MoralPerson implements Named {}

    let n: Named; let p: Person; let m: MoralPerson
    n = p
    n = m
    - p = n
    - p = m
    - m = n
    - m = p
    ```

    Because of this distinction, an `interface` should not extend a `type` alias.

-   Assume exact `type`

    Non-exact types must be explicitly marked:

    ```ts
    // Exact type
    type Person = {
        name: string
    }
    const p1: Person = {
        name: "Eva",
    }

    // Inexact type
    type InexactPerson = {
        name: string
        [extra: string]: unknown
    }
    const p2: InexactPerson = {
        name: "Eva",
        age: 25,
    }
    ```

    You may also use an `interface` for inexact types.
    Note that this allows also using class instances (assuming nominal typing for classes).

    ```ts
    interface Named {
        name: string
    }
    class Person implements Named { ... }

    const p3: Named = {
        name: "Eva",
        age: 25,
    }
    const p4: Named = new Person("Eva", 25)
    ```

    _TypeScript_ checks exactness when you assign a literal to a typed variable and argument.
    You should leverage this to catch exactness errors.

    ```ts
    type Person = {
        name: string
        age: number
    }

    const p0: Person = { name: "Eva", age: 25, isAdult: true } // ERROR
    const p1 = { name: "Eva", age: 25, isAdult: true }
    const p2: Person = p1 // NO ERROR

    withAdultFlag({ name: "Eva", age: 25, isAdult: true }) // ERROR
    withAdultFlag(p1) // NO ERROR

    function withAdultFlag({ name, age }: Person): Person {
        if (age >= 18) {
            return { name, isAdult: true } // ERROR
        }
        const result = { name, isAdult: false }
        return result // NO ERROR
    }
    ```

-   Use `readonly`

    Mark properties that are never reassigned with the `readonly` modifier.
    These need not be deeply immutable.

    ```diff
    type Person = {
    -    name: string
    +    readonly name: string
    }
    ```

    Class properties may also be marked as `readonly`:

    ```diff
    class AwesomeClass {
    -   name: string
    +   readonly name: string[]
    }
    ```

-   Use type parameters with caution

    Avoid types and functions with many type parameters.
    This makes your _API_ harder to use and understand.

    Think twice before adding a type parameter.

-   Avoid mapped and conditional types

    [Mapped types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html)
    and [conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html)
    are advanced features.
    They come with edge cases and make the code harder to understand.

    You should avoid using them and rely on the simplest type construct that can express your code.
    A few repetitions are often cheaper than the long-term cost of complex types.
    Use the following [utility types](https://www.typescriptlang.org/docs/handbook/utility-types.html#instancetypetype)
    before resorting to mapped and conditional types: `Partial`, `Readonly`, and `Record`.

    Avoid the following utility types: `Pick`, `Omit`, `Exclude`, `Extract`, `NonNullable`, and `required`.
    They can usually be replaced by plain types.

-   No `any`, use `unknown`

    `any` is dangerous: it allows assignment into any other type and dereferencing any property from it.
    Use `unknown` instead, or a type assertion as a last resort.

-   No `@ts-ignore`, `@ts-expect-error`, `@ts-nocheck`

-   No non-null assertions `!`

    Non-null assertions (`x!`) are unsafe and unfamiliar.
    Use optional chaining `?` combined and nullish coalescing `??`, or in last resort type assertions.

    ```diff
    - prop!.doSomething()
    + prop?.doSomething()
    ```

-   Use type assertions `as`

    Avoid type assertions.
    However, sometimes it is necessary to avoid complex types that use mapped and conditional types.
    In this case use the `as` syntax, and add a comment to indicate why the assertion is valid:

    ```diff
    // z must be Awesome because ...
    - const y = <Awesome>z.length
    + const y = (z as Awesome).length
    ```

-   Type assertions and object literals

    Use type annotations instead of type assertions to specify the type of object literals.

    ```diff
    - const {
    + const Person = {
        bar: 123,
        bam: "abc", // no error!
    + }
    - } as Person
    ```

    For constants, you can also use `as const` assertions:

    ```ts
    const ANONYMOUS = { name: "Anonymous" } as const
    ```

-   Nominal type aliases

    This is an advanced feature that makes the code harder to read and understand.
    However, it can avoid severe issue in some cases.
    Use with caution.

    _TypeScript_ does not support nominal type aliases natively.
    _Flow_ supports nominal type aliases through [sub-typing of opaque types](https://flow.org/en/docs/types/opaque-types/#toc-subtyping-constraints).
    You can emulate nominal types in _TypeScript_ using a [brand](https://basarat.gitbook.io/typescript/main-1/nominaltyping#using-interfaces).
    A brand is a fake property that changes the shape of the aliased type.
    For example:

    ```ts
    /** @phantom */
    declare const PERCENTAGE_BRAND: unique symbol

    export type Percentage = number & { readonly [PERCENTAGE_BRAND]: never }

    export function Percentage(n: number): Percentage {
        assert(n >= 0)
        return n as Percentage
    }

    const p = Percentage(5)
    ```

    ```ts
    /** @phantom */
    declare const PERSON_BRAND: unique symbol

    export type Person = {
        readonly name: string
        readonly [PERSON_BRAND]: never
    }

    export function Person(p: Omit<Person, typeof PERSON_BRAND>): Person {
        assert(p.name.length > 0)
        return p as Person
    }

    const p = Person({ name: "Hari Seldon" })
    ```

    We use the _JSDoc_ tag `@phantom` to signal that the constant does not exist at runtime
    and thus must only be used in type contexts.

    We chose the type `never` for our fake property.
    Nothing except a `never` value can be assigned to `never`.
    This signals that the property should not be read.

    Nominal types allow checking invariants.
    In the previous example, we check that a person has a non-empty name.
    In this case, you should only use brand for immutable types to avoid invalidating data.
