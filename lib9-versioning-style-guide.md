# Lib9 Versioning Style Guide

## Use Semantic Versioning

[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## Keep a Changelog

[Keep a Changelog](https://keepachangelog.com/en/1.0.0/) at the root of your project in `CHANGELOG.md`.

-   No changelog generation

    This style guide encourages the use of an enforceable format for the commit messages.
    This is often used to generate the changelog.
    This leads to changelog that are too closely related to the code.

    A changelog entry and a commit message serve different purpose:

    -   A changelog entry explains a change from a user point of view.
    -   A commit message documents a code change from a developer point of view.

-   Document any change visible to your users

    A changelog entry should be commited with the change that it describes in a `Unreleased` section.
    This helps to write good commit messages and to take some time to think about the change you did.

    A changelog entry should add context and motivation.
    A good example is the [esbuild](https://github.com/evanw/esbuild) changelog.

-   Changelog improvements

    A changelog should not be considered as append-only.
    Treat it as a source of documentation.

-   Follow a simple format

    Every version should have a dedicated section.
    The `Unreleased` section document the changes that are not released yet.
    This section comes first.
    Other sections are in reverse chronological order.

    ```md
    # Changelog

    This project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

    ## Unreleased

    -   Change D

        ...

    -   Change C

        ...

    ## 0.2.0 (2023-02-04)

    -   Change B

        ...

    ## 0.1.0 (2023-02-04)

    -   Change A

        ...
    ```

-   Use _Markdown_

    See the [Markdown style guide](./lib9-versioning-style-guide.md).

## Commit message format

This style guide use a format that conforms to [Conventional Commit](https://www.conventionalcommits.org/en/v1.0.0/).
It adds restrictions and is simpler.

Every commit message follows the following format:

```txt
<type>(<optional-scope>): <subject>

<optional-body>

<optional-trailers>
```

-   Allowed types

    -   `feat`, new feature
    -   `fix`, bugfix
    -   `docs`, documentation update
    -   `test`, test update
    -   `chore`, project housekeeping (dependency, build updates)
    -   `ci`, CI-related changes
    -   `perf`, performance improvement
    -   `refactor`, code refactoring without change in functionality

    When working on a change, you may temporarily use `WIP` as type.

-   Scope

    Scopes are optional.
    They may specify the place of the change.

-   Subject

    -   use imperative, present tense: "add" not "added" nor "adds"
    -   don't capitalize the first letter
    -   no dot at the end
    -   use concise subject

-   Body

    -   explain the introduced changes, design choices
    -   if the introduced change is a breaking change, then the body
        should include the string `BREAKING CHANGES`.
        It should propose a way for updating codes that will break.

-   `BREAKING CHANGES:`

    `BREAKING CHANGES:` signals a change that should bump the major version.
    It should be at the start of the body.
    It can be followed by a short description.
    More details can be provided after a blank line.

    ```txt
    <type>(<optional-scope>): <subject>

    BREAKING CHANGES: <optional-short-description>

    <optional-details>
    ```

    Example:

    ````txt
    refactor: rename `f` to `g`

    BREAKING CHANGES: use `g` instead of `f`

    `g` is more consistent with...

    ```diff
    - f()
    + g()
    ```
    ````

-   Trailers

    A trailer must be written on a single line, and start on the first column.
    The trailers must not be separated by blank lines.

    Common trailers:

    -   `Acked-By`
    -   `Approved-By`
    -   `Closes`
    -   `Co-Authored-By`
    -   `Co-Developed-By`
    -   `Fixes`
    -   `Helped-By`
    -   `Implements`
    -   `References`
    -   `Related-To`
    -   `Reported-By`
    -   `Reviewed-By`
    -   `See-Also`
    -   `Signed-Off-By`
    -   `Suggested-By`
    -   `Tested-By`
    -   `Thanks-To`

    Some examples:

    ```txt
    fix: <subject>

    <optional body>

    Fixes: #11
    ```

    ```txt
    feat: <subject>

    <optional body>

    Closes: #42
    ```

-   Use _Markdown_

    See the [Markdown style guide](./lib9-versioning-style-guide.md).
    You should not use headings `#`, thematic breaks `---`, and images `![]()` in git messages.

## Commit as a change unit

Every commit should pass the test suite.
You can test several commits at once thanks to `git rebase`.
The following commands test the two last commits:

```sh
git rebase --exec 'npm test' HEAD~2
```

## Use linear history

-   Use fast-forward merge

-   Branches should be rebased before merging

## Use consistent branch names

-   Use `lower-case-with-dashes`

-   Use prefixes to categorize branches

    You can use the Commit message types as branch prefix.
    For example, `feat-b`, `fix-a`.
