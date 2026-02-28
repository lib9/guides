# Lib9 Markdown Style Guide

## Use basic _Markdown_

Use basic _Markdown_ that is compatible with both [CommonMark](https://commonmark.org/) and [Djot](https://www.Djot.net/).
You can also use tables and footnotes which are widely supported.
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

    Note that in _Djot_, a list must be preceded by a blank line.
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

    Try to write short sentences.
    Break large sentences at natural places, such as after commas.
    See [Semantic Linefeeds](https://rhodesmill.org/brandon/2012/one-sentence-per-line/).

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

-   Compact and wide lists, enumerations, and task lists

    If a list item consists of several lines, then add a blank line between every list item.

    ```md
    - Item 1

      Second paragraph of the first item

    - Item 2

    - Item 3
    ```

    Otherwise, use the compact form.

    ```md
    - Item 1
    - Item 2
    - Item 3
    ```

    This also applies to enumerations and task lists.

-   Use ASCII diagrams

    A markdown file should be readable in its original form.
    A simple ASCII art diagram can often replace an image.

    Some editors such as [_ASCII flow_](https://asciiflow.com/) can help to create diagrams.
    You can even use standardized ASCII art like [_Svgbob_](https://ivanceras.github.io/svgbob-editor/):

    ````md
    ```svgbob
    ┌──────┐     ┌──────┐
    │step 1├────►│step 2│
    └──────┘     └──────┘
    ```
    ````

    You can generate [ASCII diagrams from other textual formats](https://metacpan.org/dist/Graph-Easy/view/bin/graph-easy).

-   Use [_Mermaid_ diagrams](https://mermaid.js.org/)

    A lot of services, such as _GitHub_ and _GitLab_, render _Mermaid_ diagrams.

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
