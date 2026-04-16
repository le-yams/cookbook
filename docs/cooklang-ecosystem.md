# Cooklang Ecosystem & File Conventions

Reference: <https://cooklang.org/docs/spec/>

## File Types

| Extension | Purpose |
|---|---|
| `.cook` | Recipe file with Cooklang markup |
| `.menu` | Meal plan referencing recipes |
| `.shopping-list` | Shopping list definition |
| `.shopping-checked` | Checked items log (append-only) |
| `aisle.conf` | Shopping list category config |
| `pantry.conf` | Pantry inventory (TOML format) |

---

## Menu Files (`.menu`)

Organize recipes across meals/days using sections and recipe references:

```
= Monday
@./mains/pasta carbonara{2}
@./sides/green salad{}

= Tuesday
@./mains/chicken stir fry{4}
@./sides/steamed rice{4}
```

Sections can include dates:

```
== Day 1 (2026-03-07) ==
@./breakfast/shakshuka{4%servings}
```

**Scaling in menus:**

- No units: scale by factor (`@./bread{2}` = double the recipe)
- `%servings`: scale to match serving count
- `%units`: scale to produce specific quantity (experimental)

---

## Shopping List

### Definition (`.shopping-list`)

Line-oriented. Recipe references start with `./`:

```
./Breakfast/Mexican Style Burrito{2}
./Components/Guacamole{2}
olive oil{4%l}
salt -- remember to check the pantry
```

### Check File (`.shopping-checked`)

Append-only log of checked/unchecked items:

```
+ salt
+ avocados
- avocados
+ avocados
```

Matching is case-insensitive. Last entry wins.

---

## Aisle Configuration (`aisle.conf`)

Organize ingredients by shopping category/aisle:

```
[produce]
potatoes
bell peppers
tomatoes | cherry tomatoes

[dairy]
milk
butter
cheese | cheddar

[pantry]
rice
olive oil | extra virgin olive oil
```

Use `|` to define ingredient synonyms.

---

## Pantry Configuration (`pantry.conf`)

TOML format with storage location sections:

**Simple format:**

```toml
[freezer]
cranberries = "500%g"

[fridge]
milk = "1%L"

[pantry]
rice = "5%kg"
```

**Extended format with metadata:**

```toml
[freezer]
spinach = { bought = "05.05.2024", expire = "05.06.2025", quantity = "1%kg" }

[fridge]
milk = { expire = "10.05.2024", quantity = "1%L" }

[pantry]
pasta = { quantity = "1%kg", low = "200%g" }
```

Attributes: `bought`, `expire` (DD.MM.YYYY), `quantity`, `low` (alert threshold).

---

## Directory Structure (typical)

```
recipes/
  Breakfast/
    Pancakes.cook
    Pancakes.jpg
  Mains/
    Pasta Carbonara.cook
    Pasta Carbonara.jpg
    Pasta Carbonara.0.jpg
  Sauces/
    Hollandaise.cook
  Sides/
    Green Salad.cook
  aisle.conf
  pantry.conf
  Weekly.menu
```

---

## Tools & Editors

- **CookCLI**: <https://github.com/cooklang/CookCLI>
- **VS Code extension**: Cooklang syntax highlighting
- **iOS / Android apps**: official Cooklang apps
- **Cooklang Playground**: <https://cooklang.github.io/cooklang-rs/>
- **Parser libraries**: Rust, Python, Go, JS/TS, Swift, Ruby, and more
