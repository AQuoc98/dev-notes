**Status:** 🚧 - **Last Updated:** 1st May 2026

# Table of Contents
- [Table of Contents](#table-of-contents)
  - [Resources](#resources)
  - [How to use line clamps in CSS](#how-to-use-line-clamps-in-css)
  - [How to set gradient color for text](#how-to-set-gradient-color-for-text)
  - [What is the difference between `px`, `em`, and `rem` in CSS?](#what-is-the-difference-between-px-em-and-rem-in-css)
  - [What is the difference between `content-box` and `border-box` in CSS?](#what-is-the-difference-between-content-box-and-border-box-in-css)

## Resources
- [**CSS Portal**](https://www.cssportal.com/) - A comprehensive resource for CSS tools, tutorials, and references.
- [**Neumorphism.io**](https://neumorphism.io/) - A tool for generating neumorphic design elements with customizable colors and shadows.
- Colors
  - [**Tailwind Colors**](https://uicolors.app/tailwind-colors)
  - [**Hover Colors**](https://www.hover.dev/css-color-palette-generator) - Generate a color palette based on a single color input, with hover state variations.
  - [**Coolors**](https://coolors.co/palettes/trending) - Explore trending color palettes and create your own combinations for design projects.
  - [**HTML Color Codes**](https://htmlcolorcodes.com/color-names/) - A comprehensive list of color names and their corresponding hex codes for easy reference in web design.
- Fonts
  - [**Type Scale**](https://typescale.com/) - A tool for creating and visualizing font scales for your web projects.
  - [**Google Fonts**](https://fonts.google.com/) - A vast library of free and open-source fonts for your web projects.

[Back to top](#table-of-contents)

## How to use line clamps in CSS
```css
p {
    -webkit-line-clamp: 3;
    display: -webkit-box;
    -webkit-box-orient: vertical;
    overflow: hidden;
}

input, button or inline component {
    text-overflow: ellipsis;
    white-space: nowrap;
    overflow: hidden;
}
```

[Back to top](#table-of-contents)

## How to set gradient color for text
```css
.gradient-text {
    background: linear-gradient(to right, #ff7e5f, #feb47b);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
}
```

[Back to top](#table-of-contents)

## What is the difference between `px`, `em`, and `rem` in CSS?

`px`, `em`, and `rem` are all length units, but they differ in what they are relative to and how they scale.

| Aspect            | `px`                                           | `em`                                                                 | `rem`                                                       |
| ----------------- | ---------------------------------------------- | -------------------------------------------------------------------- | ----------------------------------------------------------- |
| Type              | Absolute                                       | Relative                                                             | Relative                                                    |
| Relative to       | Nothing (a fixed CSS pixel)                    | The `font-size` of the **parent** element                            | The `font-size` of the **root** element (`<html>`)          |
| Scales with user's browser font setting | No                                  | Yes                                                                  | Yes                                                         |
| Compounding       | No                                             | Yes — nested elements multiply the effect                            | No — always resolves against the root                       |
| Predictability    | Highest                                        | Lowest (depends on ancestor chain)                                   | High (single source of truth)                               |
| Typical use       | Borders, fine details, fixed-size icons        | Spacing/sizing that should scale with the local component's text     | Global typography, layout spacing, breakpoints              |
| Example           | `border: 1px solid #000;`                      | `padding: 1.5em;` (1.5 × parent's font-size)                         | `font-size: 1.25rem;` (1.25 × root font-size)               |
| Accessibility     | Ignores user font preferences                  | Respects user font preferences                                       | Respects user font preferences                              |

**Quick example** — assuming `html { font-size: 16px }` and a parent with `font-size: 20px`:

```css
.child-px  { font-size: 16px;   } /* always 16px */
.child-em  { font-size: 1rem;   } /* 16px (root) */
.child-em2 { font-size: 1em;    } /* 20px (parent) */
.child-rem { font-size: 1.5rem; } /* 24px (1.5 × root) */
```

**Rule of thumb**
- Use `rem` for most things (typography, spacing, layout) so the UI scales with the user's preferred font size.
- Use `em` when a value should scale relative to the *component's own* text (e.g. button padding that grows with its label).
- Use `px` only when you truly want a fixed value (hairline borders, 1px dividers, shadow offsets).
- `%` is another relative unit that scales based on the parent element's size, often used for widths and heights in responsive design.
- `vw` and `vh` are viewport-relative units that scale based on the size of the browser window, useful for full-screen layouts and responsive typography.

## What is the difference between `content-box` and `border-box` in CSS?

`box-sizing` controls how the `width` and `height` you set are interpreted relative to padding and border.

| Aspect                        | `content-box` (default)                                   | `border-box`                                                  |
| ----------------------------- | --------------------------------------------------------- | ------------------------------------------------------------- |
| What `width` / `height` cover | Content area only                                         | Content + padding + border                                    |
| Total rendered width          | `width + padding-left + padding-right + border-l + border-r` | Exactly `width` (padding & border are subtracted from it)  |
| Adding padding/border         | Makes the element **bigger**                              | Keeps the outer size the same; content area shrinks           |
| Predictability for layout     | Harder — must do math                                     | Easier — `width` is what you see                              |
| Margin                        | Not included in either case                               | Not included in either case                                   |
| Typical use                   | Legacy code, fine-grained typographic control             | Modern layouts, grids, components, design systems             |

**Example** — given `width: 200px; padding: 20px; border: 5px solid;`

```css
.content-box { box-sizing: content-box; } /* total width = 200 + 40 + 10 = 250px */
.border-box  { box-sizing: border-box;  } /* total width = 200px (content area = 150px) */
```

**Common reset** used in most modern projects so every element behaves predictably:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

**Rule of thumb**
- Default to `border-box` project-wide — sizes match what you see and grids/flex layouts stay consistent when padding changes.
- Use `content-box` only for niche cases where you specifically need the content area itself to be a fixed size.

