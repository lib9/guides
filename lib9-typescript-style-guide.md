# Lib9 TypeScript Style Guides

Source code formatting and linting should be automated.
This guide adds rules that are generally not enforced by a formatter or a linter.
It is largely inspired by the [ts.dev TypeScript Style Guide](https://ts.dev/style/).

For any style question that isn't settled definitively by this guide, do what the other code in the same file is already doing: _be consistent_.
If that doesn't resolve the question, consider emulating the other files in the same directory.

## File encoding: UTF-8

Files must be encoded in UTF-8.
However, you should restrict the use of UTF-8 characters to the ASCII subset.

## Copyright Headers

Every file should have a copyright header in the following form:

```ts
//! Copyright (c) <file-creation-year> <authors>
//! Licensed under the <license-name> (<license-link>)
```

If the code originates elsewhere, ensure that the file has the proper copyright
headers. The license should be compatible with the project's license.

The exclamation mark signals a _legal comment_ `//! <legal comment>`.
Some bundler such as _ESbuild_ recognize this mark and are able to preserve legal comments.

Note that you [should not modify the date of the copyright on change](https://matija.suklje.name/how-and-why-to-properly-write-copyright-statements-in-your-code#why-not-bump-the-year-on-change/).
For the same reasons, you [should not use range of years](https://matija.suklje.name/how-and-why-to-properly-write-copyright-statements-in-your-code#why-not-use-a-year-range).

Note also that we use `Copyright (c)` over `©` to avoid using a non-ASCII character.

## Naming convention

Identifiers must use only ASCII letters `A-Za-z`, digits `0-9`, underscore `_`, and the dollar sign `$`.

| Naming style     | Category                                                                             |
| ---------------- | ------------------------------------------------------------------------------------ |
| `UpperCamelCase` | class / decorator / enum / enum value / function / interface / type / type parameter |
| `lowerCamelCase` | function / method / module alias / parameter / property / variable                   |
| `CONSTANT_CASE`  | class constant / enum value / global constant                                        |
| `a-file.ts`      | filename                                                                             |

-   Abbreviations in `UpperCamelCase`

    Use `loadHttpUrl` instead of ~~`loadHTTPURL`~~, unless required by a platform name (e.g. `XMLHttpRequest`).

-   No boolean variables with negative names.

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

    For API familiarity, you may violate this rule.

-   Module alias in `lowerCamelCase`

    ```js
    import * as awesomeModule from "./awesome-module.js"
    ```

    Some libraries might commonly use a module alias that violates this naming scheme, but overbearingly common open source use makes the violating style more readable.
    The only libraries that currently fall under this exception are:

    -   [jquery](https://jquery.com/), using `$`
    -   [lodash](https://lodash.com/), using `_`
    -   [threejs](https://threejs.org/), using `THREE`
    -   [underscore.js](https://underscorejs.org/), using `_`

-   functions in `lowerCamelCase`

    In some context, it can be practical to name a factory like the built type.
    In this exceptional case, you can use a function name in `UpperCamelCase`.

-   `CONSTANT_CASE`

    **Immutable**: `CONSTANT_CASE` indicates that a value is intended to not be changed, and may be used for values that can technically be modified (i.e. values that are not deeply frozen) to indicate to users that they must not be modified.

    ```js
    // Even though per the rules of JavaScript UNIT_SUFFIXES is
    // mutable, the uppercase shows users to not modify it.
    const UNIT_SUFFIXES = {
        milliseconds: "ms",
        seconds: "s",
    }
    ```

    **Global**: Only symbols declared on the module level, static property of a class, and values of enums, may use `CONSTANT_CASE`.
    If a value can be instantiated more than once over the lifetime of the program (e.g. a local variable declared within a function, or a static field on a class nested in a function) then it must use `lowerCamelCase`.

    **Exception**: If a value is a function, then it should use `lowerCamelCase`.

    Note that `enum` values may also be in `UpperCamelCase`.

-   Use single upper case character for type parameters

    Commonly used type parameters:

    | type parameter | meaning |
    | -------------- | ------- |
    | E              | Element |
    | K              | Key     |
    | T              | Type    |
    | V              | Value   |

-   No `_` prefix/suffix

    Exceptions:

    -   Unused identifiers may be prefixed by an underscore (this may be required by some linters)
    -   Test utility functions
    -   Code generators may use `_` suffix for ensuring that the identifier is not a _reserved word_

-   No `$` prefix

    Except when aligning with naming conventions of third party frameworks, or to name regex matches:

    ```js
    s.replace(/^(\w)\w/, ($0, $1) => $1.toUpperCase())
    ```

-   No redundancy

    TypeScript expresses information in types, so names should not be decorated with information that is included in the type.

    Some concrete examples of this rule:

    -   No trailing or leading underscores for private properties or methods.
    -   No `opt_` prefix for optional parameters.
    -   No special prefix for interfaces, unless it's idiomatic in its environment.
    -   Suffixing `Observables` with `$` is a common external convention and can help resolve confusion regarding observable values vs concrete values. Judgement on whether this is a useful convention is left up to individual teams, but should be consistent within projects.

-   React Components

    When required by _JSX_, components must be `UpperCamelCase` no matter how the component is implemented (e.g. `function`, `class`, `React.createClass`).

    ```diff
    - function goLink(props: GoLinkProps) { return <a href={...}>... }
    + function GoLink(props: GoLinkProps) { return <a href={...}>... }
    ```

## Make documentation that actually add information

Use [`/** TSDoc */` comments](https://tsdoc.org/) to communicate information to the users of your code.
You should also document all properties and methods whose purpose is not immediately obvious from their name, as judged by your reviewer.

-   `@param` and `@return` tags may be used to add information.
    Otherwise, they should be omitted.
    A summary is often enough:

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

-   Do not declare types in `@param` or `@return` blocks, do not write `@implements`, `@enum`, `@private`, `@override` etc. on code that uses the `implements`, `enum`, `private`, `override` etc.

-   Use `@throws`

    If your function may throw an exception, you should document it with the _TSDoc_ `@throws` tag.

    ```ts
    /**
     * @throws {@link ParserError}
     */
    function parse(source: string): Ast {}
    ```

-   No `@override`

    `@override` is not enforced by the compiler, which is surprising and leads to annotations and implementation going out of sync.
    Use the `override` keyword instead.

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
    You should not use headings `#`, thematic breaks `---`, and images `![]()` in docs.

-   `{@link symbol}` should only be used to reference an exported symbol of the project.

    Otherwise, use _Markdown_ inline code and _Markdown_ hyperlinks.

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

    Code examples should not contain additional comments and must not be indented.
    It is already inside a comment.
    If it needs further comments, then it is not a good example.

-   Use ASCII art if it improves your documentation

    Some editors such as [ASCII flow](https://asciiflow.com/) can help to create diagrams.
    You can even use standardized ASCII art like [svgbob](https://ivanceras.github.io/svgbob-editor/):

    ````md
    ```svgbob
    ┌──────┐     ┌──────┐
    │step 1├────►│step 2│
    └──────┘     └──────┘
    ```
    ````

    You can also generate [ASCII diagrams from other textual formats](https://metacpan.org/dist/Graph-Easy/view/bin/graph-easy).

## Make informative comments

Comments have a bad reputation.
According to their detractors they are redundant with good code.
While this is true for comments that restate what the code does, it is not true for comments that explain why the code is in the way it is.
One of the best ways to find out if a module has enough comments is to read it again after a few days and ask for reviews.

-   Explain high-level purpose

    It is often useful to add a comment that explains the purpose of a module.
    This can help readers to find what they search.
    You can also add keywords to ease searching.

-   Document implementation choice

    Every time you hesitate between several choices, tell about that and why you choose a solution over another one.
    This is really valuable information for your readers and future self.

-   Document issue fixes

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

-   Use `// Comment.` instead of `/* Comment. */`

    You can use `/* Comment. */` for adding information in an expression.
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
    Do not use `var`.

    Declare and initialize each variable on their own line:

    ```diff
    - let foo, bar = 5
    + let foo
    + let bar = 5
    ```

-   Use pure expressions

    Expressions are often considered as pure. i.e. without mutations and side-effects.
    Thus, do not use assignment in expressions:

    ```diff
    - const y = (x += 1)*2
    + x += 1
    + const y = x*2
    ```

    In the same vein, do not use the comma operator:

    ```diff
    - const y = x++, x*2
    + x++
    + const y = x*2
    ```

    Sometimes you have no choice that calling an impure function.
    You should explicitly indicate its impurity at the call site with `/*@__IMPURE__*/`:

    ```js
    const v = /*@__IMPURE__*/ next() + 5
    ```

-   Use `===` and `!==`

    The double equality operators cause [error prone type coercions](https://dorey.github.io/JavaScript-Equality-Table/) that are hard to understand and slower to implement for JavaScript engines.

    However, you may use `== null` and `!= null` for comparing against `null` and `undefined` values.

-   Use boolean expressions in conditions

    The type of all condition in conditionals must conform to `boolean|null|undefined`.
    This restriction prevents general pitfalls with implicit type coercion.
    The inclusion of nullish values allow using optional chaining for optional boolean properties:

    ```ts
    if (o?.isEnabled) { ... }
    ```

-   Avoid `default` switch clauses

    TypeScript is able to make exhaustive checks.
    `default` switch clauses break this check.

-   No labeled statements

    Labeled statements are vestiges of `goto`.
    They allow writing spaghetti code and make harder flow analysis.

## Primitive types

-   `null` vs `undefined`

    Use either `null` or `undefined` to denote absence of a value.
    Nullable types can be constructed as a union type `string|null` or `string|undefined`.
    Avoid unions with both `string|null|undefined`.

    Favor API familiarity.
    For instance, `Map.get` may return `undefined`.
    If you create a map-like structure, then use the same API as `Map`.

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

        The following code are equivalent:

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

    For non-ASCII characters, use the actual Unicode character (e.g. `∞`).
    For non-printable characters, the equivalent hex or Unicode escapes (e.g. `\u221e`) can be used along with an explanatory comment.

    ```js
    // Perfectly clear, even without a comment.
    const units = "μs"

    // Use escapes for non-printable characters.
    const output = "\ufeff" + content // byte order mark
    ```

-   No primitive wrapper types

    Do not instantiate the wrapper classes for the primitive types `String`, `Boolean`, and `Number`.
    Wrapper classes have surprising behavior, such as `new Boolean(false)` evaluating to `true`.

    ```diff
    - new String("hello")
    + "hello"
    ```

-   Type coercion

    You can use wrapper classes without `new` for explicit type conversion:

    ```js
    Boolean(null)
    Number("2e1")
    String(5)
    ```

    Using string concatenation to cast to string is discouraged, as we check that operands to the plus operator are of matching types.
    Use explicit conversion or string template:

    ```diff
    - "count: " + 5
    + `count: ${5}`
    + "count: " + String(5)
    ```

    Code must use `Number()` to parse numeric values, and must check its return for `NaN` values explicitly, unless failing to parse is impossible from context.
    Note that `Number("")`, `Number(" ")`, and `Number("\t")` would return `0` instead of `NaN`.
    `Number("Infinity")` and `Number("-Infinity")` would return `Infinity` and `-Infinity` respectively.
    Additionally, exponential notation such as `Number("1e+309")` and `Number("-1e+309")` can overflow into `Infinity`.
    These cases may require special handling.

    ```js
    const aNumber = Number('123')
    if (Number.isNaN(aNumber)) { ... }
    if (!isFinite(aNumber)) { ... }
    ```

    Code also must not use `parseInt` or `parseFloat` to parse numbers, except for non-base-10 strings.
    Both of those functions ignore trailing characters in the string, which can shadow error conditions (e.g. parsing `12 dwarves` as `12`).

    Code that requires parsing with a radix must check that its input contains only appropriate digits for that radix before calling into `parseInt`;

    ```js
    if (!/^[a-fA-F0-9]+$/.test(someString)) { ... }
    // Needed to parse hexadecimal
    parseInt(someString, 16) // Only allowed for radix != 10
    ```

-   No `isNaN` and `isFinite`, use `Number.isNaN` and `Number.isFinite`

    The global functions `isNaN` and `isFinite` are unsafe: they attempt a type coercion.

## Arrays and tuples

-   No `new Array(...)`

    `new Array()` has confusing and contradictory usage.
    Use literal arrays instead.

    ```diff
    - const a = new Array(2) // [undefined, undefined]
    - const b = new Array(2, 3) // [2, 3]
    + const c = [2, 3]
    ```

-   No sparse arrays

    Do not create sparse arrays — i.e. arrays with holes:

    ```diff
    - const sparseArray = [1, , 2]
    + const sparseArray = [1, null, 2]
    ```

    Note that this does not prevent you to use destructuring pattern with a hole:

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

-   Use readonly tuples

    Tuples are generally considered immutable in other languages.
    You should always make `readonly` tuples.

    If you are tempted to create a Pair type, instead use a tuple type.

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

-   Use top-level named functions.

    ```diff
    - const f = function() { ... }
    - const f = () => { ... }
    + function f() { ... }
    ```

    You may use an arrow function when you wish assigning to a typed constant.

    ```js
    const f: FunctionSignature = () => { ... }
    ```

-   Use arrow functions for anonymous functions.

    ```diff
    -  bar(function() { ... })
    +  bar(() => { ... })
    ```

-   Place default parameters last.

    This makes them optional.

    ```diff
    - function ident(n = 0, s: string): string { ... }
    + function ident(s: string, n = 0): string { ... }
    ```

-   Use default parameters over optional parameters.

    ```diff
    - function ident(s: string, n?: number): string { ... }
    + function ident(s: string, n = 0): string { ... }
    ```

-   No mutable value for default parameters

    The value of a default parameter is shared between all function calls.
    Thus, you should not mutate the value or return it.

    Do not mutate the value of a default parameter, it is shared between all calls.
    Use `readonly` types for protecting the value.

    ```diff
    + function f(arr: readonly string[] = []): string[] {
    - function f(arr: string[] = []): string[] {
    -     return arr
    +     return arr.slice()
      }
    ```

-   Group default and optional parameters into an option object.

    ```diff
    - function connect(hostname = "localhost", port = 8080): void { ... }
    + function connect({ hostname = "localhost", port = 8080 } = {}): void { ... }
    ```

    An optional parameter that's not in an options object might be acceptable if there is only one, and it seems inconceivable that we would add more optional parameters in the future.

-   No `arguments`

    This is a legacy feature.
    Use rest parameters instead.

    ```diff
    - function countArgs() {return arguments.length }
    + function countArgs(...rest) {return rest.length }
    ```

-   No property addition

    You should consider a function as frozen.

    ```diff
      function f() {}
    - f.id = (x) => x
    ```

-   No `.length`, `.name`

    `f.length` returns the number of parameters of a function `f`.
    It is not reliable since default and optional parameters are not counted.
    Moreover, some transpilers and bundlers can change the number of parameters.

    `f.name` returns the name of the function `f`.
    You should not rely on `f.name` since minifiers can change the name of a function.

-   Avoid overloaded functions

    ```diff
    - function f(a: number): void
    - function f(a: string): void
      function f(a: number | string): void { ... }
    ```

-   Avoid nested named functions

    Nested named function that are not closures - i.e. function that capture a variable of the super scope - should be extracted at the top-level of the module.

    ```diff
    + function g() { return 1 }
    funcfion f() {
    -     function g() { return 1 }
        return g();
    }
    ```

-   No `this`

    `this` should only be used inside a method of a class.
    It is error-prone to use `this` in functions.

-   Use early return

    Early returns should highligh exceptions, while the main return should highligh the happy path.
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

    `void` should only be used as return type, including in generics that appears in a return type.

    A function with `void` as return type should not have `return <value>` instruction.
    A `return` without a value is allowed as control flow instruction.

    In contrast, a function with `undefined` as return type must explicitly return an `undefined` value.

## Exceptions

-   Only throw instances of `Error` or derived classes.

    Literal values do not get a stack trace filled in, making debugging hard.

    ```diff
    - throw "message"
    + throw new Error("message")
    ```

    Consider writing your own error type:

    ```ts
    class CustomError extends Error {
        readonly data: Data
        constrcutor(data: Data) {
            super("message")
            this.name = "CustomError"
            this.data = Data
        }
    }
    ```

    Do not forget to set the `name` property to the name of your class.

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

-   Do not catch all kind of errors

    Some errors are not meant to be caught.
    They signal a misused API.
    For instance:

    -   `AssertionError`
    -   `InternalError`
    -   `RangeError`
    -   `ReferenceError`
    -   `SyntaxError`
    -   `TypeError`

    You should distinguish errors that signal an API misuse or an unrecoverable error from recoverable and expected errors.
    The former group should not be caught, then the second one should be caught and treated.

## Enums

-   No `const enum`

    `const enum` forces the TypeScript Compiler to inline the `enum`.
    `const enum` is not supported by bundlers and is incompatible with the `isolatedModules` TypeScript mode.
    Their use can lead to import non-existent values (because `const enums` are erased).
    Moreover, some bundlers, such as _ESbuild_, are able to inline `enum` anyway.

    ```diff
    - export const enum Gender {
    + export enum Gender {
        FEMALE = "FEMALE"
        FLUID = "FLUID"
        MALE = "MALE"
    }
    ```

-   Use `enum` initializers

    All `enum` values must be initialized either with integers, or strings that match the `enum` name.

    ```diff
    enum Gender {
    -   FEMALE,
    -   FLUID,
    -   MALE,
    +   FEMALE = 0,
    +   FLUID = 1,
    +   MALE = 2,
    // or
    +   FEMALE = "FEMALE",
    +   FLUID = "FLUID",
    +   MALE = "MALE",
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
    Fallback to object or constants for using negative integers:

    ```ts
    type Order = -1 | 0 | 1
    const Order = {
        LOWER: -1,
        EQUAL: 0,
        UPPER: 1,
    } as const
    // or
    const LOWER = -1
    const EQUAL = 0
    const UPPER = 1
    ```

-   Do not use computed access on enums.
    This implies you cannot lookup the name of a member from its value.

## Object literals

-   Assume prototype-less object literal

    Object literals can be created with a prototype set to null.
    Thus, you should assume that object literals don't inherit of the methods of `Object`.

    ```diff
      const x = {}
    - x.hasOwnProperty("a")
    + Object.hasOwnProperty.call(x, "a")
    + // or in ES2022:
    + Object.hasOwn(x, "a")
    ```

-   No methods, getters, setters on object literals

    If you really need methods, getters, or setters, then use a class and instantiate it instead.

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

-   Use object destructuring

    In some contexts, a destructuring object pattern can be easier to read than several property assigments.

    ```diff
    - const a = x.a
    - const b = x.b
    + const { a, b } = x
    ```

    This may also allow removing a variable name:

    ```diff
    + function f({ a, b }) {
    - function f(x) {
    -     const a = x.a
    -     const b = x.b
          ...
    }
    ```

-   `.dotted` vs `[computed]` property access

    You should not mix `.dotted` and `[computed]` property access.

    If you access an object literal as an object-map, then use quoted properties:

    ```ts
    const TYPES = {
        "boolean": 0,
        "number": 1,
        ...
    } as const
    const x = ...
    TYPES[typeof x]
    ```

    Its type must also use quoted properties:

    ```ts
    type TYPES = {
        "boolean": 0,
        "number": 1,
        ...
    }
    // or simply:
    type TYPES = typeof TYPES
    ```

-   Use fixed object shapes

    JavaScript engines optimize code that use the same object shapes.
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

    You should use optional properties only at the boundary of your library where API ergonomics is important.
    In this case you can rely on `Partial<T>` type:

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

    Deleting a property change the shape of the object.

-   Avoid object spread

    Copying an object with an object spread [doesn't preserve its shape for now](https://bugs.chromium.org/p/v8/issues/detail?id=9343#c13).
    For simple copy always explicitly copy all properties:

    ```diff
      const x = { a: 1, b: 2 }
    - const y = { ...x, b: 3 }
    + const y = { a: x.a, b: 3 }
    ```

    For more complex copy, you can resort to object spread.

    If the copy creates too much deoptimization and hurts the performance, then you can use a trick to preserve the object shape:

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

-   Use `new Foo()`

    Constructor calls must use parentheses, even without any argument:

    ```diff
    - const x = new Foo
    + const x = new Foo()
    ```

-   Use `implements <interfaces>`

    Although TypeScript is structurally typed, you should explicitly implement an `interface`.
    This ensures that your `class` properly implements the `interface`.

    A class must only explicitly implement interfaces.
    In the same vein, an interface must only extend other interfaces.

-   Composition over inheritance

    You should avoid inheritance/extension.
    When you extend a class, ensure that the class is designed to be inherited or is part of your project.
    For instance, an `abstract` class expects to be extended.

    If you write a class, consider to indicate that your class must not be extended thanks to the [_TSDoc_ `@sealed` tag](https://tsdoc.org/pages/tags/sealed/).

    ```ts
    /**
     * @sealed
     */
    class MYError extends Error { ... }
    ```

    If you extend a class, avoid overriding methods and properties.
    Ensure that an instance of your class respects the invariants of the sub-class.
    In other word you must respect the [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle/).

    Do not extend standard types such as `Boolean`, `BigInt`, `Number`, `String`, `Array`, `Map`, `Set`, `Symbol`, `Function` etc.

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
    -     static { ... }
    -     static CONSTANT = 5
        static createA(): A { ... }
    }
    }
    ```

-   No `public`, avoid `protected`, use `#private` instead of `private`

    Restricting visibility of properties, methods, and entire types helps with keeping code decoupled.

    Members are public by default.
    Thus, do not use `public`.

    Avoid `protected`: it is often used in an architecture that favors inheritance.
    You should favor composition over inheritance.

    If you target ES2022 or above, then use `#private` instead of `private`.
    Otherwise, use `private` to avoid substantial emit size and performance regressions.

    ```diff
    class AwesomeClass {
    -    private property: number
    +    #property: number
    }
    ```

    You should not mix both `#private` and `private`.
    You should also avoid using a public and private identifiers with the same name.
    This is confusing.
    This also simplify migrations of `private` to `#private`.

    ```diff
    class AwesomeClass {
    -    property: number
    -    #property: number
    +    public_property: number
    +    #private_property: number
    }
    ```

    Consider converting `private` methods to non-exported functions within the same file but outside any class.

    ```diff
    class A {
    -     #f() {}
    }
    + function f(a: A) {}
    ```

-   Use property initializers

    If a class member is initialized with a constant, initialize it where it's declared, which sometimes lets you drop the constructor entirely.

    ```diff
    class Foo {
    -    private readonly userList: string[]
    -    constructor() { this.userList = [] }
    +    private readonly userList: string[] = []
    }
    ```

-   No useless `constructor`

    It is unnecessary to provide an empty `constructor` or one that simply delegates into its parent class because ES2015 provides a default class constructor if one is not specified.

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

    You can use getters and setters if it improves the API familiarity or if a user could expect a property.
    For instance, a user may expect to access the length of a data structure like the length of an array:

    ```ts
    class LinkedList {
        get length(): number { ... }
    }
    ```

    Getters and setters should mimic a property.
    In particular:

    -   A getter must be a pure and deterministic function (i.e., result is consistent and has no side effects).
    -   The setter should modify the value returned by the getter with the value passed to the setter.

    Getters and setters are particularly useful for changing the API in a backward compatible way.
    For instance, computing a property instead of storing it:

    ```diff
    class List {
    -    isEmpty: boolean // Version 1
    +    get isEmpty(): boolean { ... } // Version 1.1
    }
    ```

-   Use `this` in methods and class constructors

    All non-static class methods should use `this`.
    Otherwise, the method should be extracted to a top-level named function.

-   Avoid `@Decorator`

    Do not define new decorators.
    Only use the decorators defined by frameworks.

    -   Angular: `@Component`, `@NgModule`, ...
    -   Polymer: `@property`, ...

-   Assume frozen prototypes

    The methods of a class must not be re-assigned.

## Source organization

-   Use relative import paths with extensions

    Code must use relative imports `./foo` rather than absolute imports `path/to/foo` when referring to files within the same (logical) project.
    Consider limiting the number of parent steps `../../../` as those can make module and path structures hard to understand.

    Use relative imports with extensions.
    The extension should end with `.js` or `.ts` (Deno).

-   No circular imports

    This prevents partial evaluation and some errors that are hard to catch.
    Modules dependencies should form a Directed Acyclic Graph.

-   Module imports vs destructuring imports

    Despite the `*`, a module import is not comparable to a "wildcard" import as seen in other languages.
    Instead, module imports give a name to the entire module and each symbol reference mentions the module, which can make code more readable and gives autocompletion on all symbols in a module.
    They also require less import churn (all symbols are available), fewer name collisions, and allow terser names in the module that's imported.
    Module imports are particularly useful when using many different symbols from large APIs.

    ```js
    import * as utils from "./utils.js"
    ```

    Destructuring imports give local names for each imported symbol.
    They allow terser code when using the imported symbol, which is particularly useful for very commonly used symbols.

    ```js
    import { test, expect } from "vitest"
    ```

-   Use `import type`

    All values that are only types (e.g. `type` and `interface`) mus tbe imported with `import type` or `import {type ... }`
    This makes possible to use modern bundlers such as _ESbuild_.

-   No `namespace`

    `namespace` and `module` are legacy constructs of TypeScript.
    Use EcmaScript Modules (_ESM_) instead.
    In constrast to TypeScript `namespace` and `module`, _ESM_ can be optimized by modern bundlers.

    ```diff
    - namespace AwesomeModule { ... }
    - module AwesomeModule { ... }
    ```

    Note that you can use [ambient modules](https://www.typescriptlang.org/docs/handbook/namespaces-and-modules.html) for typing third-party modules.

-   Treat modules, enums, and classes like namespaces

    When importing a module, directly access properties on the module object rather than passing it around.
    This ensures that modules can be analyzed and optimized.

    ```diff
    import * as utils from "./utils.js"
    - f(utils)
    + f({ a: utils.a, b: utils.b })
    ```

    Enums and classes should also be considered like namespaces.
    Don't pass an `enum` or a `class` to a function and don't assign them to variables.

-   Constant declarations

    You should declare constants at the start of a module without any imports.
    Some bundlers, such as _ESbuild_, take advantage of this to inline constants.

    ```diff
    - import ...
    export const MAX_U32 = 2**32 - 1
    ```

-   No `export default`

    This ensures import uniformity and avoids some historical incompatibilities between transpilers.

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

    This type of export is confusing because this makes a namespace looks as an exported object.
    Moreover, some bundlers such as _ESbuild_ are not able to optimize this kind of exports.

    You can freely use `export * from`, `export {X} from`, and `export {X as Y} from`.

-   Write side-effect-free modules

    All exports must be side-effect free.
    This is a requirement for dead code elimination.

    If a constant is built from a function call, the function must be preceded by the comment [`/*@__PURE__*/`](https://esbuild.github.io/api/#pure).
    This informs bundlers that the call is pure.

    ```diff
    - export const X = createX()
    + export const X = /*@__PURE__*/ createX()
    ```

-   Place imports at the top of the file after the [copyright notice](#Copyright-Headers).

-   Place non-exported functions and classes at the end of the file

    In this way the reader of your code can quickly scan exported functions.

## Type system

-   Type inference

    Code should rely on type inference as implemented by the TypeScript compiler for all type expressions (variables, properties of object literals, arrow functions, ...)
    This simplifies code refactoring.

    However, an `export` must be explicitly typed if its type cannot be syntactically inferred.
    This makes possible to type-check separately every file.
    An `export` is syntactically inferred if it is initialized with a literal value that is recursively composed only of literal values.

    ```diff
    // Type of `f()` cannot be syntactically inferred.
    - export const X = { a: f() }
    + export const X: { readonly a: number } = { a: /* @__PURE__ */ f() }

    export const Y = { b: 5 } as const
    ```

    All named functions, including those that are not exported, must be explicitly typed.
    This improves code documentation.

    ```diff
    - function doNothings(s) { ... }
    + function doNothings(s: string): void { ... }
    ```

    In the same vein, class members must be explicitly typed.

    ```diff
    class Person {
    -   name
    +   name: string

    -   method() { ... }
    +   method(): void { ... }
    }
    ```

-   Nullable/undefined type aliases

    Type aliases should not include `|null` or `|undefined` in a union type.
    This typically indicates that null values are being passed around through too many layers of an application, and this clouds the source of the original issue that resulted in null.
    They also make it unclear when specific values on a type might be absent.

    Instead, code should only add `|null` or `|undefined` when the alias is actually used.
    Code should deal with null values close to where they arise, using the above techniques.

    ```diff
    - type Coffee = Latte | Americano | null
    + type Coffee = Latte | Americano

    - function drink(): Coffee { ... }
    + function drink(): Coffee | null { ... }
    ```

-   Optional `?` vs `|undefined` type

    Optional mark `?` indicates that a property or a parameter can be omitted, while `|undefined` indicates that `undefined` is a valid value for the property or parameter.
    TypeScript implicitly adds `|undefined` to optional properties.
    You should not rely on this implicit behavior and always add `|undefined` if it is a valid value for the property.

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

    You should treat `interface` and object `type` alias [like Flow](https://flow.org/en/docs/types/interfaces):

    -   Object `type` for object literals.
    -   `interface` for object literals and `class` instances.

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
    Note that this allows also to use class instances (assuming nominal typing for classes).

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

    TypeScript checks exactness when you assign an object literal to an annotated variable or a function argument.

    ```ts
    type Person = {
        name: string
    }
    const p0: Person = { name: "Eva", age: 25 } // error
    const p1 = { name: "Eva", age: 25 }
    const p2: Person = p1 // no error
    ```

-   Use `readonly`

    Mark properties that are never reassigned with the `readonly` modifier (these need not be deeply immutable).

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

    Avoid types and function with zillions of type parameters.
    This makes harder to use and understand your API.

    Think twice before adding a type parameter.

-   Avoid mapped and conditional Types

    [Mapped types](https://www.typescriptlang.org/docs/handbook/2/mapped-types.html) and [conditional types](https://www.typescriptlang.org/docs/handbook/2/conditional-types.html) are advanced features.
    They come wit hedge case and make the code harder to understand.

    You should avoid their use and rely on the simplest type construct that can possibly express your code.
    A bit of repetition or verbosity is often cheaper than the long term cost of complex type expressions.
    Use the following [utility types](https://www.typescriptlang.org/docs/handbook/utility-types.html#instancetypetype) before resorting to mapped and conditional types: `Partial`, `Readonly`, and `Record`.

    Avoid the following utility types: `Pick`, `Omit`, `Exclude`, `Extract`, `NonNullable`, and `required`.
    They can generally be replaced with plain types.

-   No `any`, use `unknown`

    `any` is dangerous: it allows assignment into any other type and dereferencing any property off it.
    Use `unknown` instead or in last resort a type assertion.

-   No `@ts-ignore`, `@ts-expect-error`, `@ts-nocheck`

-   No non-nullability assertions

    Non-nullability assertions (`x!`) are unsafe and unfamiliar.
    Use optional chaining `?` combined and nullish coalescing `??`, or in last resort type assertions.

    ```diff
    - prop!.doSomething()
    + prop?.doSomething()
    ```

-   Use type assertions `as`

    Avoid type assertions.
    However, it is sometimes necessary to avoid complex types that use mapped and conditional types.
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

    This is an advanced feature that makes code harder to read and understand.
    However, it can avoid severe issue in some case.
    Use with caution.

    TypeScript does not natively support nominal type aliases.
    _Flow_ supports nominal type aliases through [sub-typing of opaque types](https://flow.org/en/docs/types/opaque-types/#toc-subtyping-constraints).
    You can emulate them in TypeScript using a [brand](https://basarat.gitbook.io/typescript/main-1/nominaltyping#using-interfaces).
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

    We use the _JSDoc_ tag `@phantom` to signal that the constant does not exist at runtime and thus must only be used in type contexts.

    We chose the type `never` for our fake property.
    Nothing except a `never` value can be assigned to `never`.
    This signals that the property should not be read.

    Nominal types allow to check invariants.
    In the previous example, we check that a person has a non-empty name.
    In this case, you should only use brand for immutable types to avoid data invalidation.
