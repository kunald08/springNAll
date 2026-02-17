# Bootstrap 5 — From Basics to Expert

---

## 1. Introduction to Bootstrap 5

### What is Bootstrap?

Bootstrap is the **most popular CSS framework** for building responsive, mobile-first websites. It was created by Twitter engineers (Mark Otto and Jacob Thornton) and is used by millions of websites worldwide.

Here's the problem Bootstrap solves: building a professional-looking website from scratch requires writing hundreds of lines of CSS for buttons, forms, navigation bars, grids, modals, and more. Bootstrap gives you all of this **pre-built**. Instead of spending hours writing CSS for a button, you just add `class="btn btn-primary"` to your HTML and you instantly get a polished, styled button. Instead of figuring out responsive layouts from scratch, you use Bootstrap’s grid system which handles it automatically.

Bootstrap provides:
- Pre-built CSS classes for layout, typography, forms, buttons
- Responsive grid system
- Ready-to-use components (navbar, modals, cards)
- JavaScript plugins (dropdowns, carousels, tooltips)

### Under the Hood: How Bootstrap Works

```
Bootstrap = Pre-written CSS + JavaScript

You don't write:                    You write:
┌──────────────────────────┐       ┌──────────────────────────┐
│ .my-button {             │       │ <button class="btn       │
│   display: inline-block; │       │   btn-primary btn-lg">   │
│   font-weight: 400;      │  →    │   Click Me               │
│   text-align: center;    │       │ </button>                │
│   padding: .375rem .75rem│       │                          │
│   border-radius: .375rem;│       │ (Bootstrap CSS handles   │
│   background: #0d6efd;  │       │  all the styling)        │
│   color: white;          │       │                          │
│ }                        │       │                          │
└──────────────────────────┘       └──────────────────────────┘

Bootstrap 5 changes from Bootstrap 4:
  - Dropped jQuery dependency (pure vanilla JS)
  - Dropped IE support
  - New utility API
  - RTL (Right-to-Left) support
  - New components (offcanvas, accordion)
  - Updated forms
```

---

## 2. Bootstrap Setup (CDN)

To use Bootstrap, you need to include its CSS and JavaScript files in your HTML page. The easiest way is via a **CDN** (Content Delivery Network) — you just add two links to your HTML and Bootstrap is ready to use. No downloads, no installations, no build tools. The CDN serves the files from servers around the world, so the user's browser loads them from the nearest server.

For more advanced setups (like customizing Bootstrap's colors or using a build tool), you can install it via npm. But for learning and quick projects, CDN is the simplest approach.

### Method 1: CDN (Quick Start — No Installation)

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bootstrap Page</title>
    
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" 
          rel="stylesheet">
</head>
<body>

    <h1 class="text-center text-primary mt-5">Hello Bootstrap!</h1>
    <button class="btn btn-success">Click Me</button>

    <!-- Bootstrap JS (includes Popper.js for dropdowns/tooltips) -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js">
    </script>
</body>
</html>
```

### Method 2: npm (For Build Tools)

```bash
npm install bootstrap@5.3.0
```

```javascript
// In your JS file
import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap/dist/js/bootstrap.bundle.min.js';
```

---

## 3. Layout and Grid System

The grid system is the **backbone of Bootstrap**. It's how you create page layouts — sidebars, multi-column content, card grids, etc. The core idea is simple: every row is divided into **12 equal columns**. You decide how many of those 12 columns each piece of content should occupy.

Want two equal halves? Give each `col-6` (6+6=12). Want a sidebar and main content? Give the sidebar `col-3` and the content `col-9` (3+9=12). The numbers must always add up to 12 (or less).

Bootstrap uses **Flexbox** behind the scenes to power the grid. You always follow the pattern: `container` > `row` > `col`. The **container** sets the maximum width and centers the content. The **row** creates a horizontal group. The **columns** divide the space.

### Under the Hood: The Grid System

Bootstrap uses a **12-column grid system** with Flexbox under the hood:

```
Container
└── Row (display: flex)
    ├── Col (flex item)
    ├── Col (flex item)
    └── Col (flex item)

12 columns:
|  1  |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  | 10  | 11  | 12  |

col-6 + col-6 = 12 (two equal halves)
|           6           |           6           |

col-4 + col-4 + col-4 = 12 (three equal thirds)
|       4       |       4       |       4       |

col-3 + col-9 = 12 (sidebar + main content)
|   3   |                 9                 |
```

### Container, Row, Column

```html
<!-- Containers -->
<div class="container">        <!-- Fixed-width, centered, max-width at each breakpoint -->
<div class="container-fluid">  <!-- Full-width, always 100% -->
<div class="container-md">     <!-- 100% until md breakpoint, then fixed -->

<!-- Basic Grid -->
<div class="container">
    <div class="row">
        <div class="col-4">Column 1 (4/12 = 33%)</div>
        <div class="col-4">Column 2 (4/12 = 33%)</div>
        <div class="col-4">Column 3 (4/12 = 33%)</div>
    </div>
</div>

<!-- Equal-width columns (auto) -->
<div class="row">
    <div class="col">Equal</div>
    <div class="col">Equal</div>
    <div class="col">Equal</div>
</div>

<!-- Mixed widths -->
<div class="row">
    <div class="col-3">Sidebar (25%)</div>
    <div class="col-9">Main Content (75%)</div>
</div>

<!-- Nested grid -->
<div class="row">
    <div class="col-8">
        Main
        <div class="row">
            <div class="col-6">Nested 1</div>
            <div class="col-6">Nested 2</div>
        </div>
    </div>
    <div class="col-4">Sidebar</div>
</div>
```

---

## 4. Responsive Breakpoints

One of Bootstrap's biggest strengths is making websites **responsive** (look good on all screen sizes) with almost no effort. Bootstrap defines 6 screen size ranges called **breakpoints**. Each breakpoint has a short name (like `sm`, `md`, `lg`) that you add to class names.

The magic is that you can assign **different column widths at different screen sizes**. For example, `col-12 col-md-6` means "take full width on small screens (12/12), but take half width on medium screens and above (6/12)." This way, cards stack vertically on phones but sit side-by-side on tablets and desktops.

Bootstrap is **mobile-first**, meaning styles without a breakpoint infix apply to the smallest screens and up. Styles with `md` apply from 768px and up. So you start with mobile layout and add modifications for larger screens.

### Breakpoint System

| Breakpoint | Class infix | Min-width | Typical Device |
|------------|-------------|-----------|----------------|
| Extra small | (none) | `<576px` | Phones (portrait) |
| Small | `sm` | `≥576px` | Phones (landscape) |
| Medium | `md` | `≥768px` | Tablets |
| Large | `lg` | `≥992px` | Desktops |
| Extra large | `xl` | `≥1200px` | Large desktops |
| XXL | `xxl` | `≥1400px` | Extra large desktops |

### Responsive Grid

```html
<!-- Stack on mobile, side-by-side on medium+ -->
<div class="row">
    <div class="col-12 col-md-6">Left half (full on mobile)</div>
    <div class="col-12 col-md-6">Right half (full on mobile)</div>
</div>

<!-- Different layouts at different breakpoints -->
<div class="row">
    <div class="col-12 col-sm-6 col-lg-3">
        <!-- 
        Mobile: 12/12 = full width (stacked)
        Small:  6/12 = half width (2 per row)
        Large:  3/12 = quarter width (4 per row)
        -->
        Card 1
    </div>
    <div class="col-12 col-sm-6 col-lg-3">Card 2</div>
    <div class="col-12 col-sm-6 col-lg-3">Card 3</div>
    <div class="col-12 col-sm-6 col-lg-3">Card 4</div>
</div>

<!-- Offsetting columns -->
<div class="row">
    <div class="col-md-6 offset-md-3">Centered column</div>
</div>

<!-- Row columns (quick equal columns) -->
<div class="row row-cols-1 row-cols-md-2 row-cols-lg-4 g-4">
    <div class="col"><div class="card">Card 1</div></div>
    <div class="col"><div class="card">Card 2</div></div>
    <div class="col"><div class="card">Card 3</div></div>
    <div class="col"><div class="card">Card 4</div></div>
</div>

<!-- Gutters (spacing between columns) -->
<div class="row g-3">     <!-- g-0 to g-5 -->
    <div class="col">Column</div>
    <div class="col">Column</div>
</div>
<div class="row gx-5">    <!-- Horizontal gutter only -->
<div class="row gy-3">    <!-- Vertical gutter only -->
```

---

## 5. Essential Components

Bootstrap comes packed with pre-built UI components that would take hours to build from scratch. These include styled **typography** (headings, paragraphs, text utilities), **buttons** (in various colors, sizes, and states), **cards** (boxed content containers with images, text, and actions), and **forms** (styled inputs, selects, checkboxes, and validation states).

The beauty of these components is consistency — they all follow the same design language (colors, spacing, borders), so your site looks polished and professional. Each component is controlled through CSS classes — you rarely need to write custom CSS.

### Typography

Bootstrap enhances default browser typography with better fonts, consistent spacing, and utility classes for text alignment, weight, transform (uppercase, lowercase), and colors. You get 8 contextual colors (`text-primary`, `text-danger`, etc.) that you'll use everywhere. The same color scheme applies to backgrounds (`bg-primary`, `bg-danger`, etc.).

```html
<!-- Headings -->
<h1>h1 heading</h1>
<p class="h1">Paragraph styled as h1</p>

<!-- Display headings (larger, thinner) -->
<h1 class="display-1">Display 1</h1>
<h1 class="display-4">Display 4</h1>

<!-- Lead paragraph -->
<p class="lead">This is a lead paragraph — larger and lighter.</p>

<!-- Text utilities -->
<p class="text-start">Left aligned</p>
<p class="text-center">Center aligned</p>
<p class="text-end">Right aligned</p>
<p class="fw-bold">Bold text</p>
<p class="fw-light">Light text</p>
<p class="fst-italic">Italic text</p>
<p class="text-decoration-underline">Underlined</p>
<p class="text-decoration-line-through">Strikethrough</p>
<p class="text-uppercase">uppercase</p>
<p class="text-lowercase">LOWERCASE</p>
<p class="text-capitalize">capitalize each word</p>
<p class="text-truncate" style="max-width: 200px;">This long text will be truncated...</p>

<!-- Colors -->
<p class="text-primary">Primary (blue)</p>
<p class="text-secondary">Secondary (gray)</p>
<p class="text-success">Success (green)</p>
<p class="text-danger">Danger (red)</p>
<p class="text-warning">Warning (yellow)</p>
<p class="text-info">Info (cyan)</p>
<p class="text-muted">Muted (light gray)</p>

<!-- Background colors -->
<div class="bg-primary text-white p-3">Primary background</div>
<div class="bg-danger text-white p-3">Danger background</div>
<div class="bg-light text-dark p-3">Light background</div>
```

### Buttons

Bootstrap buttons are some of the most commonly used components. Just add `class="btn btn-primary"` to any `<button>` or `<a>` element and you get a fully styled button with hover effects, focus styles, and keyboard accessibility built in. Buttons come in 8 color variants (`btn-primary`, `btn-danger`, `btn-success`, etc.), **outline** versions for a lighter look, and 3 sizes (`btn-sm`, default, `btn-lg`). You can also create full-width buttons, button groups, and loading state buttons with spinners.

```html
<!-- Button styles -->
<button class="btn btn-primary">Primary</button>
<button class="btn btn-secondary">Secondary</button>
<button class="btn btn-success">Success</button>
<button class="btn btn-danger">Danger</button>
<button class="btn btn-warning">Warning</button>
<button class="btn btn-info">Info</button>
<button class="btn btn-light">Light</button>
<button class="btn btn-dark">Dark</button>
<button class="btn btn-link">Link</button>

<!-- Outline buttons -->
<button class="btn btn-outline-primary">Outline Primary</button>
<button class="btn btn-outline-danger">Outline Danger</button>

<!-- Sizes -->
<button class="btn btn-primary btn-lg">Large</button>
<button class="btn btn-primary">Default</button>
<button class="btn btn-primary btn-sm">Small</button>

<!-- Block button (full width) -->
<div class="d-grid gap-2">
    <button class="btn btn-primary">Full Width Button</button>
</div>

<!-- Button group -->
<div class="btn-group" role="group">
    <button class="btn btn-primary">Left</button>
    <button class="btn btn-primary">Middle</button>
    <button class="btn btn-primary">Right</button>
</div>

<!-- States -->
<button class="btn btn-primary" disabled>Disabled</button>
<button class="btn btn-primary active">Active</button>

<!-- Loading spinner button -->
<button class="btn btn-primary" disabled>
    <span class="spinner-border spinner-border-sm"></span>
    Loading...
</button>
```

### Cards

Cards are one of Bootstrap's most versatile components — they're **flexible content containers** with a border and padding. Think of them like the cards you see on every modern website: product cards on Amazon, post cards on social media, feature cards on landing pages. A card can contain an image at the top (`card-img-top`), a body section with title and text (`card-body`), a header, a footer, and even lists. Cards are perfect for displaying repeated, similarly-structured content in a grid layout.

```html
<!-- Basic card -->
<div class="card" style="width: 18rem;">
    <img src="image.jpg" class="card-img-top" alt="Card image">
    <div class="card-body">
        <h5 class="card-title">Card Title</h5>
        <h6 class="card-subtitle mb-2 text-muted">Subtitle</h6>
        <p class="card-text">Some quick example text for the card body.</p>
        <a href="#" class="btn btn-primary">Go somewhere</a>
    </div>
</div>

<!-- Card with list -->
<div class="card">
    <div class="card-header">Featured</div>
    <ul class="list-group list-group-flush">
        <li class="list-group-item">Item 1</li>
        <li class="list-group-item">Item 2</li>
        <li class="list-group-item">Item 3</li>
    </ul>
    <div class="card-footer text-muted">2 days ago</div>
</div>

<!-- Card grid -->
<div class="row row-cols-1 row-cols-md-3 g-4">
    <div class="col">
        <div class="card h-100">
            <div class="card-body">
                <h5 class="card-title">Card 1</h5>
                <p class="card-text">Content here...</p>
            </div>
        </div>
    </div>
    <div class="col">
        <div class="card h-100">
            <div class="card-body">
                <h5 class="card-title">Card 2</h5>
                <p class="card-text">Content here...</p>
            </div>
        </div>
    </div>
    <div class="col">
        <div class="card h-100">
            <div class="card-body">
                <h5 class="card-title">Card 3</h5>
                <p class="card-text">Content here...</p>
            </div>
        </div>
    </div>
</div>
```

### Forms

Bootstrap transforms plain HTML form elements into polished, styled inputs. Every input gets the `form-control` class which gives it consistent sizing, spacing, rounded corners, and focus ring. Checkboxes and radio buttons use `form-check`, toggles use `form-switch`, and dropdowns use `form-select`. Bootstrap also provides floating labels (the label moves up when you click the input), input groups (input with an icon or text addon), and built-in validation styling (green border for valid, red border for invalid with feedback messages).

```html
<form>
    <!-- Text input -->
    <div class="mb-3">
        <label for="name" class="form-label">Name</label>
        <input type="text" class="form-control" id="name" placeholder="Enter name">
    </div>

    <!-- Email -->
    <div class="mb-3">
        <label for="email" class="form-label">Email</label>
        <input type="email" class="form-control" id="email" placeholder="name@example.com">
        <div class="form-text">We'll never share your email.</div>
    </div>

    <!-- Password -->
    <div class="mb-3">
        <label for="password" class="form-label">Password</label>
        <input type="password" class="form-control" id="password">
    </div>

    <!-- Select -->
    <div class="mb-3">
        <label for="role" class="form-label">Role</label>
        <select class="form-select" id="role">
            <option selected>Choose...</option>
            <option value="admin">Admin</option>
            <option value="user">User</option>
            <option value="guest">Guest</option>
        </select>
    </div>

    <!-- Textarea -->
    <div class="mb-3">
        <label for="bio" class="form-label">Bio</label>
        <textarea class="form-control" id="bio" rows="3"></textarea>
    </div>

    <!-- Checkbox -->
    <div class="form-check mb-3">
        <input class="form-check-input" type="checkbox" id="terms">
        <label class="form-check-label" for="terms">Agree to terms</label>
    </div>

    <!-- Radio buttons -->
    <div class="mb-3">
        <div class="form-check">
            <input class="form-check-input" type="radio" name="plan" id="free" checked>
            <label class="form-check-label" for="free">Free</label>
        </div>
        <div class="form-check">
            <input class="form-check-input" type="radio" name="plan" id="pro">
            <label class="form-check-label" for="pro">Pro</label>
        </div>
    </div>

    <!-- Switch -->
    <div class="form-check form-switch mb-3">
        <input class="form-check-input" type="checkbox" id="notifications">
        <label class="form-check-label" for="notifications">Enable notifications</label>
    </div>

    <!-- File input -->
    <div class="mb-3">
        <label for="file" class="form-label">Upload file</label>
        <input class="form-control" type="file" id="file">
    </div>

    <!-- Input group (with addon) -->
    <div class="input-group mb-3">
        <span class="input-group-text">@</span>
        <input type="text" class="form-control" placeholder="Username">
    </div>

    <!-- Floating labels -->
    <div class="form-floating mb-3">
        <input type="email" class="form-control" id="floatingEmail" placeholder="name@example.com">
        <label for="floatingEmail">Email address</label>
    </div>

    <!-- Validation -->
    <div class="mb-3">
        <input type="text" class="form-control is-valid" value="Valid input">
        <div class="valid-feedback">Looks good!</div>
    </div>
    <div class="mb-3">
        <input type="text" class="form-control is-invalid">
        <div class="invalid-feedback">Please provide a value.</div>
    </div>

    <!-- Inline form -->
    <div class="row g-3 align-items-center mb-3">
        <div class="col-auto">
            <input type="text" class="form-control" placeholder="Search...">
        </div>
        <div class="col-auto">
            <button class="btn btn-primary">Search</button>
        </div>
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

---

## 6. Navbar, Modals, Dropdowns

These are Bootstrap's **interactive components** — they require JavaScript to work (Bootstrap's JS bundle is included via the CDN link). The **navbar** is a responsive navigation bar that automatically collapses into a hamburger menu on small screens. **Modals** are popup dialog boxes that overlay the page (used for confirmation dialogs, forms, image previews, etc.). **Dropdowns** are toggleable menus that appear when you click a button.

All of these work through **data attributes** — you add `data-bs-toggle` and `data-bs-target` attributes to your HTML elements, and Bootstrap’s JavaScript handles the behavior automatically. No JavaScript coding needed on your part.

### Navbar

The navbar is one of Bootstrap’s most complex components because it handles so many things: brand/logo, navigation links, search forms, dropdowns, and responsive collapsing. The key class is `navbar-expand-lg` which means "show the full nav on large screens and above, collapse it into a hamburger menu on smaller screens."

```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark">
    <div class="container-fluid">
        <!-- Brand -->
        <a class="navbar-brand" href="#">MyApp</a>
        
        <!-- Hamburger toggle (mobile) -->
        <button class="navbar-toggler" type="button" 
                data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
        </button>
        
        <!-- Collapsible content -->
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav me-auto">
                <li class="nav-item">
                    <a class="nav-link active" href="#">Home</a>
                </li>
                <li class="nav-item">
                    <a class="nav-link" href="#">Features</a>
                </li>
                <li class="nav-item dropdown">
                    <a class="nav-link dropdown-toggle" href="#" 
                       data-bs-toggle="dropdown">Services</a>
                    <ul class="dropdown-menu">
                        <li><a class="dropdown-item" href="#">Web Design</a></li>
                        <li><a class="dropdown-item" href="#">Development</a></li>
                        <li><hr class="dropdown-divider"></li>
                        <li><a class="dropdown-item" href="#">Consulting</a></li>
                    </ul>
                </li>
            </ul>
            
            <!-- Search form -->
            <form class="d-flex" role="search">
                <input class="form-control me-2" type="search" placeholder="Search">
                <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
        </div>
    </div>
</nav>

<!-- Navbar variants -->
<!-- navbar-light bg-light  → Light background with dark text -->
<!-- navbar-dark bg-dark    → Dark background with light text -->
<!-- navbar-dark bg-primary → Colored background -->
<!-- fixed-top              → Fixed to top of viewport -->
<!-- sticky-top             → Sticky on scroll -->
```

### Modal

A modal is a **popup dialog** that appears on top of the page content. It darkens the background and focuses the user’s attention on the modal content. Modals are used for confirmations ("Are you sure you want to delete?"), forms (login or registration without navigating away), displaying detailed information, or showing large images. The modal is triggered by a button with `data-bs-toggle="modal"` and `data-bs-target="#myModal"`.

```html
<!-- Trigger button -->
<button type="button" class="btn btn-primary" 
        data-bs-toggle="modal" data-bs-target="#myModal">
    Open Modal
</button>

<!-- Modal -->
<div class="modal fade" id="myModal" tabindex="-1">
    <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title">Modal Title</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"></button>
            </div>
            <div class="modal-body">
                <p>Modal body text goes here.</p>
            </div>
            <div class="modal-footer">
                <button class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
                <button class="btn btn-primary">Save changes</button>
            </div>
        </div>
    </div>
</div>

<!-- Modal sizes: modal-sm, modal-lg, modal-xl, modal-fullscreen -->
<!-- Scrollable: modal-dialog-scrollable -->

<!-- Open modal via JavaScript -->
<script>
    const modal = new bootstrap.Modal(document.getElementById('myModal'));
    modal.show();
    modal.hide();
</script>
```

### Dropdown

Dropdowns are **toggleable menus** that appear below (or above, or beside) a button when clicked. They're commonly used for navigation menus, user account menus, and action menus. Items in a dropdown can be links, buttons, headers, or dividers. Dropdowns close automatically when you click outside of them.

```html
<!-- Basic dropdown -->
<div class="dropdown">
    <button class="btn btn-secondary dropdown-toggle" data-bs-toggle="dropdown">
        Dropdown
    </button>
    <ul class="dropdown-menu">
        <li><h6 class="dropdown-header">Section</h6></li>
        <li><a class="dropdown-item" href="#">Action</a></li>
        <li><a class="dropdown-item active" href="#">Active</a></li>
        <li><a class="dropdown-item disabled" href="#">Disabled</a></li>
        <li><hr class="dropdown-divider"></li>
        <li><a class="dropdown-item" href="#">Separated link</a></li>
    </ul>
</div>

<!-- Directions: dropup, dropend, dropstart -->
<div class="dropup">...</div>
```

---

## 7. Bootstrap Utilities and Helpers

Utilities are Bootstrap’s **single-purpose helper classes**. Instead of writing custom CSS for every little thing, you use utility classes directly in your HTML. Need margin on top? Add `mt-3`. Need to center text? Add `text-center`. Need to hide something on mobile? Add `d-none d-md-block`.

This approach (called **utility-first CSS**) is incredibly fast for development — you can style almost anything without writing a single line of custom CSS. Once you learn the naming pattern, you can guess what any utility class does.

### Spacing

Spacing utilities control **margin** (space outside an element) and **padding** (space inside an element). The naming pattern is: `{property}{sides}-{size}`. `m` = margin, `p` = padding. `t` = top, `b` = bottom, `s` = start (left), `e` = end (right), `x` = horizontal, `y` = vertical, blank = all sides. Sizes range from `0` (no space) to `5` (3rem). So `mt-3` means "margin-top: 1rem" and `px-4` means "padding-left and padding-right: 1.5rem."

```html
<!-- 
Spacing utilities: {property}{sides}-{size}
Property: m (margin), p (padding)
Sides: t(top), b(bottom), s(start/left), e(end/right), x(horizontal), y(vertical), blank(all)
Size: 0, 1(0.25rem), 2(0.5rem), 3(1rem), 4(1.5rem), 5(3rem), auto
-->

<div class="mt-3">Margin top 1rem</div>
<div class="mb-5">Margin bottom 3rem</div>
<div class="mx-auto" style="width: 200px;">Centered</div>
<div class="p-4">Padding all sides 1.5rem</div>
<div class="px-3 py-2">Horizontal padding 1rem, vertical 0.5rem</div>
```

### Display

Display utilities control the CSS `display` property. The most common ones are `d-none` (hide element), `d-block` (show as block), `d-flex` (make it a flex container), and `d-grid` (make it a grid container). These become especially powerful with **responsive variants** — `d-none d-md-block` means "hidden on small screens, visible on medium and above." This is the easiest way to show/hide content based on screen size.

```html
<div class="d-none">Hidden</div>
<div class="d-block">Block</div>
<div class="d-inline">Inline</div>
<div class="d-flex">Flex container</div>
<div class="d-grid">Grid container</div>

<!-- Responsive display -->
<div class="d-none d-md-block">Hidden on mobile, visible on md+</div>
<div class="d-block d-lg-none">Visible on mobile, hidden on lg+</div>
```

### Flexbox Utilities

Bootstrap exposes all of Flexbox’s power through utility classes, so you don’t need to write any custom CSS. `d-flex` creates a flex container. `justify-content-between` pushes items to the edges. `align-items-center` vertically centers items. These are the most common — used for navbars, card layouts, centering, and any horizontal arrangement of items.

```html
<div class="d-flex justify-content-between align-items-center">
    <span>Left</span>
    <span>Right</span>
</div>

<div class="d-flex flex-column">
    <div>Item 1</div>
    <div>Item 2</div>
</div>

<!-- 
justify-content-{start|end|center|between|around|evenly}
align-items-{start|end|center|baseline|stretch}
flex-{row|column|row-reverse|column-reverse}
flex-wrap
flex-grow-1
order-{0-5}
gap-{0-5}
-->
```

### Sizing

Sizing utilities set the width or height of an element as a percentage. `w-50` makes the element 50% of its parent’s width. `vh-100` makes it 100% of the viewport height (useful for full-screen sections). These are shorthand for common CSS width/height rules.

```html
<div class="w-25">Width 25%</div>
<div class="w-50">Width 50%</div>
<div class="w-75">Width 75%</div>
<div class="w-100">Width 100%</div>
<div class="h-100">Height 100%</div>
<div class="mw-100">Max-width 100%</div>
<div class="vh-100">100% viewport height</div>
```

### Borders and Rounded

```html
<div class="border">All borders</div>
<div class="border-top border-primary">Top border, primary color</div>
<div class="border-0">No border</div>
<div class="rounded">Rounded corners</div>
<div class="rounded-circle">Circle</div>
<div class="rounded-pill">Pill shape</div>
<div class="shadow">Shadow</div>
<div class="shadow-sm">Small shadow</div>
<div class="shadow-lg">Large shadow</div>
```

### Other Utilities

Bootstrap also provides utilities for borders, rounded corners, shadows, visibility, positioning, floating, and opacity. These cover nearly every common CSS property you’d need, making it possible to build entire layouts without a single custom CSS rule.

```html
<!-- Visibility -->
<div class="visible">Visible</div>
<div class="invisible">Invisible (still takes space)</div>

<!-- Position -->
<div class="position-relative">
    <div class="position-absolute top-0 end-0">Top right corner</div>
</div>
<div class="fixed-top">Fixed top</div>
<div class="sticky-top">Sticky top</div>

<!-- Overflow -->
<div class="overflow-auto" style="height: 100px;">Scrollable content...</div>
<div class="overflow-hidden">Hidden overflow</div>

<!-- Float -->
<div class="float-start">Float left</div>
<div class="float-end">Float right</div>
<div class="clearfix">Clear floats</div>

<!-- Opacity -->
<div class="opacity-25">25% opacity</div>
<div class="opacity-50">50% opacity</div>
<div class="opacity-75">75% opacity</div>
<div class="opacity-100">Full opacity</div>
```

---

## 8. Responsive Design with Bootstrap

This is where everything comes together. A real Bootstrap page combines the grid system (for layout), components (for UI elements), utilities (for fine-tuning), and responsive breakpoints (for adapting to different screens). The example below shows a complete responsive page with a sticky navbar, hero section, feature cards, and footer — all built with zero custom CSS.

Notice how the cards use `row-cols-1 row-cols-md-2 row-cols-lg-3` — this means 1 card per row on mobile, 2 per row on tablets, and 3 per row on desktops. The navbar uses `navbar-expand-lg` to collapse into a hamburger on smaller screens.

### Complete Responsive Page Example

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive Page</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" 
          rel="stylesheet">
</head>
<body>

    <!-- Responsive Navbar -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark sticky-top">
        <div class="container">
            <a class="navbar-brand" href="#">Brand</a>
            <button class="navbar-toggler" data-bs-toggle="collapse" data-bs-target="#nav">
                <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="nav">
                <ul class="navbar-nav ms-auto">
                    <li class="nav-item"><a class="nav-link" href="#">Home</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">About</a></li>
                    <li class="nav-item"><a class="nav-link" href="#">Contact</a></li>
                </ul>
            </div>
        </div>
    </nav>

    <!-- Hero Section -->
    <section class="bg-primary text-white text-center py-5">
        <div class="container">
            <h1 class="display-4 fw-bold">Welcome to Our Site</h1>
            <p class="lead mb-4">Building amazing responsive websites with Bootstrap 5</p>
            <a href="#" class="btn btn-light btn-lg">Get Started</a>
        </div>
    </section>

    <!-- Feature Cards -->
    <section class="container my-5">
        <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
            <div class="col">
                <div class="card h-100 shadow-sm">
                    <div class="card-body text-center">
                        <h5 class="card-title">Feature 1</h5>
                        <p class="card-text">Description of feature 1.</p>
                        <a href="#" class="btn btn-outline-primary">Learn More</a>
                    </div>
                </div>
            </div>
            <div class="col">
                <div class="card h-100 shadow-sm">
                    <div class="card-body text-center">
                        <h5 class="card-title">Feature 2</h5>
                        <p class="card-text">Description of feature 2.</p>
                        <a href="#" class="btn btn-outline-primary">Learn More</a>
                    </div>
                </div>
            </div>
            <div class="col">
                <div class="card h-100 shadow-sm">
                    <div class="card-body text-center">
                        <h5 class="card-title">Feature 3</h5>
                        <p class="card-text">Description of feature 3.</p>
                        <a href="#" class="btn btn-outline-primary">Learn More</a>
                    </div>
                </div>
            </div>
        </div>
    </section>

    <!-- Footer -->
    <footer class="bg-dark text-white text-center py-4">
        <div class="container">
            <p class="mb-0">&copy; 2025 Brand. All rights reserved.</p>
        </div>
    </footer>

    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js">
    </script>
</body>
</html>
```

---

## 9. More Components Quick Reference

Bootstrap includes many more components beyond what we’ve covered in detail. Here are the most useful ones in quick-reference format:

- **Alerts**: Colored notification boxes for success, warning, error, or info messages. Can be dismissible (with an X button).
- **Badges**: Small colored labels attached to text or buttons (like notification counts).
- **Accordions**: Expandable/collapsible content sections (FAQ style). Only one section is open at a time.
- **Tooltips**: Small popups that appear on hover to explain something.
- **Toasts**: Non-blocking notification pop-ups that appear in a corner (like push notifications).
- **Pagination**: Page number navigation for multi-page content.
- **Breadcrumbs**: Navigation trail showing the current page location (Home > Products > Details).
- **Progress bars**: Visual indicators of completion percentage.

### Alerts

```html
<div class="alert alert-success alert-dismissible fade show" role="alert">
    <strong>Success!</strong> Operation completed.
    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
</div>
```

### Badges

```html
<span class="badge bg-primary">New</span>
<span class="badge rounded-pill bg-danger">99+</span>
<button class="btn btn-primary">Notifications <span class="badge bg-secondary">4</span></button>
```

### Accordion

```html
<div class="accordion" id="faq">
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button" data-bs-toggle="collapse" data-bs-target="#q1">
                Question 1?
            </button>
        </h2>
        <div id="q1" class="accordion-collapse collapse show" data-bs-parent="#faq">
            <div class="accordion-body">Answer 1...</div>
        </div>
    </div>
    <div class="accordion-item">
        <h2 class="accordion-header">
            <button class="accordion-button collapsed" data-bs-toggle="collapse" data-bs-target="#q2">
                Question 2?
            </button>
        </h2>
        <div id="q2" class="accordion-collapse collapse" data-bs-parent="#faq">
            <div class="accordion-body">Answer 2...</div>
        </div>
    </div>
</div>
```

### Tooltips & Toasts

```html
<!-- Tooltip (must initialize with JS) -->
<button data-bs-toggle="tooltip" title="Tooltip text!">Hover me</button>
<script>
    const tooltips = document.querySelectorAll('[data-bs-toggle="tooltip"]');
    tooltips.forEach(el => new bootstrap.Tooltip(el));
</script>

<!-- Toast notification -->
<div class="toast-container position-fixed bottom-0 end-0 p-3">
    <div class="toast" id="myToast">
        <div class="toast-header">
            <strong class="me-auto">Notification</strong>
            <small>Just now</small>
            <button class="btn-close" data-bs-dismiss="toast"></button>
        </div>
        <div class="toast-body">Hello! This is a toast message.</div>
    </div>
</div>
<script>
    const toast = new bootstrap.Toast(document.getElementById('myToast'));
    toast.show();
</script>
```

### Pagination, Breadcrumb, Progress

```html
<!-- Pagination -->
<nav>
    <ul class="pagination">
        <li class="page-item disabled"><a class="page-link" href="#">Previous</a></li>
        <li class="page-item active"><a class="page-link" href="#">1</a></li>
        <li class="page-item"><a class="page-link" href="#">2</a></li>
        <li class="page-item"><a class="page-link" href="#">3</a></li>
        <li class="page-item"><a class="page-link" href="#">Next</a></li>
    </ul>
</nav>

<!-- Breadcrumb -->
<nav>
    <ol class="breadcrumb">
        <li class="breadcrumb-item"><a href="#">Home</a></li>
        <li class="breadcrumb-item"><a href="#">Products</a></li>
        <li class="breadcrumb-item active">Details</li>
    </ol>
</nav>

<!-- Progress bar -->
<div class="progress">
    <div class="progress-bar bg-success" style="width: 75%">75%</div>
</div>
```

---

## 10. Quick Reference — Naming Pattern

```
Bootstrap class naming convention:
{component}-{modifier}                → btn-primary
{component}-{breakpoint}-{modifier}   → col-md-6
{property}{sides}-{breakpoint}-{size} → mt-lg-3

Colors: primary, secondary, success, danger, warning, info, light, dark
Sizes:  sm, md, lg, xl, xxl
Spacing: 0, 1, 2, 3, 4, 5, auto
```
