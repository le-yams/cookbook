# Cooklang Language Specification

Reference: <https://cooklang.org/docs/spec/>

## Overview

Cooklang is a lightweight markup language for writing recipes in plain text.
Each recipe is a `.cook` file containing human-readable instructions with
machine-parseable markup for ingredients, cookware, timers, and metadata.

---

## Syntax Elements

### Ingredients (`@`)

| Pattern | Example | Result |
|---|---|---|
| Single word | `@salt` | ingredient: salt |
| Multi-word (end with `{}`) | `@ground black pepper{}` | ingredient: ground black pepper |
| With quantity | `@potato{2}` | ingredient: potato, qty: 2 |
| Quantity + unit (`%` separator) | `@milk{4%cup}` | ingredient: milk, qty: 4 cup |
| Fraction | `@syrup{1/2%tbsp}` | ingredient: syrup, qty: 1/2 tbsp |
| Preparation note (parentheses) | `@onion{1}(finely chopped)` | ingredient: onion, qty: 1, prep: finely chopped |
| Fixed quantity (no scaling) | `@salt{=1%tsp}` | stays 1 tsp when recipe is scaled |
| Recipe reference | `@./sauces/Hollandaise{150%g}` | link to another recipe |

### Cookware (`#`)

| Pattern | Example |
|---|---|
| Single word | `#pot` |
| Multi-word | `#baking sheet{}` |
| With quantity | `#bowl{1}` |

### Timers (`~`)

| Pattern | Example |
|---|---|
| Duration only | `~{25%minutes}` |
| Named timer | `~eggs{3%minutes}` |

Format: `~[name]{quantity%unit}`

### Comments

| Type | Syntax |
|---|---|
| Line comment | `-- comment text` |
| Block comment | `[- comment text -]` |

---

## Recipe Structure

### Metadata (YAML front matter)

```yaml
---
title: Spaghetti Carbonara
tags:
  - pasta
  - quick
servings: 2
prep time: 10 minutes
cook time: 20 minutes
source: https://example.com/recipe
---
```

**Canonical metadata keys:**

- `title` -- recipe name
- `tags` -- descriptive tags (array)
- `servings` / `serves` / `yield` -- number of portions (used for scaling)
- `source`, `source.name`, `source.url`, `source.author` -- origin
- `course` / `category` -- meal type
- `locale` -- language (ISO 639, e.g. `fr`, `en_GB`)
- `prep time` / `time.prep` -- preparation time
- `cook time` / `time.cook` -- cooking time
- `time required` / `time` / `duration` -- total time
- `difficulty` -- easy, medium, hard
- `cuisine` -- cuisine type
- `diet` -- dietary restrictions
- `image` / `images` -- image URLs

### Steps

Each paragraph is one cooking step. Separate steps with blank lines.

```
Crack the eggs into a blender, add the flour and milk, blitz until smooth.

Pour into a bowl and leave to stand for ~{15%minutes}.
```

Line continuation with `\`:

```
Lay out the @rice paper{1}.\
Top with @avocado{1/2}(sliced).
```

### Notes (`>`)

Background info, tips, not part of cooking steps:

```
> These freeze well. Double the batch for later.
```

### Sections (`=`)

Organize complex recipes into components:

```
= Dough
Mix @flour{200%g} and @water{100%ml}.

== Filling
Combine @cheese{100%g} and @spinach{50%g}.
```

Number of `=` symbols sets nesting level. Trailing `=` are optional.

---

## Quantity Format

```
{quantity}          -- just a number: {2}
{quantity%unit}     -- number + unit: {1%kg}
{=quantity%unit}    -- fixed (no scaling): {=1%tsp}
{}                  -- no quantity (marks multi-word boundary)
```

Quantities accept integers, decimals (`1.5`), and fractions (`1/2`).

Units are free-form text (anything between `%` and `}`).

---

## Scaling

- Default servings from metadata (defaults to 1 if unset)
- Ingredients scale linearly with servings
- Fixed quantities (`=`) do not scale
- Timers and cookware do not scale

---

## Recipe Pictures

Automatic matching by filename:

```
Baked Potato.cook
Baked Potato.jpg       -- main recipe image
Baked Potato.0.jpg     -- step 0 image
Baked Potato.3.jpg     -- step 3 image
```

Or via metadata `image` / `images` keys.

---

## Parsing Rules

- `@` + text = ingredient
- `#` + text = cookware
- `~` = timer
- `--` = line comment
- `[- ... -]` = block comment
- `>` at line start = note
- `=` at line start = section
- `{}` = quantity / component boundary
- `%` inside `{}` = quantity-unit separator
- `\` at line end = line continuation within step
- Multi-word ingredients/cookware must end with `{}`
- Steps are separated by blank lines
