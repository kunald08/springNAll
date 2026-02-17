# jQuery & AJAX — From Basics to Expert

---

## 1. What is jQuery?

**jQuery** is a fast, lightweight JavaScript library (created by John Resig in 2006) that simplifies:
- DOM manipulation
- Event handling
- AJAX calls
- Animations

### Under the Hood: How jQuery Works

```
jQuery wraps native JavaScript → provides shorter, cross-browser syntax

Native JS:                                jQuery:
document.getElementById("title")    →     $("#title")
document.querySelectorAll(".card")  →     $(".card")
element.addEventListener("click")   →     element.on("click")
element.style.display = "none"      →     element.hide()
```

### Why jQuery Still Matters

- Legacy codebases (millions of sites still use it)
- Understanding jQuery helps you appreciate vanilla JS
- Some libraries/plugins still depend on it
- Simpler syntax for quick DOM manipulation

> **Note**: For new projects, vanilla JavaScript (ES6+) is preferred. jQuery is considered **legacy** but still widely used.

### Setup via CDN

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery Demo</title>
</head>
<body>
    <h1 id="title">Hello jQuery</h1>
    <button id="btn">Click Me</button>

    <!-- jQuery CDN (load before your scripts) -->
    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    <script src="app.js"></script>
</body>
</html>
```

### Document Ready

```javascript
// Wait for DOM to be fully loaded before running jQuery code

// Method 1: Classic (verbose)
$(document).ready(function() {
    console.log("DOM is ready!");
});

// Method 2: Shorthand (recommended)
$(function() {
    console.log("DOM is ready!");
});

// Under the Hood: This is similar to:
document.addEventListener("DOMContentLoaded", function() {
    console.log("DOM is ready!");
});
```

---

## 2. jQuery Selectors

Selectors are how you tell jQuery **which elements** on the page you want to work with. jQuery uses the same selector syntax as CSS — so if you know CSS selectors, you already know jQuery selectors. The `$()` function (also written as `jQuery()`) is the core of everything in jQuery. You pass it a selector string, and it finds all matching elements and wraps them in a jQuery object that gives you access to all jQuery methods.

The difference between a jQuery object and a raw DOM element is important. A jQuery object has all the jQuery methods (`.hide()`, `.addClass()`, `.animate()`, etc.), while a raw DOM element only has native JavaScript methods. You can convert between them: `$(domElement)` wraps a DOM element in jQuery, and `$("#id")[0]` extracts the raw DOM element from a jQuery object.

jQuery uses **CSS selectors** (same as `document.querySelector`), wrapped in `$()`:

```javascript
// ─── Basic Selectors ───
$("*")                  // All elements
$("p")                  // All <p> elements
$(".card")              // All elements with class "card"
$("#title")             // Element with id "title"
$("div.card")           // <div> elements with class "card"
$("ul li")              // All <li> inside <ul> (descendant)
$("ul > li")            // Direct child <li> of <ul>
$("h2 + p")             // <p> immediately after <h2>
$("h2 ~ p")             // All <p> siblings after <h2>

// ─── Attribute Selectors ───
$("[href]")             // Elements with href attribute
$("[type='text']")      // Input type text
$("[href^='https']")    // href starts with "https"
$("[href$='.pdf']")     // href ends with ".pdf"
$("[href*='example']")  // href contains "example"

// ─── Filter Selectors ───
$("li:first")           // First <li>
$("li:last")            // Last <li>
$("li:eq(2)")           // 3rd <li> (0-indexed)
$("li:lt(3)")           // First 3 <li> elements
$("li:gt(2)")           // All <li> after index 2
$("li:even")            // Even-indexed (0, 2, 4...)
$("li:odd")             // Odd-indexed (1, 3, 5...)
$("li:not(.active)")    // <li> without class "active"
$("div:has(p)")         // <div> containing <p>
$(":input")             // All form inputs (input, textarea, select, button)
$(":checked")           // All checked checkboxes/radios
$(":visible")           // All visible elements
$(":hidden")            // All hidden elements
$("p:contains('Hello')")// <p> containing text "Hello"
$(":empty")             // Elements with no children

// ─── Multiple Selectors ───
$("h1, h2, h3")         // All h1, h2, and h3 elements

// ─── Context/Scope ───
$("li", "#myList")       // <li> elements inside #myList only
```

### The jQuery Object vs DOM Element

```javascript
// $() returns a jQuery OBJECT (not a raw DOM element)
const $title = $("#title");     // jQuery object (by convention, prefix with $)
const title = $("#title")[0];   // Raw DOM element (access via index)
const title2 = $("#title").get(0); // Same thing

// Convert DOM element to jQuery object
const domElement = document.getElementById("title");
const $jqElement = $(domElement);   // Now you can use jQuery methods

// Check if element exists
if ($("#title").length > 0) {
    console.log("Element exists!");
}

// Chaining (jQuery methods return the jQuery object → chain multiple calls)
$("p")
    .addClass("highlight")
    .css("color", "red")
    .fadeIn(500)
    .text("Updated!");
```

---

## 3. DOM Manipulation & Events

DOM manipulation is the core of what jQuery does — **reading, changing, adding, and removing content** from the web page. jQuery provides short, readable methods for everything: `.text()` and `.html()` for changing content, `.attr()` for changing attributes, `.css()` for changing styles, `.addClass()` and `.removeClass()` for toggling CSS classes, and `.append()`, `.prepend()`, `.remove()` for adding and removing elements.

One of jQuery's best features is **chaining** — since most jQuery methods return the jQuery object itself, you can call multiple methods in a row: `$("p").addClass("highlight").css("color", "red").fadeIn(500)`. This makes your code concise and readable.

### Reading and Modifying Content

```javascript
// ─── Text Content ───
$("p").text();                           // GET text content
$("p").text("New text");                 // SET text content

// ─── HTML Content ───
$("div").html();                         // GET HTML
$("div").html("<strong>Bold text</strong>"); // SET HTML

// ─── Form Values ───
$("input").val();                        // GET value
$("input").val("New value");             // SET value
$("select").val();                       // GET selected option
$("select").val("option2");              // SET selected option

// ─── Attributes ───
$("a").attr("href");                     // GET attribute
$("a").attr("href", "https://new.com"); // SET attribute
$("a").attr({                            // SET multiple attributes
    href: "https://new.com",
    target: "_blank"
});
$("a").removeAttr("target");             // REMOVE attribute

// ─── Data Attributes ───
$("div").data("user-id");               // GET data-user-id
$("div").data("user-id", 42);           // SET data-user-id

// ─── Properties (for boolean attributes) ───
$("input").prop("checked");              // GET checked state (true/false)
$("input").prop("checked", true);        // SET checked
$("input").prop("disabled", true);       // Disable input
```

### CSS and Classes

```javascript
// ─── CSS ───
$("p").css("color");                     // GET CSS property
$("p").css("color", "red");             // SET CSS property
$("p").css({                             // SET multiple CSS properties
    color: "red",
    fontSize: "18px",                    // camelCase
    "background-color": "#f0f0f0"        // or kebab-case in quotes
});

// ─── Classes ───
$("div").addClass("active");
$("div").removeClass("active");
$("div").toggleClass("active");          // Add if absent, remove if present
$("div").hasClass("active");             // Returns true/false

$("div").addClass("class1 class2");      // Multiple classes
```

### DOM Traversal

DOM traversal means **navigating from one element to related elements** — parents, children, siblings. This is essential because often you click a button inside a card and need to find the card title next to it, or you update a list item and need to affect its parent list. jQuery provides an intuitive set of methods for walking up the tree (`.parent()`, `.closest()`), down the tree (`.children()`, `.find()`), and sideways (`.siblings()`, `.next()`, `.prev()`).

`.closest()` is especially useful — it walks up the tree and finds the nearest ancestor matching a selector. For example, when handling a click on a delete button inside a table row, `$(this).closest("tr")` gives you the row the button is in.

```javascript
// ─── Parents ───
$("li").parent();                        // Direct parent
$("li").parents();                       // All ancestors
$("li").parents(".container");           // Specific ancestor
$("li").closest(".card");                // Nearest ancestor matching selector
$("li").parentsUntil(".container");      // Ancestors up to (not including) container

// ─── Children ───
$("ul").children();                      // Direct children
$("ul").children(".active");             // Filtered children
$("div").find("p");                      // All descendants matching selector
$("div").contents();                     // All children including text nodes

// ─── Siblings ───
$("li").siblings();                      // All siblings
$("li").next();                          // Next sibling
$("li").nextAll();                       // All next siblings
$("li").prev();                          // Previous sibling
$("li").prevAll();                       // All previous siblings

// ─── Filtering ───
$("li").first();                         // First matched element
$("li").last();                          // Last matched element
$("li").eq(2);                           // Element at index 2
$("li").filter(".active");               // Filter by selector
$("li").not(".active");                  // Exclude by selector
$("li").has("span");                     // Has descendant matching selector
$("li").is(".active");                   // Returns boolean
```

### Creating, Inserting, Removing Elements

Dynamic web pages need the ability to **add new elements** (like a new row in a table after filling a form), **remove elements** (like deleting a card when the user clicks X), and **move elements around** (like reordering a list). jQuery makes all of these operations simple and readable.

The key methods are: `.append()` / `.prepend()` for adding inside an element, `.after()` / `.before()` for adding next to an element, `.remove()` for deleting an element, `.clone()` for duplicating, and `.wrap()` for wrapping an element in a new container.

```javascript
// ─── Creating Elements ───
const $newDiv = $("<div>", {
    class: "card",
    id: "new-card",
    text: "Hello!",
    css: { color: "blue" }
});

// Short way
const $p = $("<p>New paragraph</p>");

// ─── Inserting ───
$("ul").append("<li>Last item</li>");           // Add as last child (INSIDE)
$("ul").prepend("<li>First item</li>");         // Add as first child (INSIDE)
$("<li>After</li>").appendTo("ul");             // Reverse of append

$("h1").after("<p>After the heading</p>");      // Add AFTER element (sibling)
$("h1").before("<p>Before the heading</p>");    // Add BEFORE element (sibling)
$("<p>Inserted</p>").insertAfter("h1");         // Reverse of after

// ─── Wrapping ───
$("p").wrap("<div class='wrapper'></div>");     // Wrap each element
$("p").wrapAll("<div class='wrapper'></div>");  // Wrap all elements together
$("p").unwrap();                                 // Remove parent wrapper

// ─── Removing ───
$("p").remove();                 // Remove element from DOM (removes events too)
$("p").detach();                 // Remove but keep events (for re-insertion)
$("div").empty();                // Remove all children
$("p").remove(".old");           // Remove elements matching selector

// ─── Replacing ───
$("h2").replaceWith("<h3>Replaced</h3>");
$("<h3>Replaced</h3>").replaceAll("h2");

// ─── Cloning ───
const $clone = $("div.card").clone(true);   // true = deep clone with events
$("body").append($clone);
```

### Event Handling

Events are things that happen on the page — clicks, key presses, form submissions, mouse movements, page scrolling. **Event handling** is writing code that runs in response to those events. jQuery's `.on()` method is the standard way to attach event handlers.

The most important concept is **event delegation**: instead of attaching a click handler to every single button on the page (which breaks when new buttons are added dynamically), you attach ONE handler to a parent element and let it catch events from its children as they bubble up. This is why `$("#list").on("click", "li", handler)` works for both existing and future `<li>` elements — the handler is on the `#list`, not on each `<li>`.

```javascript
// ─── Basic Event Binding ───
// Method 1: on() — recommended
$("button").on("click", function() {
    console.log("Clicked!");
    console.log($(this).text());    // 'this' is the clicked element
});

// Method 2: Shorthand methods
$("button").click(function() { console.log("Clicked!"); });
$("input").change(function() { console.log("Changed!"); });
$("form").submit(function(e) { e.preventDefault(); });
$("input").focus(function() { console.log("Focused!"); });
$("input").blur(function() { console.log("Lost focus!"); });
$("div").mouseenter(function() { $(this).css("background", "yellow"); });
$("div").mouseleave(function() { $(this).css("background", "white"); });
$(window).resize(function() { console.log("Window resized"); });
$(window).scroll(function() { console.log("Scrolling"); });

// ─── Multiple Events ───
$("input").on({
    focus: function() { $(this).addClass("focused"); },
    blur: function() { $(this).removeClass("focused"); },
    input: function() { console.log($(this).val()); }
});

// Same handler for multiple events
$("button").on("mouseenter mouseleave", function() {
    $(this).toggleClass("hovered");
});

// ─── Event Delegation (for dynamic elements) ───
// Attach to parent → works for current AND future children
$(document).on("click", ".dynamic-btn", function() {
    console.log("Clicked dynamic button!");
});

// Better: scope to nearest static parent
$("#list").on("click", "li", function() {
    console.log("Clicked:", $(this).text());
});

// ─── One-time Event ───
$("button").one("click", function() {
    console.log("This fires only ONCE");
});

// ─── Removing Events ───
function handler() { console.log("Click!"); }
$("button").on("click", handler);
$("button").off("click", handler);    // Remove specific handler
$("button").off("click");             // Remove all click handlers
$("button").off();                    // Remove ALL handlers

// ─── Event Object ───
$("a").on("click", function(e) {
    e.preventDefault();       // Prevent default action
    e.stopPropagation();      // Stop bubbling
    e.target;                 // DOM element that triggered event
    $(e.target);              // jQuery wrapped
    e.type;                   // "click"
    e.which;                  // Key/button code
    e.pageX;                  // Mouse X position
    e.pageY;                  // Mouse Y position
});

// ─── Trigger Events Programmatically ───
$("button").trigger("click");
$("form").trigger("submit");
$("input").trigger("focus");
```

---

## 4. jQuery Effects

Effects add **visual polish** to your page by animating transitions. Instead of elements appearing and disappearing instantly (which feels jarring), effects make them fade in, slide down, or animate smoothly. jQuery provides built-in effects for the most common animations and a general `.animate()` method for custom animations.

The three main categories are:
- **Show/Hide**: `show()`, `hide()`, `toggle()` — basic visibility
- **Fade**: `fadeIn()`, `fadeOut()`, `fadeToggle()` — opacity transitions
- **Slide**: `slideUp()`, `slideDown()`, `slideToggle()` — vertical sliding (great for accordions and menus)

All effect methods accept a duration (in milliseconds or "slow"/"fast") and an optional callback function that runs when the animation finishes.

```javascript
// ─── Show / Hide ───
$("div").hide();                     // Hide instantly
$("div").show();                     // Show instantly
$("div").toggle();                   // Toggle visibility

$("div").hide(400);                  // Hide with 400ms animation
$("div").show("slow");              // "slow" (600ms) / "fast" (200ms)
$("div").toggle(300, function() {
    console.log("Animation complete!");    // Callback
});

// ─── Fade ───
$("div").fadeIn(400);                // Fade in
$("div").fadeOut(400);               // Fade out
$("div").fadeToggle(400);            // Toggle fade
$("div").fadeTo(400, 0.5);          // Fade to 50% opacity

// ─── Slide ───
$("div").slideUp(400);              // Slide up (hide)
$("div").slideDown(400);            // Slide down (show)
$("div").slideToggle(400);          // Toggle slide

// ─── Custom Animation ───
$("div").animate({
    opacity: 0.5,
    left: "+=50px",        // Relative values
    height: "200px",
    fontSize: "24px"
}, 1000, "swing", function() {
    console.log("Animation done!");
});

// ─── Chain Animations ───
$("div")
    .animate({ left: "200px" }, 500)
    .animate({ top: "100px" }, 500)
    .fadeOut(300)
    .fadeIn(300);

// ─── Stop Animation ───
$("div").stop();                     // Stop current animation
$("div").stop(true);                 // Stop and clear queue
$("div").stop(true, true);          // Stop, clear queue, jump to end

// ─── Delay ───
$("div").fadeIn(300).delay(2000).fadeOut(300);  // Wait 2 seconds between
```

### Practical Example: Accordion

```javascript
$(function() {
    $(".accordion-header").on("click", function() {
        const $content = $(this).next(".accordion-content");
        
        // Close all other sections
        $(".accordion-content").not($content).slideUp(300);
        $(".accordion-header").not(this).removeClass("active");
        
        // Toggle this section
        $content.slideToggle(300);
        $(this).toggleClass("active");
    });
});
```

---

## 5. AJAX with jQuery

### What is AJAX?

**AJAX** (Asynchronous JavaScript And XML) is a technique that lets your web page communicate with a server **in the background, without reloading the entire page**. This is what makes modern web applications feel smooth and fast — when you like a post on social media, the page doesn't refresh; when you search on Google, suggestions appear as you type; when you add an item to your cart, the cart count updates instantly.

Before AJAX, every interaction required a full page reload. User clicks a button → entire page re-downloads from server → entire page re-renders. With AJAX, only the small piece of data you need is exchanged with the server, and JavaScript updates just the relevant part of the page.

Despite the name containing "XML," modern AJAX almost always uses **JSON** format for data exchange because JSON is lighter, simpler, and maps directly to JavaScript objects.

### Under the Hood: How AJAX Works

```
┌──────────┐        HTTP Request          ┌──────────┐
│          │  ──────────────────────────→  │          │
│  Browser │     (GET /api/users)         │  Server  │
│  (JS)    │                              │  (API)   │
│          │  ←──────────────────────────  │          │
└──────────┘     JSON Response            └──────────┘
                 {"users": [...]}

Traditional page load:  Browser → Server → Full HTML → Re-render entire page
AJAX:                   JS → Server → JSON data → Update ONLY what changed

The page NEVER refreshes with AJAX!
```

### jQuery AJAX Methods

jQuery provides several methods for making AJAX calls, from the full-featured `$.ajax()` to convenient shorthands like `$.get()` and `$.post()`. The `$.ajax()` method gives you complete control over every aspect of the request (URL, method, headers, data, timeout, success/error callbacks). The shorthands are quicker to write for simple requests.

The typical pattern is: send a request, handle success by updating the page with the returned data, handle errors by showing a message, and optionally show/hide a loading spinner. jQuery's AJAX methods return **Deferred objects** (similar to Promises), so you can use `.done()`, `.fail()`, and `.always()` for clean, chainable error handling.

#### `$.ajax()` — The Full-Featured Method

```javascript
// ─── GET Request ───
$.ajax({
    url: "https://api.example.com/users",
    method: "GET",                    // or type: "GET"
    dataType: "json",                 // Expected response type
    headers: {
        "Authorization": "Bearer token123"
    },
    success: function(data, textStatus, jqXHR) {
        console.log("Data:", data);
        console.log("Status:", textStatus);
    },
    error: function(jqXHR, textStatus, errorThrown) {
        console.error("Error:", textStatus, errorThrown);
        console.error("Status code:", jqXHR.status);
        console.error("Response:", jqXHR.responseText);
    },
    complete: function(jqXHR, textStatus) {
        console.log("Request complete (always runs)");
    },
    beforeSend: function(jqXHR) {
        $("#loading").show();         // Show loading spinner
    },
    timeout: 5000                     // 5 second timeout
});

// ─── POST Request ───
$.ajax({
    url: "https://api.example.com/users",
    method: "POST",
    contentType: "application/json",
    data: JSON.stringify({
        name: "Kunal",
        email: "kunal@example.com",
        age: 25
    }),
    success: function(response) {
        console.log("User created:", response);
    },
    error: function(jqXHR) {
        alert("Error: " + jqXHR.status);
    }
});

// ─── PUT Request ───
$.ajax({
    url: "https://api.example.com/users/1",
    method: "PUT",
    contentType: "application/json",
    data: JSON.stringify({ name: "Updated Name" }),
    success: function(response) {
        console.log("Updated:", response);
    }
});

// ─── DELETE Request ───
$.ajax({
    url: "https://api.example.com/users/1",
    method: "DELETE",
    success: function() {
        console.log("Deleted successfully");
    }
});
```

#### Shorthand Methods

```javascript
// ─── $.get() ───
$.get("https://api.example.com/users", function(data) {
    console.log(data);
});

// With parameters
$.get("https://api.example.com/users", { role: "admin", page: 1 }, function(data) {
    console.log(data);
});

// ─── $.post() ───
$.post("https://api.example.com/users", 
    { name: "Kunal", email: "kunal@example.com" },
    function(response) {
        console.log("Created:", response);
    }
);

// ─── $.getJSON() ───
$.getJSON("https://api.example.com/users", function(data) {
    console.log(data);    // Automatically parsed as JSON
});

// ─── .load() — Load HTML into an element ───
$("#result").load("page.html");              // Load entire page
$("#result").load("page.html #section1");    // Load specific section
$("#result").load("page.html", function(response, status, xhr) {
    if (status === "error") {
        console.error("Error loading page");
    }
});
```

#### Using Promises (Modern Approach)

```javascript
// jQuery AJAX returns a Deferred object (like a Promise)

// .done(), .fail(), .always()
$.get("https://api.example.com/users")
    .done(function(data) {
        console.log("Success:", data);
    })
    .fail(function(jqXHR, textStatus, error) {
        console.error("Error:", error);
    })
    .always(function() {
        console.log("Complete");
    });

// ─── Multiple AJAX calls in parallel ───
$.when(
    $.get("https://api.example.com/users"),
    $.get("https://api.example.com/orders"),
    $.get("https://api.example.com/products")
).done(function(usersResponse, ordersResponse, productsResponse) {
    // Each response is an array: [data, textStatus, jqXHR]
    const users = usersResponse[0];
    const orders = ordersResponse[0];
    const products = productsResponse[0];
    
    console.log("All data loaded:", users, orders, products);
}).fail(function() {
    console.error("One or more requests failed");
});
```

### AJAX Global Event Handlers

Global AJAX handlers fire for **every AJAX request** on the page, which is perfect for cross-cutting concerns like showing/hiding loading spinners, global error logging, or adding authentication headers. Instead of adding loading spinner code to every individual AJAX call, you set it up once with `$(document).ajaxStart()` and `$(document).ajaxStop()` and it applies everywhere automatically.

```javascript
// These fire for ALL AJAX requests on the page

// Show loading spinner for every AJAX call
$(document).ajaxStart(function() {
    $("#loading-spinner").show();
});

$(document).ajaxStop(function() {
    $("#loading-spinner").hide();
});

$(document).ajaxError(function(event, jqXHR, settings, error) {
    console.error("AJAX Error on:", settings.url, error);
});
```

### AJAX Setup (Global Defaults)

`$.ajaxSetup()` lets you set **default options** that apply to all AJAX calls. This avoids repeating the same headers, content type, or timeout in every single request. For example, if every API call needs an `Authorization` header, set it once in `$.ajaxSetup()` and all subsequent `$.get()` and `$.post()` calls will include it automatically.

```javascript
// Set defaults for all AJAX requests
$.ajaxSetup({
    headers: {
        "Authorization": "Bearer " + getToken(),
        "X-Custom-Header": "value"
    },
    contentType: "application/json",
    timeout: 10000,
    beforeSend: function() {
        console.log("Request starting...");
    }
});

// Now all $.ajax calls inherit these settings
$.get("/api/users");    // Will include the headers automatically
```

---

## 6. Complete Practical Example: CRUD App

This example puts everything together — jQuery selectors, DOM manipulation, event handling (with delegation), AJAX calls, and Bootstrap styling — to build a complete **CRUD** (Create, Read, Update, Delete) user management application. It demonstrates the real-world pattern of fetching data from an API, displaying it in a table, adding new items, editing existing items, and deleting items — all without page reloads.

The key patterns to notice:
- **Event delegation** (`$("#userTable").on("click", ".btn-delete", ...)`) handles clicks on buttons that are created dynamically
- **`$.each()`** iterates over the API response to build table rows
- **Template literals** (backtick strings) make it easy to build HTML with dynamic data
- **Loading spinner** shows while data is being fetched

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQuery CRUD App</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" 
          rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <h1>User Management</h1>
        
        <!-- Add User Form -->
        <form id="userForm" class="mb-4">
            <div class="row g-3">
                <div class="col-md-4">
                    <input type="text" class="form-control" id="userName" 
                           placeholder="Name" required>
                </div>
                <div class="col-md-4">
                    <input type="email" class="form-control" id="userEmail" 
                           placeholder="Email" required>
                </div>
                <div class="col-auto">
                    <button type="submit" class="btn btn-primary">Add User</button>
                </div>
            </div>
        </form>

        <!-- Loading Spinner -->
        <div id="loading" class="text-center d-none">
            <div class="spinner-border text-primary"></div>
            <p>Loading...</p>
        </div>

        <!-- Users Table -->
        <table class="table table-striped">
            <thead>
                <tr>
                    <th>ID</th>
                    <th>Name</th>
                    <th>Email</th>
                    <th>Actions</th>
                </tr>
            </thead>
            <tbody id="userTable">
                <!-- Rows will be inserted here via AJAX -->
            </tbody>
        </table>
    </div>

    <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js">
    </script>
    <script>
    $(function() {
        const API_URL = "https://jsonplaceholder.typicode.com/users";

        // ─── LOAD USERS (GET) ───
        function loadUsers() {
            $("#loading").removeClass("d-none");
            
            $.ajax({
                url: API_URL,
                method: "GET",
                success: function(users) {
                    const $tbody = $("#userTable").empty();
                    
                    $.each(users, function(index, user) {
                        $tbody.append(`
                            <tr data-id="${user.id}">
                                <td>${user.id}</td>
                                <td>${user.name}</td>
                                <td>${user.email}</td>
                                <td>
                                    <button class="btn btn-sm btn-warning btn-edit">Edit</button>
                                    <button class="btn btn-sm btn-danger btn-delete">Delete</button>
                                </td>
                            </tr>
                        `);
                    });
                },
                error: function(jqXHR) {
                    alert("Failed to load users: " + jqXHR.statusText);
                },
                complete: function() {
                    $("#loading").addClass("d-none");
                }
            });
        }

        // ─── ADD USER (POST) ───
        $("#userForm").on("submit", function(e) {
            e.preventDefault();
            
            const userData = {
                name: $("#userName").val(),
                email: $("#userEmail").val()
            };

            $.ajax({
                url: API_URL,
                method: "POST",
                contentType: "application/json",
                data: JSON.stringify(userData),
                success: function(newUser) {
                    console.log("User created:", newUser);
                    
                    $("#userTable").append(`
                        <tr data-id="${newUser.id}">
                            <td>${newUser.id}</td>
                            <td>${newUser.name}</td>
                            <td>${newUser.email}</td>
                            <td>
                                <button class="btn btn-sm btn-warning btn-edit">Edit</button>
                                <button class="btn btn-sm btn-danger btn-delete">Delete</button>
                            </td>
                        </tr>
                    `);
                    
                    // Reset form
                    $("#userForm")[0].reset();
                },
                error: function() {
                    alert("Failed to create user");
                }
            });
        });

        // ─── DELETE USER (DELETE) — Event Delegation ───
        $("#userTable").on("click", ".btn-delete", function() {
            const $row = $(this).closest("tr");
            const userId = $row.data("id");

            if (confirm("Are you sure you want to delete this user?")) {
                $.ajax({
                    url: `${API_URL}/${userId}`,
                    method: "DELETE",
                    success: function() {
                        $row.fadeOut(300, function() {
                            $(this).remove();
                        });
                    },
                    error: function() {
                        alert("Failed to delete user");
                    }
                });
            }
        });

        // ─── EDIT USER (PUT) ───
        $("#userTable").on("click", ".btn-edit", function() {
            const $row = $(this).closest("tr");
            const userId = $row.data("id");
            const currentName = $row.find("td:eq(1)").text();
            
            const newName = prompt("Enter new name:", currentName);
            
            if (newName && newName !== currentName) {
                $.ajax({
                    url: `${API_URL}/${userId}`,
                    method: "PUT",
                    contentType: "application/json",
                    data: JSON.stringify({ name: newName }),
                    success: function(updated) {
                        $row.find("td:eq(1)").text(newName);
                        $row.css("background-color", "#d4edda")
                            .delay(1000)
                            .queue(function(next) {
                                $(this).css("background-color", "");
                                next();
                            });
                    },
                    error: function() {
                        alert("Failed to update user");
                    }
                });
            }
        });

        // Initial load
        loadUsers();
    });
    </script>
</body>
</html>
```

---

## 7. jQuery vs Vanilla JavaScript Quick Reference

With modern JavaScript (ES6+), many things that used to require jQuery can now be done natively. `document.querySelector()` replaces `$()`, `element.classList.add()` replaces `$el.addClass()`, `fetch()` replaces `$.ajax()`, and so on. For new projects, vanilla JavaScript is generally preferred. However, jQuery is still worth knowing because millions of existing websites and codebases use it, and understanding it helps you appreciate what modern JavaScript provides natively.

| Task | jQuery | Vanilla JS (Modern) |
|------|--------|---------------------|
| Select by ID | `$("#id")` | `document.getElementById("id")` |
| Select by class | `$(".class")` | `document.querySelectorAll(".class")` |
| Add class | `$el.addClass("x")` | `el.classList.add("x")` |
| Remove class | `$el.removeClass("x")` | `el.classList.remove("x")` |
| Set text | `$el.text("hi")` | `el.textContent = "hi"` |
| Set HTML | `$el.html("<b>hi</b>")` | `el.innerHTML = "<b>hi</b>"` |
| Set CSS | `$el.css("color", "red")` | `el.style.color = "red"` |
| Click event | `$el.on("click", fn)` | `el.addEventListener("click", fn)` |
| AJAX GET | `$.get(url, fn)` | `fetch(url).then(r => r.json())` |
| Hide | `$el.hide()` | `el.style.display = "none"` |
| Show | `$el.show()` | `el.style.display = ""` |
| Each | `$.each(arr, fn)` | `arr.forEach(fn)` |
| Append | `$parent.append(html)` | `parent.insertAdjacentHTML("beforeend", html)` |
| Ready | `$(function(){})` | `document.addEventListener("DOMContentLoaded", fn)` |

---

## 8. Quick Reference

```
jQuery Core:     $() → Select, create, wrap elements
DOM:             .text() .html() .val() .attr() .css() .addClass()
Traversal:       .parent() .children() .find() .siblings() .closest()
Manipulation:    .append() .prepend() .after() .before() .remove() .clone()
Events:          .on() .off() .trigger() .one() — use event delegation for dynamic elements
Effects:         .hide() .show() .fadeIn() .fadeOut() .slideToggle() .animate()
AJAX:            $.ajax() $.get() $.post() $.getJSON() .load()
Utilities:       $.each() $.extend() $.trim() $.isArray() $.inArray()
```
