# Lib9 Markdown Style Guide

## Use basic _Markdown_

Use basic _Markdown_ compatible with both [commonmark](https://commonmark.org/) and [djot](https://www.djot.net/).
You can also use tables and footnotes that are widely supported.
You should stick to the following syntax:

-   Headings `#`, `##`, `###`

    Add a blank line before and after a heading.

-   \__emphasis_\_

-   \*\***strong emphasis**\*\*

-   \``inline code`\`

-   `> Blockquote`

-   `[link](https://example.com)`, `<https://example.com>`, and `[labelled link][label]`

    ```md
    A [labelled link][label].

    [label]: https://example.com
    ```

-   `[^footnote]`

    ```md
    Here's the reference[^foo].

    [^foo]: This is a footnote.
    ```

-   Images `![alt text](img.jpg)` and `![alt text][label]`

    ```md
    ![alt text][label].

    [label]: img.jpg
    ```

-   Lists, enumerations, and task lists

    Note that in _djot_, a list must be preceded by a blank line.
    This also applies to sub-lists.

    ```md
    -   item 1
    -   item 2
    ```

    ```md
    1.  item 1
    2.  item 2
    ```

    ```md
    -   [x] task 1 (done)
    -   [ ] task 2 (todo)
    -   [~] task 3 (removed)
    ```

-   Code block

    Specify a language tag.

    ````md
    ```js
    double(5) === 10
    ```
    ````

-   Tables

    The presence of `:` in the separator determines the column alignment.
    If no colon is present, then the column uses the default alignment.

    ```md
    | Item    | Price | # In stock |
    | :------ | :---: | ---------: |
    | Apples  | 1.99  |        739 |
    | Bananas | 1.89  |          6 |
    ```

## Formatting

-   Write one sentence a line

    This makes `git-diff` easier.

-   No hard wrap

    Write short sentences instead of dividing a sentence into several lines.

-   No trailing spaces

    Some _Markdown_ flavors can interpret them as a hard break.
    If you want a line break, then insert a blank line.

-   At most three levels of headings

    Two levels are usually sufficient.

-   Use inline links over labelled links

    ```diff
    - A [labelled link][label]
    + An [inline link](https://example.com)

    - [label]: https://example.com
    ```

    Only use labelled links for repeated links.

    ```diff
    - A [link](https://example.com).
    - Again the same [link]((https://example.com))
    - Always the same [link](https://example.com)
    + A [link][label]
    + Again the same [link][label]
    + Always the same [link][label]

    + [label]: https://example.com
    ```

-   Use ASCII diagrams

    A markdown file should be readable in its original form.
    A simple ASCII art diagram can often replace an image.

    Some editors such as [ASCII flow](https://asciiflow.com/) can help to create diagrams.
    You can even use standardized ASCII art like [svgbob](https://ivanceras.github.io/svgbob-editor/):

    ````md
    ```svgbob
    ┌──────┐     ┌──────┐
    │step 1├────►│step 2│
    └──────┘     └──────┘
    ```
    ````

    You can generate [ASCII diagrams from other textual formats](https://metacpan.org/dist/Graph-Easy/view/bin/graph-easy).

-   Use [Mermaid diagrams](https://mermaid.js.org/)

    A lot of services, such as GitHub and GitLab, render Mermaid diagrams.

    ````md
    ```mermaid
    graph TD;
        A-->B;
        A-->C;
        B-->D;
        C-->D;
    ```
    ````

-   Use LaTeX math

    ````md
    ```math
    a^2 + b^2 = c^2
    ```
    ````

-   Use diff blocks

    ````md
    ```diff
      unchanged
    - removed
    + added
    ```
    ````
