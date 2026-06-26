# Thoplaan Family Tree

A genealogy of the descendants of **Packeer Thamby Sinna Lebbe Marikar**, modelled as small, couple-centric YAML "cards" rather than one large document.

Each card describes a single **marriage (a couple)** and lists that couple's children. Generations link together by name: a person listed as an offspring on one card becomes the head of their own card further down. This makes the data easy to edit, diff, and reason about — and it cleanly handles people who married more than once (a single person simply heads more than one card).

## Why this model

A traditional `parent → child` tree breaks down the moment an ancestor has **two spouses**: you end up with "two roots" and no clean place to attach each line. Modelling every **couple** as its own card avoids that: the same person can appear as the head of two different cards (one per marriage), and each marriage carries its own set of children.

This happens twice in this family, and the structure handles both:

- **Packeer Thamby Sinna Lebbe Marikar** had two wives — see `forest/tree1.yaml` and `forest/tree2.yaml`.
- His grandson **Muhammad Shariff Hajiar (Pul-la-Kutty)** also had two wives — see `tree02/root/root.yaml` and `tree02/root/root2.yaml`.

## Concepts: forest → tree → root → branches → leaves

| Term | Meaning |
| --- | --- |
| **Forest** | The whole extended family, rooted at the great ancestor Packeer Thamby Sinna Lebbe Marikar. Because he had two wives, his family splits into two **trees**. |
| **Tree** | One descendant line. `forest/tree1.yaml` and `forest/tree2.yaml` are the top-level cards for each line. |
| **Root** | The founding couple of a detailed tree (e.g. Muhammad Shariff Hajiar + spouse in `tree02/root/`). |
| **Branches** | The root couple's children, each modelled as their own couple card. |
| **Leaves** | Every couple from the third generation downward. |

`tree02/` is the fully expanded line descending from `forest/tree2.yaml` (the Safiya Umma line, which produced Muhammad Shariff Hajiar). The first line (`forest/tree1.yaml`) is recorded at the top level but not yet expanded into its own detailed tree.

## Repository layout

```
tp_family_tree/
├── README.md
├── forest/
│   ├── tree1.yaml          # Packeer Thamby + Ameena Umma  (1st marriage)
│   └── tree2.yaml          # Packeer Thamby + Safiya Umma   (2nd marriage)
└── tree02/                 # detailed tree for Muhammad Shariff Hajiar's line
    ├── tree01.md           # human-readable rendered outline of the whole tree
    ├── root/
    │   ├── root.yaml       # founder + 1st wife (Ummu Zabila),  20 children
    │   └── root2.yaml      # founder + 2nd wife (Noor Mazaya),  12 children
    ├── branches/           # one card per founder's child  (card01 … card32)
    └── leaves/             # one card per couple, 3rd generation and deeper
```

## Card schema

Every card is plain YAML with a `Spouse` and an `Offsprings` list. The difference between the card types is only **how the head of the couple is named** and **how the card links upward**.

**Root card** (`root/`) — the head is the founder:

```yaml
Ancestor: Muhammad Shariff Hajiar (Pul-la-Kutty)   # head of the couple
Spouse: Ummu Zabila
Offsprings:
  Muhammad Fawzy Shariff
  Noor Suada (Noor Hafeela) Shariff
  # ...
```

**Branch card** (`branches/`) — one per founder's child; the head is that child:

```yaml
Ancestor: Noor Suada (Noor Hafeela) Shariff        # head of the couple
Spouse: Farook Saleem
Offsprings:
  Nadeer Saleem
  # ...
```

**Leaf card** (`leaves/`) — third generation and deeper; carries an extra `Parent` field:

```yaml
Ancestor: Noor Suada (Noor Hafeela) Shariff        # the head's parent (links upward)
Parent: Nadeer Saleem                              # head of this couple
Spouse: Sithy Abbasiya Mowjood
Offsprings:
  Azard Saleem
  # ...
```

### How linking works

- A person named in one card's `Offsprings` is the same person who appears as the `Ancestor`/`Parent` of their own card. Linking is purely by **matching the name**, so names should be written consistently.
- In `root/` and `branches/` cards the head of the couple is the `Ancestor`. In `leaves/` cards the head is the `Parent`, and `Ancestor` points one generation up (the connector). This is what lets every leaf attach to the right place regardless of depth.
- An empty `Offsprings:` (or empty `Spouse:`) is intentional — it records a marriage or person with no further recorded descendants.

## The rendered outline

`tree02/tree01.md` is a generated, human-readable view of the entire tree as a numbered outline:

- `1.x …` — descendants through the founder's **first** marriage (Ummu Zabila).
- `2.x …` — descendants through the founder's **second** marriage (Noor Mazaya).

Numbers deepen with each generation (e.g. `1.10.1.1.1` is a great-great-grandchild). It is the easiest way to read the tree top to bottom.

## Conventions

- **Multiple marriages** are split into separate cards. Where a single source card was divided, the files use an `a` / `b` suffix on the same number (e.g. `branches/card07a.yaml` + `card07b.yaml` for Muhammad Nazim Shariff's two wives; `leaves/card182a.yaml` + `card182b.yaml` for Afra's). In the outline they appear as sibling entries, with the later marriage marked `(2nd spouse)`.
- **Cross-links** (a marriage between two people who are both in the tree) are recorded on **one** card only, to avoid duplicate couple records; the other side is shown in the outline for completeness.
- **Unknown data** is preserved verbatim as `Name Not Known` where the source had no name; entries with no usable information are omitted rather than invented.
- Incidental annotations in the source (birth years, places, etc.) are dropped from card names to keep name-matching reliable.

## Editing

1. To add a couple, create a new card in `branches/` (for a founder's child) or `leaves/` (deeper), using the schema above.
2. Make sure the head's name exactly matches how they are listed in their parent's `Offsprings`.
3. Add the person to their parent card's `Offsprings` if they are not already there.
4. Reflect the change in the `tree02/tree01.md` outline.
