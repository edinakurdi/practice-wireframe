# CSS Grid — Complete Reference Summary

---

## 1. `display: grid` + defining columns & rows

Set on the **parent** (grid container). Children become grid items automatically.

```css
.container {
  display: grid;
  grid-template-columns: 200px 1fr 1fr; /* 3 cols: fixed, flexible, flexible */
  grid-template-rows: 80px 300px 60px;  /* 3 rows with explicit heights */
}
```

> [!NOTE]
> `grid-template-columns` controls width. `grid-template-rows` controls height.  
> You define the **parent**, and children sit inside it.

---

## 2. The `fr` unit — fractional space

`fr` = a fraction of the **remaining available space** after fixed sizes are taken.

```css
grid-template-columns: 200px 1fr 1fr;
/* Col 1: 200px fixed. Remaining space split equally between col 2 and col 3. */

grid-template-columns: 1fr 3fr;
/* Col 1 gets 1/4 of total space. Col 2 gets 3/4. */
```

| Value | Meaning |
|---|---|
| `200px` | Always exactly 200px |
| `1fr` | 1 equal share of remaining space |
| `3fr` | 3x bigger share than `1fr` |

---

## 3. `grid-template-areas` + `grid-area`

Draw a visual map of your layout using named zones. Set on the **parent**.

```css
/* Parent — draws the blueprint */
.layout {
  display: grid;
  grid-template-columns: 1fr 3fr;
  grid-template-rows: 80px 300px 60px;
  grid-template-areas:
    "header  header"
    "sidebar main"
    "footer  footer";
}

/* Children — each claims a named zone */
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }
.footer  { grid-area: footer; }
```

> [!IMPORTANT]
> The name in `grid-area: header` must **exactly match** the name in `grid-template-areas`.  
> Order of rules in CSS doesn't matter — the browser reads them all.

---

## 4. `grid-column` & `grid-row` with line numbers

Grid lines are numbered starting at **1**. A 3-column grid has lines 1, 2, 3, 4.

```
Line numbers:  1    2    3    4
               |    |    |    |
Row 1:        [  A  ][  B  ][  C  ]
Row 2:        [  D  ][  E  ][  F  ]
```

```css
/* Start at line 1, end at line 3 → spans 2 columns */
.item { grid-column: 1 / 3; }

/* Span rows 1 to 3 → 2 rows tall */
.item { grid-row: 1 / 3; }

/* -1 always means the last line → full width regardless of column count */
.item { grid-column: 1 / -1; }
```

> [!TIP]
> Use line numbers when you need **exact placement**.  
> Use `span` (lesson 7) when you just want an item to be N cells wide.

---

## 5. `gap`, `row-gap`, `column-gap`

Adds space **between** cells — not outside the grid.

```css
.grid {
  gap: 16px;          /* same between all rows and columns */
  gap: 24px 8px;      /* row-gap  column-gap (vertical then horizontal) */
  row-gap: 24px;      /* only vertical space between rows */
  column-gap: 8px;    /* only horizontal space between columns */
}
```

| | `gap` | `margin` |
|---|---|---|
| Space between cells | ✅ | ✅ |
| Space on outer edges | ❌ | ✅ |
| Set on | Parent | Each child |

> [!TIP]
> `gap` is cleaner than adding `margin` to every child. Use it by default.

---

## 6. `align-items` & `justify-items`

Controls how content sits **inside** each grid cell. Set on the **parent**.

```
justify → horizontal axis  ←————→
align   → vertical axis    ↑
                            ↓
```

```css
.grid {
  align-items: stretch;   /* default — fills full cell height */
  align-items: start;     /* hugs top */
  align-items: end;       /* hugs bottom */
  align-items: center;    /* vertical middle */

  justify-items: stretch; /* default — fills full cell width */
  justify-items: start;   /* hugs left */
  justify-items: end;     /* hugs right */
  justify-items: center;  /* horizontal middle */

  /* Shorthand: align then justify */
  place-items: center;          /* both centered */
  place-items: start center;    /* align: start, justify: center */
}
```

### Override a single child with `align-self` / `justify-self`:

```css
.special-item {
  align-self: end;
  justify-self: start;
}
```

### When to use each:

| Use | Default |
|---|---|
| Page layout, cards, sections | `stretch` (leave it as default) |
| Centering an icon/button in a large cell | `center` |

---

## 7. The `span` keyword

Instead of calculating exact line numbers, just say how many cells to span.

```css
/* These are equivalent if the item starts at column 1 */
.item { grid-column: 1 / 4; }    /* line numbers */
.item { grid-column: span 3; }   /* span — same result, less math */

/* You can combine: start at a specific line, then span */
.item { grid-column: 2 / span 2; } /* start at line 2, span 2 columns */
.item { grid-row: 1 / span 2; }    /* start at row 1, span 2 rows tall */
```

| Use | When |
|---|---|
| `span N` | You want an item to be N cells wide/tall |
| `1 / 3` | You need exact placement at a specific position |
| `2 / span 2` | Specific start + flexible width |
| `1 / -1` | Always full width, no matter how many columns |

> [!NOTE]
> Items without any `grid-column`/`grid-row` are placed automatically by the browser
> left-to-right, top-to-bottom. If rows run out, new implicit rows are created.

---

## 8. `repeat()`, `minmax()`, `auto-fit` / `auto-fill`

### `repeat()` — shorthand for repeated tracks

```css
/* These are identical: */
grid-template-columns: 1fr 1fr 1fr;
grid-template-columns: repeat(3, 1fr);     /* "3 columns, each 1fr" */
grid-template-columns: repeat(4, 200px);   /* "4 columns, each 200px" */
```

### `minmax(min, max)` — a track with a floor and a ceiling

```css
minmax(200px, 1fr)   /* at least 200px, grow up to equal share of space */
minmax(100px, auto)  /* at least 100px tall, grow if content is taller */
```

### `auto-fit` vs `auto-fill`

Both: *"fit as many columns as possible."*  
Difference: what happens when items don't fill all columns.

```css
/* auto-fill: keeps empty column slots (reserved space) */
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

/* auto-fit: collapses empty slots, items stretch to fill */
grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
```

> [!TIP]
> Use `auto-fit` 90% of the time. `auto-fill` is for niche cases where you want
> reserved column slots even when empty.

### The responsive card grid — one line, no media queries:

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}
```

How it reads: *"Fit as many columns as possible, each at least 200px wide, growing equally to fill all available space."*

| Container width | Result |
|---|---|
| 900px | 4 columns × ~225px each |
| 650px | 3 columns × ~216px each |
| 420px | 2 columns × ~210px each |
| 210px | 1 column × 210px |

---

## Quick Reference Cheat Sheet

```css
.grid {
  /* Define the grid */
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: 100px auto;
  grid-template-areas:
    "header header header"
    "sidebar main main";

  /* Spacing */
  gap: 16px;

  /* Alignment (all children) */
  align-items: stretch;    /* vertical */
  justify-items: stretch;  /* horizontal */
}

/* Named area placement */
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.main    { grid-area: main; }

/* Line number / span placement */
.wide-item { grid-column: span 2; }
.tall-item { grid-row: 1 / span 2; }
.full-width { grid-column: 1 / -1; }

/* Per-item alignment override */
.special { align-self: center; justify-self: end; }

/* Responsive card grid */
.cards {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 16px;
}
```
