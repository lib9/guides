# Lib9 Versioning Style Guide

## Use Semantic Versioning

[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

A normal version has the `<major>.<minor>.<patch>` form.
Bump `<major>` if the release contains breaking changes.
Otherwise, bump `<minor>` if the release contains new features.
Otherwise, bump `<patch>`.

If `major>` is 0, then the project is considered in early development stage.
A release that bumps `<minor>` can thus contain breaking changes.

## Keep a Changelog

[Keep a Changelog](https://keepachangelog.com/en/1.0.0/) at the root of your project in `CHANGELOG.md`.

-   No changelog generation

    This style guide encourages the use of an enforceable format for the commit messages.
    It is often used to generate the changelog.
    This leads to changelog that are too closely related to the code.

    A changelog entry and a commit message serve different purpose:

    -   A changelog entry explains a change from a user point of view.
    -   A commit message documents a code change from a developer point of view.

-   Document any change visible to your users

    A changelog entry should be committed with the change that it describes in a `Unreleased` section.
    This helps you to write good commit messages and to take some time to think about the change you made.

    A changelog entry should provide context and motivation.
    A good example is the [esbuild](https://github.com/evanw/esbuild) changelog.

    It is a good place to provide code example to illustrate the change.

-   Update the changelog

    A changelog should not be considered as append-only.
    Treat it as a source of documentation.
    Edit previous entries if you find a better way to present a change.

-   Follow a simple format

    Every release should have its own section.
    The `Unreleased` section documents the changes that have not yet been released.
    This section comes first.
    Other sections follow in reverse chronological order.

    Sort changes by type (breaking change, new feature, bug fix, other change) and importance.
    Make breaking changes easily identifiable by introducing them with `BREAKING CHANGES:`.

    ```md
    # Changelog

    All notable changes to this project will be documented in this file.

    This project adheres to [Semantic Versioning][semver].
    The format of this changelog uses [a variant][lib9-versionning] of [Keep a Changelog][keep-changelog].
    New entries must be placed in a section entitled `Unreleased`.

    ## Unreleased

    -   BREAKING CHANGES: Change B

        ...

    -   Fix bug C

        ...

    ## 0.1.0 (2023-02-04)

    -   Add feature A

        ...

    [keep-changelog]: https://keepachangelog.com/en/1.0.0/
    [lib9-versionning]: https://github.com/lib9/guides/blob/main/lib9-versioning-style-guide.md#keep-a-changelog
    [semver]: https://semver.org/spec/v2.0.0.html
    ```

-   Use _Markdown_

    See the [Markdown style guide](./lib9-versioning-style-guide.md).

## Commit message format

This style guide uses a stricter format than [Conventional Commit](https://www.conventionalcommits.org/en/v1.0.0/).

Every commit message follows the following format:

```txt
<type>(<optional-scope>): <subject>

<optional-body>

<optional-trailers>
```

-   Allowed types

    -   `feat` (new feature)
    -   `fix` (bugfix)
    -   `docs` (documentation update)
    -   `test` (test update)
    -   `chore` (dependency, config, and build updates)
    -   `ci` (CI-related changes)
    -   `perf` (performance improvement)
    -   `refactor` (code refactoring without change in functionality)

    When working on a change, you may temporarily use `WIP` as type.

-   Scope

    Scopes are optional.
    They may specify the place of the change.

-   Subject

    -   Use imperative, present tense: "add" not "added" nor "adds"
    -   Don't capitalize the first letter
    -   No dot at the end
    -   Use concise subject

-   Body

    -   Explain the introduced changes, design choices

    -   If the introduced change is a breaking change,
        then the body should include the string `BREAKING CHANGES`.
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

    A trailer must be written on a single line, starting in the first column.
    Trailers must not be separated by blank lines.

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
You can test multiple commits at once thanks to `git rebase`.
The following command will test the last two commits:

```sh
git rebase --exec 'npm test' HEAD~2
```

## Use linear history

Distributed Version Control Systems, such as Git, allow creating multiple branches.
This can result in complex directed acyclic graph.
This is a cognitive burden when exploring the history.

Keep a linear history.
This is closely related to [OneFlow](https://www.endoflineblog.com/oneflow-a-git-branching-model-and-workflow).
Use fast-forward merge and rebase before merging.

-   Use annotated tags `v<version>` for releases

-   The part of the history covered by any release tag is frozen.

    This part must not be edited, except under extreme situations (e.g. to remove a leaked secret).

    The remaining part can be freely edited for improving readability and consistency.
    Use `git rebase -i` to re-order, reword, and squash commits.

    This means that you can easily introduce a hotfix by moving your hotfix commit just after the commit tagged by the last release.
    The new commit must be tagged to make a hotfix release.
    This allows to keep a linear history.
    However, if you want to incorporate the hotfix into an old release as well,
    you have no choice but to create a hotfix branch starting from the old release.
    You gave up the linear history for a tree-like history.
    This is still better than a direct acyclic graph.

    For example, in the following version diagram, the sixth commit is a hotfix that was placed just after the last tagged commit (v2.0.0).
    It is tagged with a new hotfix version (v2.0.1).
    The hotfix is also incorporated into the first release (v1.0.0) by creating a new hotfix branch.
    The commit of the branch is tagged with a new version (v1.0.1).

    ```mermaid
    gitGraph
    commit id: "1"
    commit id: "2" tag: "v1.0.0"
        branch hotfix
        commit id: "7 (hotfix)" tag: "v1.0.1"
    checkout main
    commit id: "3"
    commit id: "4" tag: "v2.0.0"
    commit id: "6 (hotfix)" tag: "v2.0.1"
    commit id: "5"
    ```