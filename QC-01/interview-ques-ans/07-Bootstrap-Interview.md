# Bootstrap — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is Bootstrap? Why do we use it?

**Answer:**
Bootstrap is a free, open-source **CSS framework** (originally by Twitter) that provides pre-built components, a responsive grid system, and utility classes for building modern, mobile-first websites quickly.

We use it because:
- **Speed** — instead of writing CSS from scratch, I use ready-made classes for layout, buttons, forms, modals, etc.
- **Responsive** — the grid system and components are responsive out of the box.
- **Consistency** — provides a uniform look and feel across browsers.
- **Customizable** — I can override variables and themes to match the project's design.
- **Community** — huge ecosystem, tons of themes, and well-documented.

The current version is **Bootstrap 5**, which dropped jQuery as a dependency and uses vanilla JavaScript.

---

## 2. How do you include Bootstrap in a project?

**Answer:**
Two main ways:

**CDN (quick, no installation):**
```html
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
```

**npm (for build tools/bundlers):**
```bash
npm install bootstrap
```
Then import in your JS/CSS files.

The CSS link goes in the `<head>`, and the JS script goes at the end of `<body>` (or with `defer`). The `bundle.min.js` includes Popper.js which is needed for tooltips and dropdowns.

---

## 3. Explain the Bootstrap Grid System.

**Answer:**
Bootstrap uses a **12-column grid** system that's responsive and mobile-first. Everything is divided into 12 equal columns.

The structure is: **Container → Row → Columns**

```html
<div class="container">
    <div class="row">
        <div class="col-md-8">Main Content (8/12 = 66%)</div>
        <div class="col-md-4">Sidebar (4/12 = 33%)</div>
    </div>
</div>
```

Key concepts:
- **`container`** — fixed-width wrapper. `container-fluid` spans full width.
- **`row`** — horizontal group of columns (uses flexbox).
- **`col-*`** — column width. Numbers must add up to 12 (or less).

The grid is responsive — I specify different column widths at different breakpoints:
```html
<div class="col-12 col-md-6 col-lg-4">
    <!-- Full width on mobile, half on tablet, one-third on desktop -->
</div>
```

Without a number (`col`), columns auto-distribute equally.

---

## 4. What are the Bootstrap breakpoints?

**Answer:**
Breakpoints are the predefined screen widths where the layout changes:

| Breakpoint | Class prefix | Width |
|-----------|-------------|-------|
| Extra small | `col-` | < 576px (default, mobile) |
| Small | `col-sm-` | ≥ 576px |
| Medium | `col-md-` | ≥ 768px (tablet) |
| Large | `col-lg-` | ≥ 992px (laptop) |
| Extra large | `col-xl-` | ≥ 1200px (desktop) |
| XXL | `col-xxl-` | ≥ 1400px (large desktop) |

Bootstrap is **mobile-first**, meaning styles are applied from the smallest breakpoint upward. `col-md-6` means "6 columns wide from medium screens and up" — below medium, it's full width (12 columns).

---

## 5. What is the difference between `container`, `container-fluid`, and `container-{breakpoint}`?

**Answer:**
- **`container`** — has a fixed `max-width` at each breakpoint. It's centered with auto margins. Good for most pages.
- **`container-fluid`** — always 100% wide regardless of screen size. Spans edge to edge. Good for full-width layouts.
- **`container-{breakpoint}`** (e.g., `container-md`) — 100% wide until the specified breakpoint, then fixed width. Hybrid approach.

Most of the time I use `container` for standard pages and `container-fluid` for dashboards or full-bleed sections.

---

## 6. What are Bootstrap utility classes? Name some common ones.

**Answer:**
Utility classes are single-purpose CSS classes that do one thing. They eliminate the need for custom CSS for common tasks:

**Spacing** (margin/padding):
- `m-3` — margin all sides (3 = 1rem)
- `mt-2` — margin-top
- `px-4` — padding left and right
- `mb-0` — margin-bottom 0
- Scale: 0, 1, 2, 3, 4, 5, auto

**Text:**
- `text-center`, `text-start`, `text-end`
- `fw-bold`, `fw-light` (font weight)
- `fs-1` to `fs-6` (font sizes)
- `text-uppercase`, `text-lowercase`

**Display:**
- `d-none` — hide
- `d-block`, `d-flex`, `d-grid`
- `d-md-none` — hide on medium and up

**Flexbox:**
- `d-flex`, `justify-content-center`, `align-items-center`
- `flex-wrap`, `flex-column`

**Colors:**
- `text-primary`, `text-danger`, `text-success`
- `bg-primary`, `bg-dark`, `bg-light`

The naming convention is consistent: `{property}-{breakpoint}-{value}`.

---

## 7. How do you create a responsive navigation bar in Bootstrap?

**Answer:**
```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container">
        <a class="navbar-brand" href="#">MyApp</a>
        <button class="navbar-toggler" type="button" 
                data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                <li class="nav-item">
                    <a class="nav-link active" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">About</a>
                </li>
            </ul>
        </div>
    </div>
</nav>
```

Key parts:
- `navbar-expand-lg` — collapses into a hamburger menu below `lg` (992px)
- `navbar-toggler` — the hamburger button (visible on small screens)
- `data-bs-toggle="collapse"` — connects the button to the collapsible content
- `ms-auto` — pushes nav links to the right
- `navbar-dark bg-dark` — dark background with light text

---

## 8. How do you create a modal in Bootstrap?

**Answer:**
A modal is a popup dialog that overlays the page:

```html
<!-- Trigger Button -->
<button class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#myModal">
    Open Modal
</button>

<!-- Modal -->
<div class="modal fade" id="myModal">
    <div class="modal-dialog">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Title</h5>
                <button class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
                <p>Modal content goes here.</p>
            </div>
            <div class="modal-footer">
                <button class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button class="btn btn-primary">Save</button>
            </div>
        </div>
    </div>
</div>
```

No JavaScript needed for basic functionality — Bootstrap handles it with `data-bs-*` attributes. For programmatic control, I can use:
```javascript
const modal = new bootstrap.Modal(document.getElementById('myModal'));
modal.show();
modal.hide();
```

---

## 9. What are Bootstrap cards? How do you use them?

**Answer:**
Cards are flexible content containers with header, body, footer, and image areas:

```html
<div class="card" style="width: 18rem;">
    <img src="image.jpg" class="card-img-top" alt="...">
    <div class="card-body">
        <h5 class="card-title">Card Title</h5>
        <p class="card-text">Some description text.</p>
        <a href="#" class="btn btn-primary">Read More</a>
    </div>
</div>
```

For a responsive grid of cards, I combine them with the grid:
```html
<div class="row g-3">
    <div class="col-md-4"><div class="card">...</div></div>
    <div class="col-md-4"><div class="card">...</div></div>
    <div class="col-md-4"><div class="card">...</div></div>
</div>
```

Cards replaced the old panels, wells, and thumbnails. `g-3` adds gutter spacing between cards.

---

## 10. What are Bootstrap form controls? How do you handle validation?

**Answer:**
Bootstrap provides styled form elements:

```html
<form class="needs-validation" novalidate>
    <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <input type="email" class="form-control" id="email" required>
        <div class="invalid-feedback">Please enter a valid email.</div>
    </div>
    <div class="mb-3">
        <label for="password" class="form-label">Password</label>
        <input type="password" class="form-control" id="password" 
               minlength="8" required>
        <div class="invalid-feedback">Password must be at least 8 characters.</div>
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

Key classes: `form-control` for inputs, `form-label` for labels, `form-select` for dropdowns, `form-check` for checkboxes/radios.

For validation, Bootstrap adds `.was-validated` to the form and shows `valid-feedback` or `invalid-feedback` messages based on HTML5 validation attributes.

---

## 11. What is the difference between Bootstrap 4 and Bootstrap 5?

**Answer:**
Major changes in Bootstrap 5:
1. **Dropped jQuery** — uses vanilla JavaScript. Smaller bundle, no jQuery dependency.
2. **New utility API** — easier to create custom utilities.
3. **Updated grid** — new `xxl` breakpoint (1400px), new `g-*` gutter classes.
4. **RTL support** — right-to-left language support built in.
5. **New components** — offcanvas, floating labels, accordions improved.
6. **Data attributes changed** — `data-toggle` → `data-bs-toggle` (prefixed with `bs`).
7. **Dropped IE support** — focus on modern browsers.
8. **CSS custom properties** — uses CSS variables for easier theming.

If I'm starting a new project, I always use Bootstrap 5.

---

## 12. How do you customize Bootstrap's default styles?

**Answer:**
Several approaches:

1. **Override with custom CSS** (simplest) — write CSS after Bootstrap's link:
```css
.btn-primary {
    background-color: #663399;
    border-color: #663399;
}
```

2. **Sass variables** (recommended for projects with a build process):
```scss
// Override variables BEFORE importing Bootstrap
$primary: #663399;
$font-family-base: 'Inter', sans-serif;
$border-radius: 0.5rem;

@import "bootstrap/scss/bootstrap";
```

3. **Utility API** — add custom utilities.

4. **Custom class prefixed** — create a class like `.custom-card` that extends Bootstrap styles.

I prefer the Sass approach for projects because it's clean, maintainable, and catches design system changes in one place.

---

## 13. What are the `data-bs-*` attributes in Bootstrap?

**Answer:**
`data-bs-*` attributes are Bootstrap's way of adding interactive behavior to HTML elements **without writing JavaScript**. They're declarative — I just add attributes and Bootstrap handles the rest.

Common examples:
- `data-bs-toggle="modal"` → toggles a modal
- `data-bs-toggle="collapse"` → toggles a collapsible element
- `data-bs-toggle="dropdown"` → toggles a dropdown menu
- `data-bs-toggle="tab"` → toggles tabs
- `data-bs-target="#myElement"` → specifies which element to target
- `data-bs-dismiss="modal"` → closes a modal
- `data-bs-dismiss="alert"` → closes an alert

The `bs` prefix was added in Bootstrap 5 to avoid conflicts with other libraries. In Bootstrap 4, it was just `data-toggle`.

---

## 14. What is a Bootstrap Accordion? When would you use it?

**Answer:**
An accordion shows/hides content sections — only one section is open at a time:

```html
<div class="accordion" id="myAccordion">
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button" data-bs-toggle="collapse" 
                    data-bs-target="#collapseOne">
                Section 1
            </button>
        </h2>
        <div id="collapseOne" class="accordion-collapse collapse show" 
             data-bs-parent="#myAccordion">
            <div class="accordion-body">
                Content for section 1.
            </div>
        </div>
    </div>
    <!-- More items... -->
</div>
```

`data-bs-parent="#myAccordion"` ensures only one item is open at a time (the others close automatically).

I use accordions for: FAQ pages, settings panels, documentation with expandable sections, and mobile-friendly content where screen space is limited.

---

## 15. How do you make an image responsive in Bootstrap?

**Answer:**
Simple — add the `img-fluid` class:

```html
<img src="photo.jpg" class="img-fluid" alt="Responsive image">
```

`img-fluid` applies `max-width: 100%` and `height: auto`, so the image scales down with its container but never stretches beyond its original size.

Other image utilities:
- `rounded` — rounded corners
- `rounded-circle` — circular image
- `img-thumbnail` — adds a border frame
- `float-start` / `float-end` — float image left/right

For responsive background images, I'd use CSS instead of HTML.

---

## 16. What are Bootstrap alerts and toasts? What's the difference?

**Answer:**
**Alerts** are static notification messages:
```html
<div class="alert alert-success alert-dismissible fade show">
    Profile updated successfully!
    <button class="btn-close" data-bs-dismiss="alert"></button>
</div>
```
Alerts sit inline with the content and stay until dismissed. Variants: `alert-primary`, `alert-danger`, `alert-warning`, `alert-info`, etc.

**Toasts** are floating notification popups:
```html
<div class="toast" id="myToast">
    <div class="toast-header">
        <strong>Notification</strong>
        <button class="btn-close" data-bs-dismiss="toast"></button>
    </div>
    <div class="toast-body">Message sent successfully.</div>
</div>
```
Toasts are positioned absolutely (usually top-right), auto-hide after a delay, and stack on top of each other. They need to be triggered via JavaScript: `new bootstrap.Toast(element).show()`.

Use alerts for important page-level messages. Use toasts for lightweight, temporary notifications.

---

## 17. How do you handle spacing in Bootstrap?

**Answer:**
Bootstrap uses a spacing utility system with the format: `{property}{sides}-{breakpoint}-{size}`

**Property:** `m` (margin) or `p` (padding)

**Sides:**
- `t` = top, `b` = bottom, `s` = start (left), `e` = end (right)
- `x` = left + right, `y` = top + bottom
- blank = all sides

**Size:** 0 through 5, or `auto`
- 0 = 0
- 1 = 0.25rem (4px)
- 2 = 0.5rem (8px)
- 3 = 1rem (16px) — default spacing
- 4 = 1.5rem (24px)
- 5 = 3rem (48px)

Examples:
- `mt-3` → margin-top: 1rem
- `px-4` → padding-left AND padding-right: 1.5rem
- `mb-md-0` → margin-bottom: 0 on medium screens and up
- `mx-auto` → center horizontally

---

## 18. What is the `d-*` utility class in Bootstrap?

**Answer:**
The `d-*` classes control the CSS `display` property. They're essential for showing/hiding elements responsively:

- `d-none` → `display: none` (hide)
- `d-block` → `display: block`
- `d-inline` → `display: inline`
- `d-flex` → `display: flex`
- `d-grid` → `display: grid`

Responsive pattern: `d-{breakpoint}-{value}`

For example, to **hide on mobile, show on desktop**:
```html
<div class="d-none d-md-block">Visible only on medium and up</div>
```

To **show on mobile, hide on desktop**:
```html
<div class="d-block d-md-none">Visible only on small screens</div>
```

---

## 19. What are Bootstrap tables? How do you make them responsive?

**Answer:**
Bootstrap provides styled table classes:

```html
<div class="table-responsive">
    <table class="table table-striped table-hover table-bordered">
        <thead class="table-dark">
            <tr>
                <th>Name</th>
                <th>Email</th>
                <th>Role</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>Kunal</td>
                <td>kunal@test.com</td>
                <td>Developer</td>
            </tr>
        </tbody>
    </table>
</div>
```

Key classes:
- `table` — base styling
- `table-striped` — alternating row colors
- `table-hover` — highlight on hover
- `table-bordered` — borders on all cells
- `table-dark` — dark theme
- `table-responsive` — wrapper that adds horizontal scroll on small screens

---

## 20. How would you build a responsive landing page with Bootstrap?

**Answer:**
I'd structure it with these components:

```html
<!-- Navbar -->
<nav class="navbar navbar-expand-lg navbar-dark bg-dark fixed-top">...</nav>

<!-- Hero Section -->
<section class="py-5 text-center bg-primary text-white">
    <div class="container">
        <h1 class="display-4">Welcome</h1>
        <p class="lead">Description</p>
        <a href="#" class="btn btn-light btn-lg">Get Started</a>
    </div>
</section>

<!-- Features Section -->
<section class="py-5">
    <div class="container">
        <div class="row g-4">
            <div class="col-md-4">
                <div class="card h-100">...</div>
            </div>
            <!-- 2 more cards -->
        </div>
    </div>
</section>

<!-- Footer -->
<footer class="bg-dark text-white py-4">
    <div class="container text-center">
        <p>&copy; 2026 MyApp</p>
    </div>
</footer>
```

Key techniques:
- Grid for responsive layout
- `py-5` for vertical section spacing
- `h-100` on cards for equal heights
- `fixed-top` for sticky navbar
- Responsive text classes for different screen sizes

This gives me a fully responsive page with minimal custom CSS, built entirely with Bootstrap classes.
