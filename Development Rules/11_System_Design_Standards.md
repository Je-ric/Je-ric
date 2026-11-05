# Design System

## 1. Color Palettes

### 1.1 Emerald (Primary Scale)

A 10-step scale used as the app's core accent color, from near-white tint to near-black shade.

| Step | Hex       | Notes                        |
|------|-----------|-------------------------------|
| 50   | `#EDFFF8` | Lightest tint — backgrounds  |
| 100  | `#D5FFF0` | Subtle backgrounds           |
| 200  | `#AEFFE2` | Light accents                |
| 300  | `#70FFCC` | Light accents                |
| 400  | `#2BFDB0` | Bright accent                |
| 500  | `#00D88B` | Base / brand color (locked)  |
| 600  | `#00C075` | Hover state                  |
| 700  | `#00965F` | Active state                 |
| 800  | `#06754E` | Strong accent                |
| 900  | `#076042` | Dark accent                  |
| 950  | `#003724` | Darkest shade                |

### 1.2 Primary Colors (Blue / Charcoal / Grey)

**Blue**
| Step | Hex |
|------|-----|
| 100  | `#F7FCFE` |
| 200* | `#DAF1FF` |
| 300* | `#AEDFFF` |
| 400  | `#71BFF1` |
| 500  | `#3197D6` (base) |
| 600* | `#237A83` |
| 700* | `#1F5E89` |
| 800  | `#194C6E` |
| 900  | `#143D57` |

**Charcoal**
| Step | Hex |
|------|-----|
| 200  | `#93A1AF` |
| 300  | `#72809E` |
| 400  | `#4F5D6B` |
| 500  | `#394056` (base) |
| 600  | `#2A3B47` |
| 700  | `#253540` |
| 800  | `#1D2836` |

**Grey**
| Step | Hex |
|------|-----|
| 200  | `#F9FAFA` |
| 300  | `#F1F3F5` |
| 400  | `#E3E8EB` |
| 500  | `#D6DDE3` (base) |
| 600  | `#C1C8D4` |
| 700  | `#B4C0CA` |
| 800  | `#A5B2BD` |

### 1.3 Secondary Colors (Yellow / Green / Red)

**Yellow**
| Step | Hex |
|------|-----|
| 100  | `#FFFDF6` |
| 200  | `#FFF6E2` |
| 300  | `#FFE9B5` |
| 400  | `#FFD56D` |
| 500  | `#FFC646` (base) |
| 600  | `#F5B126` |
| 700* | `#D79400` |
| 800* | `#B37100` |
| 900  | `#875200` |

**Green**
| Step | Hex |
|------|-----|
| 100  | `#FAFDFB` |
| 200* | `#E4FB68` |
| 300* | `#C4F0CE` |
| 400  | `#81DC9E` |
| 500  | `#4BC27D` (base) |
| 600* | `#3CB170` |
| 700  | `#2F9F62` |
| 800* | `#228350` |
| 900  | `#23633a` |

**Red**
| Step | Hex |
|------|-----|
| 100  | `#FEF7F6` |
| 200* | `#FFE3E2` |
| 300* | `#FFA2A2` |
| 400  | `#F45855` |
| 500  | `#E52F28` (base) |
| 600  | `#D21B14` |
| 700* | `#BA1F19` |
| 800* | `#9D1F1A` |
| 900  | `#731814` |

*Asterisked values as printed in the source reference; verify against source file if precision matters.*

---

## 2. Gradients

**Brand card gradient** (e.g. "Customers" stat card)
```css
background: linear-gradient(135deg, #00D88B 0%, #06754E 100%);
/* Emerald 500 → Emerald 800, diagonal */
```
Used for hero/stat cards with white text on top for strong contrast.

---

## 3. Shadows & Elevation

Cards use a soft, low-opacity shadow with a light border to sit above the page background.

```css
--shadow-card: 0 1px 2px rgba(16, 24, 40, 0.04), 0 1px 3px rgba(16, 24, 40, 0.06);

.card {
  border: var(--border-width-default) solid var(--border-default);
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-card);
}
```

Interactive elements (buttons, badges) use a subtler shadow or none at all — relying instead on background color changes for state changes.

```css
--shadow-button: 0 1px 2px rgba(16, 24, 40, 0.05);
```

---

## 4. Borders & Radius

### 4.1 Border Colors
```css
--border-default: #E3E8EB;  /* Grey 400 — card & container outlines */
--border-subtle:  #F1F3F5;  /* Grey 300 — dividers, faint separators */
--border-strong:  #D6DDE3;  /* Grey 500 — inputs, more defined outlines */
--border-brand:   #00D88B;  /* Emerald 500 — focus / selected / outline buttons */
--border-brand-hover: #00C075; /* Emerald 600 */
```
```css
--border-width-default: 1px;
--border-width-thick:   1.5px; /* outline buttons */
```

### 4.2 Radius Scale
```css
--radius-xs:   6px;   /* small tags, chips */
--radius-sm:   8px;   /* inputs, list item pills */
--radius-md:   10px;  /* buttons */
--radius-lg:   16px;  /* cards, panels */
--radius-xl:   20px;  /* hero/stat cards, large containers */
--radius-full: 999px; /* circular avatars, selected-day dot, status dots */
```

### 4.3 Applied Radius
| Element                          | Radius            |
|-----------------------------------|--------------------|
| Cards / panels (Calendar, Today)  | `--radius-lg` (16px) |
| Stat / hero card (Customers)      | `--radius-xl` (20px) |
| Buttons (all variants)            | `--radius-md` (10px) |
| List item pills (Today card)      | `--radius-sm` (8px)  |
| Info boxes                        | `--radius-sm`–`--radius-md` |
| Calendar selected-day marker      | `--radius-full` (circle) |
| Avatars                           | `--radius-full` (circle) |
| Color swatches                    | `--radius-sm` (8px), top corners only on labeled swatch |

---

## 5. Buttons

Three visual styles, each with four states: Default, Hover, Active, Disabled.

### 4.1 Flat
Solid emerald fill, white text.
```css
.btn-flat            { background: #00D88B; color: #FFFFFF; }
.btn-flat:hover       { background: #00C075; }  /* 600 */
.btn-flat:active      { background: #00965F; }  /* 700 */
.btn-flat:disabled    { background: #AEFFE2; color: #FFFFFF; opacity: 0.7; } /* 200 */
```

### 4.2 Outline
Transparent fill, emerald border and text.
```css
.btn-outline          { background: transparent; border: var(--border-width-thick) solid var(--border-brand); color: #00D88B; }
.btn-outline:hover     { background: #EDFFF8; border-color: var(--border-brand-hover); color: #00C075; } /* 50 / 600 */
.btn-outline:active    { background: #D5FFF0; border-color: #00965F; color: #00965F; } /* 100 / 700 */
.btn-outline:disabled  { border-color: #AEFFE2; color: #AEFFE2; }
```

### 4.3 Bezel
Solid fill similar to Flat but slightly deeper/darker rest state, used for a more "pressed" or tactile look.
```css
.btn-bezel            { background: #00C075; color: #FFFFFF; }  /* 600 */
.btn-bezel:hover       { background: #00A868; }
.btn-bezel:active      { background: #00965F; }  /* 700 */
.btn-bezel:disabled    { background: #AEFFE2; color: #FFFFFF; opacity: 0.7; }
```

**Shared button tokens**
```css
--btn-radius: var(--radius-md); /* 10px */
--btn-padding-y: 10px;
--btn-padding-x: 16px;
--btn-font-weight: 600;
```

---

## 6. Cards & Components

### 5.1 Stat Card (e.g. "Customers")
- Gradient background (Emerald 500 → 800)
- White label text (uppercase or medium weight, small size)
- Large bold number, white
- Small trend arrow icon inline with the number
- Supporting caption text in translucent white

### 5.2 Calendar Card
- White background, card shadow + border
- Grid of day numbers, muted grey text (`Charcoal 300`) for weekday labels
- Selected day: solid emerald circle background (`500`), white text
- Range/highlighted day: light emerald circle (`200`), dark text
- Primary CTA button ("Continue") in Flat style, full width, bottom-aligned

### 5.3 List Card (e.g. "Today")
- White background, card shadow + border
- Each list item on a light emerald background pill (`Emerald 100`–`200`)
- Item title bold, dark text
- Time/subtext in muted grey below title
- Avatar stack (overlapping circular avatars) bottom-left of each item
- Overflow "…" menu icon top-right of each item

### 5.4 Info Box
Two intensity styles for inline notice/alert bars:
```css
.info-box-1 { background: #AEFFE2; } /* Emerald 200 */
.info-box-2 { background: #70FFCC; } /* Emerald 300 */
```
- Icon (e.g. info/lock) in a filled emerald circle on the left
- Single line of dark text
- Full-width, rounded corners (`--radius-card` or slightly smaller, ~12px)

---

## 7. Typography (inferred from layout)

```css
--font-heading: 600-700 weight, dark charcoal/black
--font-body: 400-500 weight, charcoal 500
--font-caption: 400 weight, charcoal 300, smaller size
```

- Section labels (e.g. "Color Scheme", "Customers", "Calendar") use a smaller, medium-weight label style, often muted grey when not the primary heading.
- Large numeric stats use a bold, large display size (e.g. "1.553").

---

## 8. Usage Notes

- **Base/brand color** is Emerald 500 (`#00D88B`) — treat as the single source of truth for primary actions.
- Lighter steps (50–300) are reserved for backgrounds, hover fills, and subtle highlights — never for text on white.
- Darker steps (700–950) are reserved for text-on-light-background use or deep gradient ends — avoid using as large fill areas except in gradients/hero cards.
- Secondary palette (Yellow/Green/Red) should be reserved for status/semantic meaning (warning, success, error) rather than general UI decoration.
