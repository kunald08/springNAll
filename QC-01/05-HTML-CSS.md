# HTML & CSS — From Basics to Expert

---

## 1. Getting Started with HTML

### What is HTML?

**HTML** (HyperText Markup Language) is the standard language for creating web pages. It defines the **structure and content** of a webpage using elements (tags).

### Under the Hood: How a Browser Renders HTML

```
HTML File                    Browser Rendering Pipeline
   │
   ▼
┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐    ┌──────────┐
│  Parse   │ →  │   DOM    │ →  │  Render  │ →  │  Layout  │ →  │  Paint   │
│  HTML    │    │  Tree    │    │  Tree    │    │ (Reflow) │    │ (Pixels) │
└──────────┘    └──────────┘    └──────────┘    └──────────┘    └──────────┘

1. Parse HTML → Build DOM (Document Object Model) tree
2. Parse CSS → Build CSSOM (CSS Object Model) tree
3. Combine DOM + CSSOM → Render Tree
4. Layout: Calculate position and size of each element
5. Paint: Draw pixels to the screen
```

---

## 2. HTML Document Structure

Every HTML page follows the same basic structure, like a skeleton. The `<!DOCTYPE html>` declaration at the very top tells the browser "this is an HTML5 document." Then the entire page lives inside `<html>` tags. Inside that, there are two main sections: `<head>` (invisible information about the page like the title, character encoding, and links to CSS files) and `<body>` (everything the user actually sees on the screen). Think of `<head>` as the label on a package (metadata) and `<body>` as what's inside the package (actual content).

```html
<!DOCTYPE html>          <!-- Tells browser: this is HTML5 -->
<html lang="en">         <!-- Root element, language attribute -->
<head>
    <!-- Metadata — not visible on page -->
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta name="description" content="My awesome webpage">
    <title>My Page Title</title>   <!-- Shows in browser tab -->
    <link rel="stylesheet" href="styles.css">
    <link rel="icon" href="favicon.ico">
</head>
<body>
    <!-- Visible content goes here -->
    <h1>Hello World</h1>
    <p>Welcome to my page.</p>
    
    <script src="app.js"></script>   <!-- JS at bottom for performance -->
</body>
</html>
```

### Under the Hood: The DOM Tree

```
Document
 └── html
      ├── head
      │    ├── meta (charset)
      │    ├── meta (viewport)
      │    ├── title
      │    │    └── "My Page Title"
      │    └── link (stylesheet)
      └── body
           ├── h1
           │    └── "Hello World"
           ├── p
           │    └── "Welcome to my page."
           └── script
```

Every element becomes a **node** in the DOM tree. JavaScript interacts with this tree to change content dynamically.

---

## 3. HTML5 New Features

HTML5 (released in 2014) was a major upgrade to the language. Before HTML5, you needed plugins like Flash for video, audio, and games in browsers. HTML5 made all of that native — you can now embed video and audio directly with simple tags like `<video>` and `<audio>`. It also introduced **semantic elements** (like `<header>`, `<nav>`, `<footer>`) that give meaning to your page structure instead of using meaningless `<div>` tags for everything. HTML5 also added new form input types, local storage, geolocation, and much more.

### What Changed in HTML5

| Feature | Description |
|---------|-------------|
| **Semantic Elements** | `<header>`, `<nav>`, `<article>`, `<section>`, `<footer>` |
| **Multimedia** | `<video>`, `<audio>` — no need for Flash |
| **Canvas & SVG** | Draw graphics directly in the browser |
| **Web Forms 2.0** | New input types: `date`, `email`, `range`, `color` |
| **Web Storage** | `localStorage`, `sessionStorage` |
| **Geolocation** | Access user's location |
| **Web Workers** | Background JavaScript threads |
| **WebSockets** | Real-time bidirectional communication |

---

## 4. Elements & Attributes

HTML is made up of **elements** (also called tags). An element tells the browser what type of content something is — is it a paragraph? A heading? An image? A link? Most elements have an opening tag (`<p>`) and a closing tag (`</p>`) with content in between. Some elements are **self-closing** (like `<img>` and `<br>`) because they don't have content inside them.

**Attributes** are extra information you add to elements. They go inside the opening tag and follow the pattern `name="value"`. For example, `<a href="https://google.com">` — here `href` is an attribute that tells the link where to go. Every element can have attributes like `id` (unique identifier), `class` (for styling), and `style` (inline CSS).

### Anatomy of an HTML Element

```html
<tagname attribute="value">Content</tagname>
  │         │          │       │        │
  │         │          │       │        └── Closing tag
  │         │          │       └── Content (text, other elements)
  │         │          └── Attribute value
  │         └── Attribute name
  └── Opening tag

<!-- Self-closing (void) elements — no closing tag needed -->
<img src="photo.jpg" alt="A photo">
<br>
<hr>
<input type="text">
<meta charset="UTF-8">
```

### Global Attributes (available on ALL elements)

```html
<div id="unique-id"           <!-- Unique identifier (one per page) -->
     class="highlight bold"   <!-- CSS class(es) for styling -->
     style="color: red;"      <!-- Inline CSS (avoid when possible) -->
     title="Tooltip text"     <!-- Hover tooltip -->
     data-user-id="42"        <!-- Custom data attribute -->
     hidden                   <!-- Hides element -->
     tabindex="0"             <!-- Tab ordering for accessibility -->
     contenteditable="true"   <!-- User can edit content -->
     draggable="true"         <!-- Element can be dragged -->
>
    Content here
</div>
```

---

## 5. Headings, Paragraphs, and Links

Headings (`<h1>` through `<h6>`) create titles and subtitles on your page, with `<h1>` being the most important (largest) and `<h6>` the least important (smallest). Use only ONE `<h1>` per page (for SEO) and follow a logical hierarchy — don't skip from `<h1>` to `<h4>`.

Paragraphs (`<p>`) hold blocks of text. HTML collapses multiple spaces and line breaks into a single space, so if you want a line break inside a paragraph, you need `<br>`.

Links (`<a>` — anchor tags) are how you connect pages together. The `href` attribute specifies where the link goes. You can link to external websites, other pages on your site, sections on the same page (using `#id`), email addresses, and phone numbers.

```html
<!-- Headings: h1 (most important) to h6 (least important) -->
<h1>Main Title</h1>        <!-- Only ONE h1 per page (SEO best practice) -->
<h2>Section Title</h2>
<h3>Subsection</h3>
<h4>Sub-subsection</h4>
<h5>Minor Heading</h5>
<h6>Smallest Heading</h6>

<!-- Paragraphs -->
<p>This is a paragraph. HTML ignores     extra spaces 
and line breaks. Everything becomes a single line.</p>

<p>Use <br> for a line break<br>within a paragraph.</p>

<!-- Horizontal rule (thematic break) -->
<hr>

<!-- Preformatted text (preserves whitespace) -->
<pre>
    This preserves
        all     spaces
    and line breaks
</pre>

<!-- Text formatting -->
<strong>Bold (strong emphasis)</strong>     <!-- Semantic - important -->
<b>Bold (visual only)</b>                  <!-- Just visual -->
<em>Italic (emphasis)</em>                 <!-- Semantic - emphasis -->
<i>Italic (visual only)</i>               <!-- Just visual -->
<mark>Highlighted text</mark>
<del>Deleted text</del>
<ins>Inserted text</ins>
<sub>Subscript</sub> H<sub>2</sub>O
<sup>Superscript</sup> E=mc<sup>2</sup>
<code>Inline code</code>
<blockquote>A longer quotation</blockquote>

<!-- Links (Anchors) -->
<a href="https://google.com">Go to Google</a>
<a href="https://google.com" target="_blank">Open in new tab</a>
<a href="about.html">Relative link</a>
<a href="#section-id">Jump to section on same page</a>
<a href="mailto:kunal@example.com">Send email</a>
<a href="tel:+1234567890">Call us</a>
<a href="file.pdf" download>Download PDF</a>
```

---

## 6. Images, Lists, Tables

These are the core content elements you'll use on almost every webpage.

**Images** (`<img>`) display pictures. The `src` attribute says where the image file is, and the `alt` attribute provides text that describes the image (shown when the image can't load and used by screen readers for blind users — always include it for accessibility).

**Lists** come in two flavors: **unordered** (`<ul>` — bullet points) and **ordered** (`<ol>` — numbered). Each item inside a list uses `<li>`. Lists can be nested inside each other to create hierarchies.

**Tables** (`<table>`) organize data in rows and columns. Use `<thead>` for header rows, `<tbody>` for data rows, and `<tfoot>` for footer rows. `<th>` makes a header cell (bold and centered by default), and `<td>` makes a regular data cell. Tables should be used for **tabular data only** — not for page layout (that's what CSS Flexbox and Grid are for).

### Images

```html
<!-- Basic image -->
<img src="photo.jpg" alt="Description for accessibility" width="400" height="300">

<!-- Responsive image -->
<img src="photo.jpg" alt="Photo" style="max-width: 100%; height: auto;">

<!-- Picture element (serve different images based on screen size) -->
<picture>
    <source media="(min-width: 800px)" srcset="large.jpg">
    <source media="(min-width: 400px)" srcset="medium.jpg">
    <img src="small.jpg" alt="Responsive image">
</picture>

<!-- Figure with caption -->
<figure>
    <img src="chart.png" alt="Sales chart for Q1 2025">
    <figcaption>Figure 1: Q1 2025 Sales Data</figcaption>
</figure>
```

### Lists

```html
<!-- Unordered list -->
<ul>
    <li>Item 1</li>
    <li>Item 2</li>
    <li>Item 3</li>
</ul>

<!-- Ordered list -->
<ol>
    <li>First step</li>
    <li>Second step</li>
    <li>Third step</li>
</ol>

<!-- Ordered list with type -->
<ol type="A">       <!-- A, B, C... -->
<ol type="a">       <!-- a, b, c... -->
<ol type="I">       <!-- I, II, III... -->
<ol start="5">      <!-- Start at 5 -->

<!-- Nested list -->
<ul>
    <li>Frontend
        <ul>
            <li>HTML</li>
            <li>CSS</li>
            <li>JavaScript</li>
        </ul>
    </li>
    <li>Backend
        <ul>
            <li>Java</li>
            <li>Spring Boot</li>
        </ul>
    </li>
</ul>

<!-- Description list -->
<dl>
    <dt>HTML</dt>
    <dd>HyperText Markup Language — structure of web pages</dd>
    <dt>CSS</dt>
    <dd>Cascading Style Sheets — styling of web pages</dd>
</dl>
```

### Tables

```html
<table>
    <caption>Employee Data</caption>
    <thead>
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Department</th>
            <th>Salary</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>Kunal</td>
            <td>Engineering</td>
            <td>$85,000</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Priya</td>
            <td>Design</td>
            <td>$78,000</td>
        </tr>
    </tbody>
    <tfoot>
        <tr>
            <td colspan="3">Total</td>
            <td>$163,000</td>
        </tr>
    </tfoot>
</table>

<!-- Spanning -->
<td colspan="2">Spans 2 columns</td>
<td rowspan="3">Spans 3 rows</td>
```

---

## 7. Div, Span, Navigation

Before semantic HTML5, almost everything was wrapped in `<div>` and `<span>` elements. They're **generic containers** with no built-in meaning.

### Div and Span

`<div>` is a **block-level container** — it takes up the full width of its parent and starts on a new line. Use it to group related elements together for styling or layout purposes.

`<span>` is an **inline container** — it only takes up as much width as its content and stays on the same line. Use it to style a small piece of text within a paragraph.

The key difference is **block vs inline**: block elements stack vertically (like boxes stacked on top of each other), while inline elements sit next to each other horizontally (like words in a sentence).

```html
<!-- div: Block-level container (takes full width, new line) -->
<div class="card">
    <h3>Card Title</h3>
    <p>Card content</p>
</div>

<!-- span: Inline container (only as wide as content, same line) -->
<p>The price is <span class="price">$29.99</span> per month.</p>

<!-- Under the Hood: Block vs Inline -->
<!-- 
Block elements:  div, p, h1-h6, ul, ol, table, form, section, header, footer
  - Take full width
  - Start on new line
  - Can contain block and inline elements

Inline elements: span, a, strong, em, img, input, button, code
  - Only take needed width
  - Stay on same line
  - Can only contain inline elements (mostly)
-->
```

### Navigation

```html
<nav>
    <ul>
        <li><a href="/">Home</a></li>
        <li><a href="/about">About</a></li>
        <li><a href="/services">Services</a></li>
        <li><a href="/contact">Contact</a></li>
    </ul>
</nav>
```

---

## 8. Semantic Elements

**Semantic HTML** means using tags that clearly describe their content's purpose. Instead of `<div class="header">`, you use `<header>`. Instead of `<div class="navigation">`, you use `<nav>`. The content looks the same to the user, but semantics matter for three big reasons:

1. **Accessibility**: Screen readers for visually impaired users can understand the page structure ("this is the navigation, this is the main content")
2. **SEO**: Search engines like Google understand your page better, which can improve your ranking
3. **Readability**: Other developers (and your future self) can understand the code faster

Using plain `<div>` tags for everything is called "div soup" and is considered bad practice.

### Why Semantic HTML Matters

```html
<!-- ❌ Non-semantic (div soup) -->
<div class="header">
    <div class="nav">...</div>
</div>
<div class="content">
    <div class="article">...</div>
    <div class="sidebar">...</div>
</div>
<div class="footer">...</div>

<!-- ✅ Semantic HTML5 -->
<header>
    <nav>...</nav>
</header>
<main>
    <article>...</article>
    <aside>...</aside>
</main>
<footer>...</footer>
```

### Semantic Page Layout

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Semantic Layout</title>
</head>
<body>
    <header>
        <h1>My Website</h1>
        <nav>
            <ul>
                <li><a href="/">Home</a></li>
                <li><a href="/blog">Blog</a></li>
                <li><a href="/about">About</a></li>
            </ul>
        </nav>
    </header>

    <main>
        <article>
            <header>
                <h2>Article Title</h2>
                <time datetime="2025-02-11">February 11, 2025</time>
            </header>
            
            <section>
                <h3>Introduction</h3>
                <p>Article introduction...</p>
            </section>
            
            <section>
                <h3>Main Content</h3>
                <p>Detailed content here...</p>
            </section>
            
            <footer>
                <p>Written by Kunal</p>
            </footer>
        </article>

        <aside>
            <h3>Related Articles</h3>
            <ul>
                <li><a href="#">Related Article 1</a></li>
                <li><a href="#">Related Article 2</a></li>
            </ul>
        </aside>
    </main>

    <footer>
        <p>&copy; 2025 My Website. All rights reserved.</p>
    </footer>
</body>
</html>
```

### All Semantic Elements

| Element | Purpose |
|---------|---------|
| `<header>` | Introductory content or navigational aids |
| `<nav>` | Navigation links |
| `<main>` | Main unique content of the page (only ONE per page) |
| `<article>` | Self-contained content (blog post, news article) |
| `<section>` | Thematic grouping of content |
| `<aside>` | Sidebar, related content |
| `<footer>` | Footer information |
| `<figure>` | Self-contained illustration with caption |
| `<figcaption>` | Caption for `<figure>` |
| `<details>` | Expandable/collapsible content |
| `<summary>` | Heading for `<details>` |
| `<time>` | Date/time value |
| `<mark>` | Highlighted text |

---

## 9. Web Forms 2.0

Forms are how users send data to a server — login pages, registration forms, search bars, payment forms, and contact forms all use HTML forms. The `<form>` element wraps all the inputs, and when the user clicks Submit, the browser packages all the form data and sends it to the URL specified in the `action` attribute using the HTTP method specified in `method` (usually `POST` for sending data, `GET` for searching).

HTML5 introduced many new input types (`date`, `email`, `color`, `range`, etc.) that provide built-in validation and specialized pickers — for example, `type="date"` shows a calendar popup, and `type="email"` checks if the input looks like a valid email address. This means you get basic validation for free without writing any JavaScript.

### Complete Form Example

```html
<form action="/submit" method="POST" enctype="multipart/form-data">
    
    <!-- Text input -->
    <label for="name">Name:</label>
    <input type="text" id="name" name="name" placeholder="Enter your name"
           required minlength="2" maxlength="50" autofocus>

    <!-- Email -->
    <label for="email">Email:</label>
    <input type="email" id="email" name="email" required
           pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$">

    <!-- Password -->
    <label for="password">Password:</label>
    <input type="password" id="password" name="password"
           required minlength="8" pattern="(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,}">

    <!-- Number -->
    <label for="age">Age:</label>
    <input type="number" id="age" name="age" min="18" max="120" step="1">

    <!-- Phone -->
    <label for="phone">Phone:</label>
    <input type="tel" id="phone" name="phone" pattern="[0-9]{10}">

    <!-- URL -->
    <label for="website">Website:</label>
    <input type="url" id="website" name="website">

    <!-- Date/Time inputs (HTML5) -->
    <input type="date" name="date">
    <input type="time" name="time">
    <input type="datetime-local" name="datetime">
    <input type="month" name="month">
    <input type="week" name="week">

    <!-- Range (slider) -->
    <label for="rating">Rating: <output id="ratingOutput">5</output></label>
    <input type="range" id="rating" name="rating" min="1" max="10" value="5"
           oninput="ratingOutput.value=this.value">

    <!-- Color picker -->
    <label for="color">Favorite color:</label>
    <input type="color" id="color" name="color" value="#ff0000">

    <!-- File upload -->
    <label for="avatar">Upload avatar:</label>
    <input type="file" id="avatar" name="avatar" accept="image/*">
    <input type="file" name="documents" multiple accept=".pdf,.doc,.docx">

    <!-- Textarea -->
    <label for="bio">Bio:</label>
    <textarea id="bio" name="bio" rows="5" cols="50" maxlength="500"
              placeholder="Tell us about yourself..."></textarea>

    <!-- Select dropdown -->
    <label for="country">Country:</label>
    <select id="country" name="country" required>
        <option value="" disabled selected>Choose a country</option>
        <optgroup label="Asia">
            <option value="IN">India</option>
            <option value="JP">Japan</option>
        </optgroup>
        <optgroup label="Europe">
            <option value="UK">United Kingdom</option>
            <option value="DE">Germany</option>
        </optgroup>
    </select>

    <!-- Radio buttons -->
    <fieldset>
        <legend>Gender</legend>
        <input type="radio" id="male" name="gender" value="male">
        <label for="male">Male</label>
        <input type="radio" id="female" name="gender" value="female">
        <label for="female">Female</label>
        <input type="radio" id="other" name="gender" value="other">
        <label for="other">Other</label>
    </fieldset>

    <!-- Checkboxes -->
    <fieldset>
        <legend>Skills</legend>
        <input type="checkbox" id="html" name="skills" value="html" checked>
        <label for="html">HTML</label>
        <input type="checkbox" id="css" name="skills" value="css">
        <label for="css">CSS</label>
        <input type="checkbox" id="js" name="skills" value="js">
        <label for="js">JavaScript</label>
    </fieldset>

    <!-- Datalist (autocomplete) -->
    <label for="browser">Browser:</label>
    <input list="browsers" id="browser" name="browser">
    <datalist id="browsers">
        <option value="Chrome">
        <option value="Firefox">
        <option value="Safari">
        <option value="Edge">
    </datalist>

    <!-- Hidden input -->
    <input type="hidden" name="csrf_token" value="abc123xyz">

    <!-- Buttons -->
    <button type="submit">Submit</button>
    <button type="reset">Reset</button>
    <button type="button" onclick="alert('Clicked!')">Click Me</button>
</form>
```

### Form Validation

HTML5 provides **built-in form validation** that works without any JavaScript. By adding attributes like `required`, `minlength`, `maxlength`, `min`, `max`, and `pattern` to your input elements, the browser will automatically check the user's input and show error messages if something is wrong. This is called **client-side validation** — it gives users instant feedback without needing to send data to the server first. However, you should **always validate on the server side too**, because client-side validation can be bypassed.

```html
<!-- Built-in HTML5 validation attributes -->
required                    <!-- Field must be filled -->
minlength="3"              <!-- Minimum characters -->
maxlength="50"             <!-- Maximum characters -->
min="1"                    <!-- Minimum number/date value -->
max="100"                  <!-- Maximum number/date value -->
step="5"                   <!-- Number increment (5, 10, 15...) -->
pattern="[A-Za-z]{3,}"    <!-- Regex pattern to match -->

<!-- Custom validation message -->
<input type="text" required
       oninvalid="this.setCustomValidity('Please enter your name')"
       oninput="this.setCustomValidity('')">
```

---

## 10. Web Storage

Web Storage lets you save data right in the user's browser — no server needed. Before Web Storage, the only option was cookies (which are tiny, only 4KB, and get sent to the server with every request, slowing things down). Web Storage gives you two options:

- **`localStorage`**: Data stays even after the browser is closed and reopened. Good for saving user preferences like dark mode.
- **`sessionStorage`**: Data disappears when the user closes the tab. Good for temporary data like what step they're on in a multi-step form.

Both can store about 5-10MB of data (much more than cookies). You store data as key-value pairs (like a dictionary), and everything must be stored as strings — so use `JSON.stringify()` for objects and `JSON.parse()` to read them back.

```html
<script>
// localStorage — persists even after browser is closed
localStorage.setItem('username', 'Kunal');
localStorage.setItem('theme', 'dark');

let user = localStorage.getItem('username');    // "Kunal"
localStorage.removeItem('username');
localStorage.clear();                           // Remove all

// Store objects (must serialize to JSON)
const settings = { theme: 'dark', font: '16px', lang: 'en' };
localStorage.setItem('settings', JSON.stringify(settings));
const loaded = JSON.parse(localStorage.getItem('settings'));

// sessionStorage — cleared when tab/browser is closed
sessionStorage.setItem('tempData', 'value');
sessionStorage.getItem('tempData');

// Storage comparison:
// localStorage:    ~5-10MB, persists forever, same-origin
// sessionStorage:  ~5-10MB, cleared on tab close, same-origin
// Cookies:         ~4KB, sent with every HTTP request, has expiry
</script>
```

---

## 11. Geolocation

The Geolocation API lets your website ask the user for their physical location (latitude and longitude). This is useful for features like "find nearby restaurants" or "show my location on a map." The browser will always ask the user for permission first — no website can track your location without your consent. The accuracy depends on the device: phones with GPS give very accurate locations, while desktops rely on IP address and Wi-Fi signals which are less precise.

```html
<script>
if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
        // Success callback
        function(position) {
            console.log('Latitude: ' + position.coords.latitude);
            console.log('Longitude: ' + position.coords.longitude);
            console.log('Accuracy: ' + position.coords.accuracy + ' meters');
        },
        // Error callback
        function(error) {
            switch(error.code) {
                case error.PERMISSION_DENIED:
                    console.log("User denied geolocation");
                    break;
                case error.POSITION_UNAVAILABLE:
                    console.log("Location unavailable");
                    break;
                case error.TIMEOUT:
                    console.log("Request timed out");
                    break;
            }
        },
        // Options
        {
            enableHighAccuracy: true,
            timeout: 5000,
            maximumAge: 0
        }
    );
    
    // Watch position (continuous tracking)
    const watchId = navigator.geolocation.watchPosition(successCallback);
    // Stop watching
    navigator.geolocation.clearWatch(watchId);
}
</script>
```

---

# CSS — Cascading Style Sheets

---

## 12. CSS Syntax

While HTML defines **what** content is (headings, paragraphs, images), CSS defines **how** that content looks (colors, fonts, spacing, layout). CSS stands for **Cascading Style Sheets** — "cascading" means that multiple styles can apply to the same element, and there are rules for which one wins (more on that in specificity).

A CSS rule has two parts: a **selector** (which elements to style) and a **declaration block** (what styles to apply). Each declaration is a property-value pair like `color: blue;`.

```css
/* CSS Rule Structure */
selector {
    property: value;
    property: value;
}

/* Example */
h1 {
    color: blue;
    font-size: 24px;
    text-align: center;
}
```

---

## 13. Ways to Apply CSS

There are three places you can write CSS code:

1. **Inline CSS**: Written directly on an element using the `style` attribute. It's the most specific (wins over other styles) but the hardest to maintain — imagine having to change the color on 50 different elements one by one. **Avoid this** in production code.
2. **Internal CSS**: Written inside a `<style>` tag in the `<head>` of your HTML file. Good for single-page demos but not scalable for multi-page sites.
3. **External CSS**: Written in a separate `.css` file and linked with `<link>`. This is the **best practice** because you can reuse the same stylesheet across all pages, and changing one file updates the entire site.

```html
<!-- 1. Inline CSS (highest specificity — avoid) -->
<p style="color: red; font-size: 16px;">Red text</p>

<!-- 2. Internal CSS (in <style> tag in <head>) -->
<head>
    <style>
        p {
            color: blue;
            font-size: 16px;
        }
    </style>
</head>

<!-- 3. External CSS (recommended — best practice) -->
<head>
    <link rel="stylesheet" href="styles.css">
</head>
```

**Priority Order**: Inline > Internal/External (last one wins) > Browser defaults  
**Best Practice**: Always use external CSS for maintainability.

---

## 14. CSS Selectors

Selectors are how you tell CSS **which elements** to style. They are the targeting system of CSS. The simplest selectors target elements by their tag name (`p`), class (`.card`), or ID (`#header`). More advanced selectors can target elements based on their position in the page (first child, every other row), their state (hovered, focused, checked), or their relationship to other elements (direct child, sibling).

Mastering selectors means you can style any element on the page without adding extra classes or IDs to your HTML.

```css
/* ─── Basic Selectors ─── */
*                   { }    /* Universal — selects ALL elements */
h1                  { }    /* Element/Type selector */
.classname          { }    /* Class selector */
#unique-id          { }    /* ID selector (use sparingly) */

/* ─── Combinators ─── */
div p               { }    /* Descendant: p inside div (any depth) */
div > p             { }    /* Child: p directly inside div */
h1 + p              { }    /* Adjacent sibling: p immediately after h1 */
h1 ~ p              { }    /* General sibling: all p after h1 (same parent) */

/* ─── Attribute Selectors ─── */
[type]               { }    /* Has attribute */
[type="text"]        { }    /* Exact value */
[class~="btn"]       { }    /* Contains word "btn" */
[href^="https"]      { }    /* Starts with "https" */
[href$=".pdf"]       { }    /* Ends with ".pdf" */
[href*="example"]    { }    /* Contains "example" */

/* ─── Pseudo-classes ─── */
a:hover              { }    /* Mouse over */
a:active             { }    /* Being clicked */
a:visited            { }    /* Already visited link */
input:focus          { }    /* Element has focus */
input:checked        { }    /* Checked checkbox/radio */
input:disabled       { }    /* Disabled input */
p:first-child        { }    /* First child of parent */
p:last-child         { }    /* Last child */
p:nth-child(2)       { }    /* 2nd child */
p:nth-child(odd)     { }    /* Odd children (1st, 3rd, 5th...) */
p:nth-child(even)    { }    /* Even children */
p:nth-child(3n)      { }    /* Every 3rd child */
p:not(.special)      { }    /* NOT matching selector */

/* ─── Pseudo-elements ─── */
p::first-line        { }    /* First line of text */
p::first-letter      { }    /* First letter */
p::before            { content: "→ "; }  /* Insert before */
p::after             { content: " ←"; }  /* Insert after */
::selection          { }    /* Text selected by user */
::placeholder        { }    /* Input placeholder text */
```

### CSS Specificity (How Conflicts Are Resolved)

When multiple CSS rules target the same element and set the same property, CSS needs a way to decide **which one wins**. That's specificity — a scoring system. Each selector gets a score based on how specific it is. ID selectors (`#header`) score higher than class selectors (`.card`), which score higher than element selectors (`p`). Inline styles beat everything. The rule with the highest specificity wins.

Understanding specificity prevents the frustrating "why isn't my CSS working?" moments. If your style isn't applying, it's almost always because a more specific rule is overriding it.

```
Specificity is calculated as a 4-part score: (a, b, c, d)

a = Inline styles (style="...")     = 1000
b = ID selectors (#id)              = 100
c = Classes, pseudo-classes, attrs  = 10
d = Elements, pseudo-elements       = 1

Examples:
p                    → 0,0,0,1 = 1
.card                → 0,0,1,0 = 10
#header              → 0,1,0,0 = 100
div.card             → 0,0,1,1 = 11
#header .nav li      → 0,1,1,1 = 111
style="color:red"    → 1,0,0,0 = 1000

!important           → Overrides EVERYTHING (⚠️ avoid)

Higher specificity WINS. If tied, last rule wins.
```

---

## 15. Styling Text and Fonts

Typography is one of the most impactful parts of web design — the right font and text styling can make your site look professional or amateur. `font-family` sets which font to use (always provide fallback fonts in case the first one isn't available). `font-size` controls how big the text is. `font-weight` controls how bold it is (400 is normal, 700 is bold). `line-height` controls the spacing between lines — a value of 1.6 is generally comfortable to read.

For web projects, **Google Fonts** provides hundreds of free, high-quality fonts that you can use by simply adding a `<link>` tag.

```css
/* Font properties */
body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    font-size: 16px;           /* Base size */
    font-weight: 400;          /* 100-900; 400=normal, 700=bold */
    font-style: italic;        /* normal, italic, oblique */
    line-height: 1.6;          /* 1.6 times font-size */
    letter-spacing: 0.5px;     /* Space between letters */
    word-spacing: 2px;         /* Space between words */
}

/* Text properties */
h1 {
    text-align: center;        /* left, right, center, justify */
    text-decoration: underline; /* none, underline, line-through, overline */
    text-transform: uppercase;  /* uppercase, lowercase, capitalize */
    text-indent: 50px;          /* First line indent */
    text-shadow: 2px 2px 4px rgba(0,0,0,0.3);  /* h-offset v-offset blur color */
    white-space: nowrap;        /* Prevent text wrapping */
    overflow: hidden;           /* Hide overflowing text */
    text-overflow: ellipsis;    /* Show "..." for overflow */
}

/* Google Fonts usage */
/* Add to HTML <head>: */
/* <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap" rel="stylesheet"> */

body {
    font-family: 'Roboto', sans-serif;
}

/* CSS Units */
/* 
Absolute:       px, pt, cm, in
Relative:       em (parent font-size), rem (root font-size), 
                % (parent), vw (viewport width), vh (viewport height)

Best practice: Use rem for font-size, px for borders, % or vw/vh for layout
*/
```

---

## 16. Box Model

The **Box Model** is the most fundamental concept in CSS. Every HTML element is treated as a rectangular box with four layers: **content** (the actual text/image), **padding** (space between content and border — like bubble wrap inside a box), **border** (the edge of the box), and **margin** (space between this box and other elements — like the gap between two boxes on a shelf).

The most common gotcha with the box model is how `width` works. By default (`box-sizing: content-box`), if you set `width: 300px`, that's ONLY the content — padding and border are added on top, making the total element wider than 300px. By switching to `box-sizing: border-box`, `width: 300px` includes everything (content + padding + border), which is much more intuitive. **Always use `border-box`** — most CSS resets set it globally.

### Under the Hood: Every Element is a Box

```
┌─────────────────────────────────────────────────┐
│                   MARGIN                        │
│  ┌───────────────────────────────────────────┐  │
│  │                BORDER                     │  │
│  │  ┌─────────────────────────────────────┐  │  │
│  │  │             PADDING                 │  │  │
│  │  │  ┌───────────────────────────────┐  │  │  │
│  │  │  │          CONTENT              │  │  │  │
│  │  │  │     (width × height)          │  │  │  │
│  │  │  └───────────────────────────────┘  │  │  │
│  │  │                                     │  │  │
│  │  └─────────────────────────────────────┘  │  │
│  │                                           │  │
│  └───────────────────────────────────────────┘  │
│                                                 │
└─────────────────────────────────────────────────┘
```

```css
.box {
    /* Content */
    width: 300px;
    height: 200px;

    /* Padding (inside border) */
    padding: 20px;                    /* All sides */
    padding: 10px 20px;              /* Vertical | Horizontal */
    padding: 10px 20px 30px;         /* Top | Horizontal | Bottom */
    padding: 10px 20px 30px 40px;    /* Top | Right | Bottom | Left (clockwise) */

    /* Border */
    border: 2px solid #333;
    border-radius: 8px;              /* Rounded corners */

    /* Margin (outside border) */
    margin: 20px;
    margin: 0 auto;                  /* Center horizontally */

    /* ⭐ box-sizing (critical!) */
    box-sizing: border-box;
    /* Default: content-box → width = content only
       border-box → width = content + padding + border
       ALWAYS use border-box! */
}

/* Global box-sizing reset (best practice) */
*, *::before, *::after {
    box-sizing: border-box;
}
```

### Total Element Width Calculation

```
content-box (default):
  Total Width = width + padding-left + padding-right + border-left + border-right
  Example: 300 + 20 + 20 + 2 + 2 = 344px  ← UNEXPECTED!

border-box (recommended):
  Total Width = width (content shrinks to fit)
  Example: width: 300px → always 300px total
```

---

## 17. Colors, Backgrounds, Borders

CSS gives you fine-grained control over how elements look visually. **Colors** can be specified in many formats: named colors (`red`), hex codes (`#ff0000`), RGB values (`rgb(255,0,0)`), or HSL values (`hsl(0,100%,50%)`). HSL is often easiest to work with because you can intuitively adjust hue (the color), saturation (how vivid), and lightness (how bright).

**Backgrounds** can be solid colors, images, or gradients (smooth transitions between colors). **Borders** create visible edges around elements, and `border-radius` rounds the corners (set it to `50%` for a perfect circle). **Box shadows** add depth and make elements look like they're floating above the page.

```css
/* Colors */
.element {
    /* Named colors */
    color: red;
    
    /* Hex */
    color: #ff0000;           /* Full hex */
    color: #f00;              /* Shorthand */
    color: #ff000080;         /* With alpha (50% opacity) */
    
    /* RGB */
    color: rgb(255, 0, 0);
    color: rgba(255, 0, 0, 0.5);   /* With alpha */
    
    /* HSL (Hue, Saturation, Lightness) */
    color: hsl(0, 100%, 50%);      /* Red */
    color: hsla(0, 100%, 50%, 0.5);
}

/* Backgrounds */
.card {
    background-color: #f5f5f5;
    background-image: url('bg.jpg');
    background-size: cover;         /* cover, contain, or specific size */
    background-position: center;
    background-repeat: no-repeat;   /* no-repeat, repeat-x, repeat-y */
    background-attachment: fixed;   /* Parallax effect */
    
    /* Shorthand */
    background: #f5f5f5 url('bg.jpg') no-repeat center/cover;
    
    /* Gradient backgrounds */
    background: linear-gradient(to right, #ff0000, #0000ff);
    background: linear-gradient(135deg, #667eea, #764ba2);
    background: radial-gradient(circle, #ff0000, #0000ff);
}

/* Borders */
.box {
    border: 2px solid #333;
    border-top: 3px dashed red;
    border-radius: 8px;            /* All corners */
    border-radius: 50%;            /* Circle (if equal width/height) */
    border-radius: 10px 20px 30px 40px;  /* Each corner */
    
    /* Box shadow */
    box-shadow: 5px 5px 10px rgba(0,0,0,0.3);
    /* h-offset, v-offset, blur, color */
    box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1), 0 2px 4px -1px rgba(0,0,0,0.06);
    /* Multiple shadows */
    
    /* Opacity */
    opacity: 0.8;                  /* 0 (invisible) to 1 (fully visible) */
}
```

---

## 18. Layout with CSS

CSS layout is all about controlling **where elements appear on the page** and **how much space they take up**. The `display` property is the foundation — it determines whether an element behaves as a block (full width, starts new line), inline (only as wide as content, stays on same line), or something else entirely (flex, grid).

The `position` property controls how an element is positioned relative to its normal spot in the page flow. `relative` shifts it from its normal position. `absolute` removes it from the flow and positions it relative to its nearest positioned ancestor. `fixed` keeps it in the same spot even when you scroll (like a sticky navbar). `sticky` is a clever hybrid — it acts normal until you scroll past it, then it sticks to the viewport.

### Display Property

```css
/* Block: takes full width, starts new line */
.block { display: block; }

/* Inline: only takes needed width, stays on line */
.inline { display: inline; }

/* Inline-block: inline but respects width/height */
.inline-block { display: inline-block; }

/* None: completely removes from layout */
.hidden { display: none; }

/* Visibility: hidden but still takes space */
.invisible { visibility: hidden; }
```

### Position

```css
/* Static (default) — normal document flow */
.static { position: static; }

/* Relative — offset from normal position, still takes original space */
.relative {
    position: relative;
    top: 10px;      /* Push down 10px from original position */
    left: 20px;
}

/* Absolute — removed from flow, positioned relative to nearest positioned ancestor */
.absolute {
    position: absolute;
    top: 0;
    right: 0;
}

/* Fixed — removed from flow, positioned relative to viewport (stays on scroll) */
.fixed {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    z-index: 1000;    /* Stacking order */
}

/* Sticky — switches between relative and fixed based on scroll */
.sticky {
    position: sticky;
    top: 0;           /* Becomes fixed when scrolled past this point */
}
```

### Float (Legacy — use Flexbox/Grid instead)

```css
.float-left { float: left; }
.float-right { float: right; }
.clearfix::after {           /* Clear float hack */
    content: "";
    display: table;
    clear: both;
}
```

---

## 19. Flexbox Layout

**Flexbox** (Flexible Box Layout) is a CSS layout system designed for laying out items in **one direction** — either a row (horizontal) or a column (vertical). Before Flexbox, centering things in CSS was notoriously difficult and hacky. With Flexbox, centering is literally three lines of code: `display: flex; justify-content: center; align-items: center;`.

The key concept is: you have a **flex container** (the parent with `display: flex`) and **flex items** (the direct children). The container controls the overall direction and alignment, while individual items can control how much they grow or shrink. The **main axis** is the direction items flow (row = horizontal), and the **cross axis** is perpendicular to it (row = vertical).

Flexbox is perfect for: navigation bars, centering content, equal-height columns, card layouts, and footer-at-bottom designs.

### Under the Hood: Flexbox model

```
┌─── Flex Container ────────────────────────────────────┐
│  Main Axis →                                          │
│  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  ┌──────┐  │ ↕ Cross
│  │Item 1│  │Item 2│  │Item 3│  │Item 4│  │Item 5│  │   Axis
│  └──────┘  └──────┘  └──────┘  └──────┘  └──────┘  │
└───────────────────────────────────────────────────────┘
```

```css
/* Flex Container Properties */
.container {
    display: flex;

    /* Direction of main axis */
    flex-direction: row;             /* → (default) */
    flex-direction: row-reverse;     /* ← */
    flex-direction: column;          /* ↓ */
    flex-direction: column-reverse;  /* ↑ */

    /* Alignment on MAIN axis */
    justify-content: flex-start;     /* |■ ■ ■      | */
    justify-content: flex-end;       /* |      ■ ■ ■| */
    justify-content: center;         /* |   ■ ■ ■   | */
    justify-content: space-between;  /* |■    ■    ■| */
    justify-content: space-around;   /* | ■   ■   ■ | */
    justify-content: space-evenly;   /* |  ■  ■  ■  | */

    /* Alignment on CROSS axis */
    align-items: stretch;            /* Default — items stretch to fill */
    align-items: flex-start;         /* Top */
    align-items: flex-end;           /* Bottom */
    align-items: center;             /* Center vertically */
    align-items: baseline;           /* Align text baselines */

    /* Wrapping */
    flex-wrap: nowrap;               /* Default — single line */
    flex-wrap: wrap;                 /* Multi-line */
    flex-wrap: wrap-reverse;

    /* Multi-line alignment (only works with wrap) */
    align-content: flex-start;
    align-content: center;
    align-content: space-between;

    /* Gap between items */
    gap: 20px;                       /* Both row and column gap */
    row-gap: 20px;
    column-gap: 10px;
}

/* Flex Item Properties */
.item {
    flex-grow: 1;      /* How much item should grow (0 = don't grow) */
    flex-shrink: 1;    /* How much item should shrink (0 = don't shrink) */
    flex-basis: 200px; /* Initial size before growing/shrinking */
    
    /* Shorthand */
    flex: 1;           /* flex-grow: 1, flex-shrink: 1, flex-basis: 0 */
    flex: 0 0 200px;   /* Don't grow, don't shrink, 200px wide */

    /* Override container's align-items for this item */
    align-self: flex-end;

    /* Order (visual order, not DOM order) */
    order: 2;          /* Default is 0, lower = appears first */
}
```

### Common Flexbox Patterns

```css
/* Center anything (horizontally + vertically) */
.center-everything {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}

/* Navbar with logo left, links right */
.navbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 0 20px;
}

/* Equal-width columns */
.columns {
    display: flex;
    gap: 20px;
}
.columns > * {
    flex: 1;
}

/* Footer always at bottom */
body {
    display: flex;
    flex-direction: column;
    min-height: 100vh;
}
main {
    flex: 1;    /* Takes all available space, pushes footer down */
}
```

---

## 20. CSS Grid Layout

While Flexbox handles **one-dimensional** layouts (row OR column), **CSS Grid** handles **two-dimensional** layouts (rows AND columns at the same time). Think of Grid like a spreadsheet — you define how many rows and columns you want, then place items into specific cells. Grid is ideal for overall page layouts (header, sidebar, content, footer) and any situation where you need precise control over both horizontal and vertical placement.

The `fr` unit (fraction) is unique to Grid — `1fr` means "one fraction of the available space." So `grid-template-columns: 1fr 2fr` creates two columns where the second is twice as wide as the first. The `repeat()` function saves you from typing the same thing multiple times: `repeat(3, 1fr)` means `1fr 1fr 1fr`.

**When to use Flexbox vs Grid**: Use Flexbox for components inside a page (nav bars, card contents, centering). Use Grid for the overall page layout and complex two-dimensional designs. You can (and should) use both together.

### Under the Hood: Grid Model

```
┌────────┬────────┬────────┐
│  1,1   │  1,2   │  1,3   │  Row 1
├────────┼────────┼────────┤
│  2,1   │  2,2   │  2,3   │  Row 2
├────────┼────────┼────────┤
│  3,1   │  3,2   │  3,3   │  Row 3
└────────┴────────┴────────┘
 Col 1    Col 2    Col 3

Grid Lines:  1    2    3    4  (columns)
             1    2    3    4  (rows)
```

```css
/* Grid Container */
.grid {
    display: grid;
    
    /* Define columns */
    grid-template-columns: 200px 200px 200px;           /* 3 fixed columns */
    grid-template-columns: 1fr 1fr 1fr;                 /* 3 equal columns */
    grid-template-columns: repeat(3, 1fr);              /* Same as above */
    grid-template-columns: 200px 1fr 2fr;               /* Fixed + flexible */
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); /* Responsive! */
    
    /* Define rows */
    grid-template-rows: 100px 200px 100px;
    grid-template-rows: auto 1fr auto;                  /* Header, content, footer */
    
    /* Gap */
    gap: 20px;
    row-gap: 20px;
    column-gap: 10px;
    
    /* Named areas */
    grid-template-areas:
        "header header header"
        "sidebar main   main"
        "footer footer footer";
    
    /* Alignment */
    justify-items: center;     /* Horizontal alignment of all items */
    align-items: center;       /* Vertical alignment of all items */
    place-items: center;       /* Both at once */
}

/* Grid Items */
.item {
    /* Placement by line numbers */
    grid-column: 1 / 3;       /* Span from column line 1 to 3 */
    grid-row: 1 / 2;          /* Span from row line 1 to 2 */
    
    /* Shorthand */
    grid-column: 1 / span 2;  /* Start at 1, span 2 columns */
    grid-area: 2 / 1 / 4 / 3; /* row-start / col-start / row-end / col-end */
    
    /* Named area placement */
    grid-area: header;         /* Place in "header" area */
}
```

### Grid Layout Example: Dashboard

```css
.dashboard {
    display: grid;
    grid-template-columns: 250px 1fr;
    grid-template-rows: 60px 1fr 40px;
    grid-template-areas:
        "header  header"
        "sidebar content"
        "footer  footer";
    min-height: 100vh;
    gap: 0;
}

.header  { grid-area: header;  background: #333; color: white; }
.sidebar { grid-area: sidebar; background: #f0f0f0; }
.content { grid-area: content; padding: 20px; }
.footer  { grid-area: footer;  background: #333; color: white; }
```

---

## 21. Responsive Design

Responsive design means making your website look good on **all screen sizes** — from a small phone screen to a large desktop monitor. The same HTML and CSS adapts its layout based on the screen width.

The approach is **mobile-first**: you write your base CSS for the smallest screens first, then use **media queries** (`@media`) to add styles for larger screens. Media queries are like conditional statements for CSS — "if the screen is at least 768px wide, apply these styles." This way, your site works on phones by default and gets enhanced as screen size increases.

The essential meta tag `<meta name="viewport" content="width=device-width, initial-scale=1.0">` is required for responsive design to work on mobile devices — without it, phones render the page at desktop width and then shrink it down, making everything tiny.

```css
/* Mobile-first approach (start small, add breakpoints for larger) */

/* Base styles (mobile) */
.container {
    padding: 10px;
}

.grid {
    display: grid;
    grid-template-columns: 1fr;    /* Single column on mobile */
    gap: 20px;
}

/* Tablet (768px+) */
@media (min-width: 768px) {
    .container {
        padding: 20px;
        max-width: 720px;
        margin: 0 auto;
    }
    .grid {
        grid-template-columns: repeat(2, 1fr);  /* 2 columns */
    }
}

/* Desktop (1024px+) */
@media (min-width: 1024px) {
    .container {
        max-width: 960px;
    }
    .grid {
        grid-template-columns: repeat(3, 1fr);  /* 3 columns */
    }
}

/* Large Desktop (1200px+) */
@media (min-width: 1200px) {
    .container {
        max-width: 1140px;
    }
    .grid {
        grid-template-columns: repeat(4, 1fr);  /* 4 columns */
    }
}

/* Common breakpoints:
   Mobile:        < 576px
   Small tablet:  576px - 767px
   Tablet:        768px - 991px
   Desktop:       992px - 1199px
   Large Desktop: 1200px+
*/

/* Essential responsive meta tag (already in HTML boilerplate) */
/* <meta name="viewport" content="width=device-width, initial-scale=1.0"> */
```

---

## 22. Transitions, Animations, Transforms

These CSS features add **motion and interactivity** to your website.

**Transitions** create smooth changes between states. For example, when a user hovers over a button, instead of the color changing instantly, it fades smoothly over 0.3 seconds. You define transitions on the "resting" state and the browser handles the animation automatically when the state changes.

**Transforms** change an element's shape, size, or position without affecting the document layout. You can `translate` (move), `scale` (resize), `rotate` (spin), and `skew` (tilt) elements. Transforms are often combined with transitions for interactive effects (like a button moving up slightly on hover).

**Animations** (using `@keyframes`) create complex, multi-step animations that can loop infinitely. Unlike transitions which only go between two states (normal and hovered), animations can go through many stages — like a bouncing ball that moves up, comes down, squishes, and bounces up again.

```css
/* ─── Transitions ─── */
.button {
    background-color: #3498db;
    color: white;
    padding: 12px 24px;
    border: none;
    transition: all 0.3s ease;
    /* transition: property duration timing-function delay; */
}

.button:hover {
    background-color: #2980b9;
    transform: translateY(-2px);
    box-shadow: 0 4px 8px rgba(0,0,0,0.2);
}

/* Timing functions: ease, linear, ease-in, ease-out, ease-in-out, cubic-bezier() */

/* ─── Transforms ─── */
.element {
    transform: translateX(50px);        /* Move horizontally */
    transform: translateY(-20px);       /* Move vertically */
    transform: translate(50px, -20px);  /* Both */
    transform: scale(1.5);             /* Enlarge 150% */
    transform: rotate(45deg);          /* Rotate 45 degrees */
    transform: skew(10deg, 5deg);      /* Skew */
    transform: rotate(45deg) scale(1.2); /* Multiple transforms */
}

/* ─── Animations (Keyframes) ─── */
@keyframes slideIn {
    from {
        transform: translateX(-100%);
        opacity: 0;
    }
    to {
        transform: translateX(0);
        opacity: 1;
    }
}

@keyframes bounce {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(-30px); }
}

.animated-element {
    animation: slideIn 0.5s ease-out forwards;
    /* animation: name duration timing-function delay iteration-count direction fill-mode; */
}

.bouncing {
    animation: bounce 1s ease-in-out infinite;
}

/* Pulse effect */
@keyframes pulse {
    0% { transform: scale(1); }
    50% { transform: scale(1.05); }
    100% { transform: scale(1); }
}

.pulse {
    animation: pulse 2s infinite;
}
```

---

## 23. Quick Reference

```
HTML: Structure (what content IS)
CSS:  Presentation (how content LOOKS)

Box Model: Content → Padding → Border → Margin
Flexbox:   1D layout (row OR column)
Grid:      2D layout (rows AND columns)

Specificity: Inline (1000) > ID (100) > Class (10) > Element (1)
Responsive:  Mobile-first + media queries
```
