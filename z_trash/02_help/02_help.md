# Documentation Features

Page last revised on: {{ git_revision_date }}

```
Requirements:

mkdocs
mkdocs-material
plantuml-markdown
pymdown-extensions
pygments
mkdocs-pdf-export-plugin
fontawesome_markdown
mkdocs-git-revision-date-plugin

```

??? multiple optional-class "Summary"
    Here's some content.
    [=25%]{: .thin}

!!! note
    Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla et euismod
    nulla. Curabitur feugiat, tortor non consequat finibus, justo purus auctor
    massa, nec semper lorem quam in massa.


:smile: :heart: :thumbsup:

## Mermaid Charts

```mermaid
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
```
 

## Sequence Diagrams
```mermaid
sequenceDiagram
Title: Here is a title
A->B: Normal line
B-->C: Dashed line
C->>D: Open arrow
D-->>A: Dashed open arrow
```

## Flowchart
``` mermaid
graph LR
  A[Start] --> B{Error?};
  B -->|Yes| C[Hmm...];
  C --> D[Debug];
  D --> B;
  B ---->|No| E[Yay!];
```
