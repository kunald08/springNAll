# HTML & CSS — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is HTML? What does it do?

**Answer:**
HTML stands for **HyperText Markup Language**. It's the standard language for structuring content on the web. HTML defines WHAT is on the page — headings, paragraphs, links, images, forms — using **tags** or **elements**.

It's NOT a programming language — it's a **markup language**. It doesn't have logic, loops, or variables. It just describes the structure and content of a web page. The browser reads the HTML and renders it visually.

A basic HTML document has: `<!DOCTYPE html>`, `<html>`, `<head>` (metadata, title, CSS links), and `<body>` (visible content).

---

## 2. What is the difference between HTML elements, tags, and attributes?

**Answer:**
- **Tag** — the markup syntax itself: `<p>`, `</p>`, `<img>`. An opening tag `<p>` and closing tag `</p>` together form...
- **Element** — the complete unit: `<p>Hello World</p>` — that's a paragraph element. Some elements are self-closing like `<img />`, `<br />`, `<input />`.
- **Attribute** — extra information inside the opening tag: `<a href="https://google.com" target="_blank">` — here `href` and `target` are attributes.

Attributes always go in the opening tag, written as `name="value"` pairs.

---

## 3. What are semantic HTML elements? Why are they important?

**Answer:**
Semantic elements clearly describe their **meaning** to both the browser and the developer. Instead of using generic `<div>` for everything, semantic elements tell you WHAT the content is.

Examples:
- `<header>` — page or section header
- `<nav>` — navigation links
- `<main>` — main content area
- `<article>` — self-contained content (blog post, news article)
- `<section>` — thematic grouping of content
- `<aside>` — sidebar content
- `<footer>` — page or section footer
- `<figure>` / `<figcaption>` — image with caption

Why they matter:
1. **Accessibility** — screen readers use semantic elements to help visually impaired users navigate the page.
2. **SEO** — search engines understand the page structure better, improving rankings.
3. **Readability** — `<nav>` is instantly understandable, `<div class="nav">` requires reading the class name.
4. **Maintainability** — the code is self-documenting.

---

## 4. What is the difference between `<div>` and `<span>`?

**Answer:**
- **`<div>`** — a **block-level** container. It takes up the full width and starts on a new line. Used for grouping large sections of content.
- **`<span>`** — an **inline** container. It only takes up as much width as its content. Used for styling small pieces of text within a line.

Example: If I want to highlight one word in a paragraph, I'd wrap it in `<span>`. If I want to group a heading, paragraph, and image into a card, I'd use `<div>`.

Neither has any semantic meaning — they're purely for styling/grouping. That's why semantic elements are preferred when applicable.

---

## 5. What is the difference between block-level and inline elements?

**Answer:**
- **Block-level** — takes up the full width, starts on a new line. Examples: `<div>`, `<p>`, `<h1>-<h6>`, `<ul>`, `<form>`, `<section>`, `<header>`. I can set width, height, margin, and padding on all sides.

- **Inline** — only takes up as much width as needed, does NOT start on a new line. Examples: `<span>`, `<a>`, `<strong>`, `<em>`, `<img>`, `<input>`. I can't set width/height; top/bottom margins are ignored.

- **Inline-block** — behaves inline (doesn't break to new line) but allows setting width, height, and margins like a block element. Set via CSS: `display: inline-block`.

---

## 6. What are the different types of HTML form inputs?

**Answer:**
HTML5 introduced many input types:

- `text` — regular text input
- `password` — hidden text
- `email` — validates email format
- `number` — numeric input with spinner
- `tel` — phone number
- `url` — validates URL format
- `date`, `time`, `datetime-local` — date/time pickers
- `range` — slider
- `color` — color picker
- `file` — file upload
- `checkbox` — multiple selections
- `radio` — single selection from a group
- `hidden` — not visible but submitted with form
- `submit` — submit button
- `textarea` — multi-line text (separate element, not an input type)
- `select` / `option` — dropdown

For validation, I use attributes like `required`, `minlength`, `maxlength`, `pattern` (regex), `min`, `max`, and `placeholder`.

---

## 7. What is the `DOCTYPE` declaration?

**Answer:**
`<!DOCTYPE html>` tells the browser which version of HTML the document is written in. For HTML5, it's simply `<!DOCTYPE html>`.

Without it, the browser goes into **"quirks mode"** — it renders the page using older, inconsistent rules for backwards compatibility with ancient web pages. With the DOCTYPE, the browser uses **"standards mode"** — modern, consistent rendering.

It must be the very first line of the HTML document, before the `<html>` tag. It's not an HTML element — it's an instruction to the browser.

---

## 8. What is the difference between `localStorage` and `sessionStorage`?

**Answer:**
Both are part of the **Web Storage API** and store key-value pairs in the browser:

- **`localStorage`** — data persists even after the browser is closed. It stays until explicitly cleared. Useful for user preferences, themes, or saved data.

- **`sessionStorage`** — data is cleared when the browser tab is closed. It's scoped to that specific tab. Useful for temporary data during a single session.

Both:
- Store strings only (use `JSON.stringify()` / `JSON.parse()` for objects)
- Have ~5MB storage limit
- Are synchronous (block the main thread)
- Are NOT sent to the server with HTTP requests (unlike cookies)

Methods: `.setItem(key, value)`, `.getItem(key)`, `.removeItem(key)`, `.clear()`

---

## 9. What is CSS? How do you apply it to HTML?

**Answer:**
CSS stands for **Cascading Style Sheets**. It controls the **visual presentation** — colors, fonts, layout, spacing, animations — of HTML elements. HTML is the structure, CSS is the style.

Three ways to apply CSS:

1. **Inline** — directly on the element: `<p style="color: red;">`. Quick but not reusable, and hard to maintain. Avoid for production.

2. **Internal** — in a `<style>` block inside `<head>`. Good for single-page demos, but doesn't scale.

3. **External (preferred)** — in a separate `.css` file linked via `<link rel="stylesheet" href="style.css">`. Clean separation of concerns, cacheable by the browser, reusable across pages.

The "cascading" means styles can be inherited and overridden based on **specificity** and **order**.

---

## 10. Explain CSS specificity. How does the browser decide which style wins?

**Answer:**
When multiple CSS rules target the same element, **specificity** determines which one applies. It's calculated as a score:

| Selector | Specificity |
|----------|-------------|
| Inline style (`style=""`) | 1000 |
| ID (`#header`) | 100 |
| Class, attribute, pseudo-class (`.card`, `[type]`, `:hover`) | 10 |
| Element, pseudo-element (`p`, `::before`) | 1 |
| Universal (`*`) | 0 |

Example: `#header .nav a` = 100 + 10 + 1 = **111**
`div.card p` = 1 + 10 + 1 = **12**
The first one wins because 111 > 12.

If specificity is equal, the **last rule** in the CSS file wins.

`!important` overrides everything — but it makes debugging hard, so I avoid it unless absolutely necessary.

---

## 11. What is the CSS Box Model?

**Answer:**
Every HTML element is a rectangular box made of four layers (from inside out):

1. **Content** — the actual text, image, etc.
2. **Padding** — space between content and the border (transparent, takes background color)
3. **Border** — the edge around the padding
4. **Margin** — space outside the border (transparent, creates gaps between elements)

By default (`box-sizing: content-box`), width/height apply ONLY to the content. Padding and border are extra.

I always use **`box-sizing: border-box`** — it makes width/height include padding and border. This makes calculations predictable:
```css
* { box-sizing: border-box; }
```

With `border-box`: if I set `width: 300px`, the element is exactly 300px regardless of padding or border.

---

## 12. What is Flexbox? When do you use it?

**Answer:**
Flexbox is a **one-dimensional layout** system — it arranges items in a row OR a column. I use it for aligning and distributing space among items inside a container.

```css
.container {
    display: flex;
    justify-content: center;    /* Horizontal alignment */
    align-items: center;        /* Vertical alignment */
    gap: 16px;                  /* Space between items */
}
```

Key properties on the **container** (parent):
- `flex-direction` — `row` (default) or `column`
- `justify-content` — alignment along the main axis (`center`, `space-between`, `space-around`, `flex-start`, `flex-end`)
- `align-items` — alignment along the cross axis (`center`, `stretch`, `flex-start`, `flex-end`)
- `flex-wrap` — `wrap` to allow items to flow to next line

Key properties on **items** (children):
- `flex-grow` — how much extra space to take (0 = don't grow)
- `flex-shrink` — how much to shrink if needed
- `flex-basis` — initial size before growing/shrinking
- `align-self` — override the container's `align-items` for this item

Common use cases: navigation bars, card layouts, centering content, equal-height columns.

---

## 13. What is CSS Grid? How is it different from Flexbox?

**Answer:**
CSS Grid is a **two-dimensional layout** system — it handles both rows AND columns simultaneously. Flexbox is one-dimensional (row or column, not both).

```css
.container {
    display: grid;
    grid-template-columns: 1fr 2fr 1fr;    /* 3 columns */
    grid-template-rows: auto 1fr auto;      /* 3 rows */
    gap: 16px;
}
```

When to use each:
- **Flexbox** — for components within a section: navbars, button groups, card content, aligning items in a row.
- **Grid** — for full page layouts: defining header, sidebar, main content, footer areas.

They work great together — I use Grid for the page layout and Flexbox inside Grid items for component-level alignment.

---

## 14. What is responsive design? How do you make a website responsive?

**Answer:**
Responsive design means the website **adapts its layout and appearance** to different screen sizes — desktop, tablet, and mobile — using a single codebase.

Techniques:
1. **Viewport meta tag**: `<meta name="viewport" content="width=device-width, initial-scale=1.0">` — essential for mobile rendering.

2. **Media queries**: Apply different CSS at different screen widths:
```css
@media (max-width: 768px) {
    .container { flex-direction: column; }
}
```

3. **Relative units**: Use `%`, `em`, `rem`, `vw`, `vh` instead of fixed `px`.

4. **Flexbox and Grid**: Naturally flexible layouts.

5. **Mobile-first approach**: Write CSS for mobile first, then add `min-width` media queries for larger screens. This is the recommended approach because mobile is the baseline.

Common breakpoints: 576px (mobile), 768px (tablet), 992px (laptop), 1200px (desktop).

---

## 15. What is the difference between `em`, `rem`, `px`, `%`, `vw`, and `vh`?

**Answer:**
- **`px`** — absolute unit. 16px is always 16px. Good for borders and shadows.
- **`em`** — relative to the **parent element's** font size. If parent is 16px, `1.5em` = 24px. It compounds (nested elements multiply), which can cause unexpected sizes.
- **`rem`** — relative to the **root** (`<html>`) font size. `1rem` = root font size (usually 16px). More predictable than `em` — I prefer `rem` for most sizing.
- **`%`** — relative to the parent's dimension. `width: 50%` is half the parent's width.
- **`vw`** — 1% of the **viewport width**. `100vw` = full screen width.
- **`vh`** — 1% of the **viewport height**. `100vh` = full screen height.

My go-to: `rem` for font sizes and spacing, `%` or `fr` for layout widths, `px` for tiny details like borders.

---

## 16. What is the `position` property in CSS?

**Answer:**
It controls how an element is positioned in the document:

- **`static`** (default) — normal document flow. `top`, `left`, etc. have no effect.
- **`relative`** — positioned relative to its NORMAL position. It still occupies its original space. `top: 10px` moves it 10px from where it'd normally be.
- **`absolute`** — positioned relative to the nearest **positioned ancestor** (one with `position: relative/absolute/fixed`). Removed from document flow — other elements act as if it doesn't exist.
- **`fixed`** — positioned relative to the **viewport**. Stays in place when scrolling. Used for sticky headers and floating buttons.
- **`sticky`** — hybrid of relative and fixed. It's relative until a scroll threshold, then becomes fixed. Used for headers that stick when scrolling.

The most common pattern is setting `position: relative` on a parent and `position: absolute` on a child to position the child within the parent.

---

## 17. What is the `z-index`? How does it work?

**Answer:**
`z-index` controls the **stacking order** of overlapping elements — which element appears on top. Higher values appear in front of lower values.

It only works on **positioned elements** (not `static`). Elements with `position: relative`, `absolute`, `fixed`, or `sticky` can use `z-index`.

```css
.modal { z-index: 1000; }
.overlay { z-index: 999; }
.header { z-index: 100; }
```

Important concept: **stacking context**. A new stacking context is created by elements with `z-index`, `opacity < 1`, `transform`, or `position: fixed`. A child's `z-index` only competes with siblings within the same stacking context, not globally.

---

## 18. What are CSS transitions and animations?

**Answer:**
**Transitions** — smooth changes between two states triggered by a user action (hover, click, etc.):
```css
.button {
    background: blue;
    transition: background 0.3s ease;
}
.button:hover {
    background: red;    /* Smoothly transitions over 0.3s */
}
```

**Animations** — for multi-step, automatic animations using `@keyframes`:
```css
@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}
.element {
    animation: fadeIn 0.5s ease-in-out;
}
```

Transition = simple A→B change on user action. Animation = complex multi-step sequence that can run automatically, loop, reverse, etc.

For performance, I animate only `transform` and `opacity` — they're handled by the GPU and don't trigger expensive layout recalculations.

---

## 19. What is the difference between `display: none` and `visibility: hidden`?

**Answer:**
- **`display: none`** — completely removes the element from the document flow. Other elements collapse into the space it occupied. The element is invisible AND takes up no space. Screen readers also skip it.

- **`visibility: hidden`** — hides the element visually, but it still **occupies its space** in the layout. Other elements don't move. Screen readers may still announce it.

If I want to toggle content visibility and have the layout shift (like showing/hiding a menu), I use `display: none`. If I want to hide something but keep the layout intact (like a placeholder), I use `visibility: hidden`.

There's also `opacity: 0` — invisible but still interactive (clickable), still takes up space. Useful for animation purposes.

---

## 20. How do you center a `div` both horizontally and vertically?

**Answer:**
This is one of the most common CSS questions. Several approaches:

**Method 1: Flexbox (my go-to)**
```css
.parent {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}
```

**Method 2: Grid**
```css
.parent {
    display: grid;
    place-items: center;
    height: 100vh;
}
```

**Method 3: Absolute + Transform**
```css
.child {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
}
```

I use Flexbox for most cases — it's clean, readable, and works for any content. Grid's `place-items: center` is the shortest syntax. The transform approach is useful when I can't change the parent's display property.
