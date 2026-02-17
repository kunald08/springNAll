# jQuery & AJAX — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is jQuery? Why was it created?

**Answer:**
jQuery is a fast, lightweight **JavaScript library** created in 2006 by John Resig. It was created to solve a major pain point at the time — **browser inconsistency**. The same JavaScript code would behave differently in Internet Explorer, Firefox, Chrome, and Safari.

jQuery provided a single API that worked consistently across all browsers. It also made common tasks much simpler:
- DOM selection: `$("#id")` instead of `document.getElementById("id")`
- Event handling: `.on("click", fn)` with cross-browser support
- AJAX: `$.ajax()` instead of verbose `XMLHttpRequest` setup
- Animations: `.fadeIn()`, `.slideToggle()` with one line

While jQuery is considered **legacy** now (modern browsers are consistent, and vanilla JS has improved), it's still used in millions of existing websites and many enterprise applications. Understanding it is important for maintaining legacy codebases.

---

## 2. How do you include jQuery in a project?

**Answer:**
Two main ways:

**CDN (most common):**
```html
<script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
```

**npm (for projects with build tools):**
```bash
npm install jquery
```

The jQuery script must be loaded **before** any code that uses it. I put it at the end of `<body>` or use `defer`. The `.min.js` version is the minified (production) version — smaller file size.

---

## 3. What does `$(document).ready()` do? Why is it needed?

**Answer:**
`$(document).ready()` ensures my jQuery code runs only **after the DOM is fully loaded** — meaning all HTML elements are available for manipulation. Without it, jQuery might try to select elements that don't exist yet in the DOM.

```javascript
$(document).ready(function() {
    // Safe to manipulate DOM here
});

// Shorthand (same thing):
$(function() {
    // DOM is ready
});
```

It's similar to `document.addEventListener("DOMContentLoaded", fn)` in vanilla JS.

The difference from `window.onload`: `$(document).ready()` fires when the DOM tree is built (HTML parsed), while `window.onload` waits for ALL resources (images, stylesheets, iframes) to load. So `ready` fires earlier, which is usually what I want.

---

## 4. What does the `$` symbol mean in jQuery?

**Answer:**
`$` is simply an **alias for the `jQuery` function**. They are identical — `$("p")` and `jQuery("p")` do the exact same thing.

`$` is used because it's short and easy to type. If there's a conflict with another library that also uses `$`, I can use `jQuery.noConflict()`:

```javascript
jQuery.noConflict();
jQuery(function() {
    jQuery("p").hide();    // Use 'jQuery' instead of '$'
});
```

Or I can scope it:
```javascript
jQuery.noConflict();
(function($) {
    $("p").hide();         // $ works inside this scope
})(jQuery);
```

---

## 5. Explain jQuery selectors. How are they similar to CSS selectors?

**Answer:**
jQuery selectors work exactly like **CSS selectors** — which makes them intuitive if you know CSS:

- `$("p")` — all `<p>` elements (element selector)
- `$(".card")` — all elements with class "card"
- `$("#title")` — element with id "title"
- `$("div.card")` — `<div>` elements with class "card"
- `$("ul > li")` — direct child `<li>` elements of `<ul>`
- `$("input[type='text']")` — input elements with type text
- `$("li:first")` — first `<li>` element
- `$("li:nth-child(2)")` — second `<li>` element
- `$("p:contains('Hello')")` — `<p>` elements containing "Hello"
- `$(":input")` — all form input elements

The `$()` function returns a **jQuery object** (not a raw DOM element), which gives me access to all jQuery methods. If I need the raw DOM element, I use `[0]` or `.get(0)`.

---

## 6. How do you manipulate the DOM with jQuery?

**Answer:**
jQuery makes DOM manipulation much simpler than vanilla JS:

**Reading/Setting content:**
- `.text()` — get/set text content
- `.html()` — get/set HTML content
- `.val()` — get/set form input values
- `.attr("href")` — get/set attributes
- `.css("color", "red")` — get/set CSS properties

**Adding/Removing elements:**
- `.append()` — add inside at the end
- `.prepend()` — add inside at the beginning
- `.after()` — add as sibling after
- `.before()` — add as sibling before
- `.remove()` — delete the element
- `.empty()` — remove all children

**CSS classes:**
- `.addClass("active")` — add a class
- `.removeClass("active")` — remove a class
- `.toggleClass("active")` — toggle a class
- `.hasClass("active")` — check if has class (returns boolean)

One powerful feature is **chaining** — since most methods return the jQuery object, I can chain multiple operations: `$("p").addClass("bold").css("color", "red").fadeIn(500)`.

---

## 7. How does event handling work in jQuery?

**Answer:**
jQuery uses `.on()` as the primary method for attaching event handlers:

```javascript
// Click event
$("button").on("click", function() {
    console.log("Clicked!");
    console.log($(this).text());    // 'this' is the element
});

// Shorthand (still works)
$("button").click(function() { ... });

// Multiple events
$("input").on({
    focus: function() { $(this).addClass("focused"); },
    blur: function() { $(this).removeClass("focused"); }
});

// Remove event
$("button").off("click");

// One-time event
$("button").one("click", function() {
    console.log("Only fires once");
});
```

The most important pattern is **event delegation** — attaching a handler to a parent for dynamic child elements:
```javascript
$(document).on("click", ".dynamic-btn", function() {
    console.log("Works for dynamically added buttons too!");
});
```

This works because events **bubble up** from the child to the parent.

---

## 8. What is event delegation in jQuery? Why is it important?

**Answer:**
Event delegation means I attach an event listener to a **parent element** instead of each individual child. When a child triggers an event, it bubbles up to the parent where the handler catches it.

```javascript
// Instead of this (doesn't work for elements added later):
$("li").on("click", function() { ... });

// Use delegation (works for current AND future elements):
$("ul").on("click", "li", function() {
    console.log($(this).text());
});
```

Why it's important:
1. **Dynamic content** — if I add new `<li>` elements via AJAX after the page loads, delegated events still work. Direct binding does NOT.
2. **Performance** — one listener on the parent vs. hundreds on individual children.
3. **Memory** — fewer event listeners means less memory usage.

The syntax is: `$(parentElement).on(event, childSelector, handler)`.

---

## 9. What are jQuery effects/animations?

**Answer:**
jQuery provides built-in animation methods:

**Show/Hide:**
- `.hide(speed)` / `.show(speed)` / `.toggle(speed)`

**Fading:**
- `.fadeIn(speed)` / `.fadeOut(speed)` / `.fadeToggle(speed)` / `.fadeTo(speed, opacity)`

**Sliding:**
- `.slideUp(speed)` / `.slideDown(speed)` / `.slideToggle(speed)`

**Custom Animation:**
```javascript
$("div").animate({
    left: "200px",
    opacity: 0.5,
    fontSize: "24px"
}, 1000, function() {
    console.log("Animation complete!");
});
```

Speed can be: milliseconds (`400`), `"slow"` (600ms), or `"fast"` (200ms). All methods accept a **callback function** that runs after the animation finishes.

I can chain animations — they queue up and run sequentially:
```javascript
$("div").slideUp(300).slideDown(300).fadeOut(300).fadeIn(300);
```

`.stop()` halts the current animation to prevent queue buildup.

---

## 10. What is AJAX? Why is it important?

**Answer:**
AJAX stands for **Asynchronous JavaScript And XML**. It's a technique for making HTTP requests to a server **without reloading the entire web page**. Only the data we need is fetched and the page is updated dynamically.

Why it's important:
- **Better user experience** — no full page reloads, the page feels responsive and fast (like a desktop app).
- **Reduced server load** — only send/receive the data needed, not the entire HTML page.
- **Real-time updates** — I can fetch new data (like notifications, live scores) without interrupting what the user is doing.

Despite the name, modern AJAX almost always uses **JSON** (not XML) as the data format. The name stuck for historical reasons.

Examples of AJAX in action: Google's search suggestions as you type, loading more posts on social media when you scroll, submitting a form without page refresh.

---

## 11. How do you make AJAX requests with jQuery?

**Answer:**
jQuery provides several methods:

**`$.ajax()` — the full control method:**
```javascript
$.ajax({
    url: "/api/users",
    method: "GET",
    dataType: "json",
    success: function(data) {
        console.log(data);
    },
    error: function(jqXHR, textStatus, error) {
        console.error("Error:", error);
    },
    complete: function() {
        console.log("Done (success or fail)");
    }
});
```

**Shorthand methods:**
```javascript
// GET
$.get("/api/users", function(data) { console.log(data); });

// POST
$.post("/api/users", { name: "Kunal" }, function(response) { console.log(response); });

// GET JSON (auto-parses)
$.getJSON("/api/users", function(data) { console.log(data); });

// Load HTML into an element
$("#result").load("page.html");
```

For sending JSON in a POST request, I set `contentType` and stringify the data:
```javascript
$.ajax({
    url: "/api/users",
    method: "POST",
    contentType: "application/json",
    data: JSON.stringify({ name: "Kunal", age: 25 }),
    success: function(response) { console.log(response); }
});
```

---

## 12. What are the different `$.ajax()` options you commonly use?

**Answer:**
The most important options:

- **`url`** — the endpoint to call
- **`method`** (or `type`) — HTTP method: GET, POST, PUT, DELETE
- **`data`** — data to send (for POST/PUT)
- **`dataType`** — expected response format: "json", "html", "text", "xml"
- **`contentType`** — what I'm sending: "application/json" for JSON payloads
- **`headers`** — custom HTTP headers (like Authorization tokens)
- **`success(data, status, xhr)`** — callback on success
- **`error(xhr, status, error)`** — callback on failure
- **`complete(xhr, status)`** — callback that ALWAYS runs (like `finally`)
- **`beforeSend(xhr)`** — runs before the request (good for showing loading spinners)
- **`timeout`** — max wait time in milliseconds

```javascript
$.ajax({
    url: "/api/data",
    method: "GET",
    headers: { "Authorization": "Bearer token123" },
    timeout: 5000,
    beforeSend: function() { $("#loading").show(); },
    success: function(data) { renderData(data); },
    error: function(xhr) { alert("Error: " + xhr.status); },
    complete: function() { $("#loading").hide(); }
});
```

---

## 13. How do you handle AJAX errors?

**Answer:**
I handle errors at three levels:

**1. Per-request error callback:**
```javascript
$.ajax({
    url: "/api/users",
    error: function(jqXHR, textStatus, errorThrown) {
        if (jqXHR.status === 404) {
            alert("Resource not found");
        } else if (jqXHR.status === 500) {
            alert("Server error");
        } else if (textStatus === "timeout") {
            alert("Request timed out");
        }
    }
});
```

**2. Promise-based `.fail()`:**
```javascript
$.get("/api/users")
    .done(function(data) { console.log(data); })
    .fail(function(jqXHR) { console.error("Failed:", jqXHR.status); })
    .always(function() { console.log("Complete"); });
```

**3. Global AJAX error handler (catches ALL AJAX errors):**
```javascript
$(document).ajaxError(function(event, jqXHR, settings, error) {
    console.error("AJAX Error on " + settings.url + ": " + error);
});
```

I always check the HTTP status code in the error handler to provide meaningful error messages to the user.

---

## 14. What is the difference between `$.ajax()`, `$.get()`, `$.post()`, and `$.getJSON()`?

**Answer:**
They're all AJAX methods, but with different levels of control:

- **`$.ajax()`** — the most powerful, fully configurable. I can set method, headers, content type, timeout, etc. Use when I need full control.

- **`$.get(url, data, callback)`** — shorthand for a GET request. Simpler but less configurable.

- **`$.post(url, data, callback)`** — shorthand for a POST request.

- **`$.getJSON(url, callback)`** — shorthand for a GET request that automatically parses the response as JSON.

Under the hood, `$.get()`, `$.post()`, and `$.getJSON()` all call `$.ajax()` internally with preset options. They're convenience methods.

My rule: use `$.ajax()` for anything complex (custom headers, error handling, timeouts). Use shorthand methods for quick, simple requests.

---

## 15. How do you send form data via AJAX?

**Answer:**
Two common approaches:

**1. Serialize the form:**
```javascript
$("#myForm").on("submit", function(e) {
    e.preventDefault();    // Prevent page reload
    
    $.ajax({
        url: "/api/submit",
        method: "POST",
        data: $(this).serialize(),    // "name=Kunal&email=kunal@test.com"
        success: function(response) {
            alert("Submitted!");
        }
    });
});
```

`.serialize()` converts form inputs into a URL-encoded string. `.serializeArray()` returns an array of objects.

**2. Send as JSON:**
```javascript
const formData = {
    name: $("#name").val(),
    email: $("#email").val()
};

$.ajax({
    url: "/api/submit",
    method: "POST",
    contentType: "application/json",
    data: JSON.stringify(formData),
    success: function(response) { console.log(response); }
});
```

I always call `e.preventDefault()` on form submit to prevent the default full-page form submission.

---

## 16. What is `$.when()`? How do you make multiple AJAX calls in parallel?

**Answer:**
`$.when()` runs multiple AJAX calls simultaneously and waits for ALL of them to complete:

```javascript
$.when(
    $.get("/api/users"),
    $.get("/api/products"),
    $.get("/api/orders")
).done(function(usersRes, productsRes, ordersRes) {
    // Each response is [data, textStatus, jqXHR]
    const users = usersRes[0];
    const products = productsRes[0];
    const orders = ordersRes[0];
    
    console.log("All data loaded!");
}).fail(function() {
    console.error("One or more requests failed");
});
```

This is similar to `Promise.all()` in vanilla JS. It's useful when I need data from multiple endpoints before rendering the page, and the requests don't depend on each other.

If ANY request fails, `.fail()` is called. `.done()` only fires if ALL succeed.

---

## 17. What are the global AJAX event handlers in jQuery?

**Answer:**
These handlers fire for EVERY AJAX request on the page — great for cross-cutting concerns like loading indicators:

```javascript
// Show loading spinner for ALL AJAX calls
$(document).ajaxStart(function() {
    $("#loading-spinner").show();
});

$(document).ajaxStop(function() {
    $("#loading-spinner").hide();
});

// Log all AJAX errors
$(document).ajaxError(function(event, jqXHR, settings) {
    console.error("Failed: " + settings.url);
});

// Run before every AJAX request
$(document).ajaxSend(function(event, jqXHR, settings) {
    jqXHR.setRequestHeader("X-Custom-Token", getToken());
});

// Run after every successful AJAX request
$(document).ajaxSuccess(function(event, jqXHR, settings) {
    console.log("Success: " + settings.url);
});
```

I use `ajaxStart`/`ajaxStop` in most projects for a global loading indicator, and `ajaxError` for global error handling.

---

## 18. What is the difference between jQuery AJAX and the Fetch API?

**Answer:**
| Feature | jQuery `$.ajax()` | Fetch API |
|---------|-------------------|-----------|
| Library needed | Yes (jQuery) | No (built-in) |
| Returns | jQuery Deferred | Native Promise |
| Error handling | Rejects on HTTP errors (404, 500) | Does NOT reject on HTTP errors, only on network failure |
| JSON parsing | Auto-parse with `dataType: "json"` | Manual: `response.json()` |
| Old browser support | Yes (IE6+) | No (needs polyfill for old browsers) |
| Request cancellation | Via `jqXHR.abort()` | Via `AbortController` |
| Upload progress | Supported | Supported |

Key difference: jQuery's `$.ajax()` treats HTTP errors (404, 500) as errors and calls the error callback. Fetch does NOT — it only rejects on network failures. With Fetch, I must check `response.ok` manually.

For new projects, I use the **Fetch API** or **axios**. For legacy jQuery projects, I stick with `$.ajax()`.

---

## 19. What is `$.ajaxSetup()`? When would you use it?

**Answer:**
`$.ajaxSetup()` sets **default options** for ALL subsequent AJAX calls:

```javascript
$.ajaxSetup({
    headers: {
        "Authorization": "Bearer " + token,
        "Content-Type": "application/json"
    },
    timeout: 10000,
    beforeSend: function() { $("#spinner").show(); },
    complete: function() { $("#spinner").hide(); }
});

// Now every $.get(), $.post(), $.ajax() inherits these settings
$.get("/api/users");    // Automatically includes the Authorization header
```

I use it when every AJAX call needs the same headers (like auth tokens) or the same error handling. It keeps code DRY — I don't have to repeat the same options in every call.

However, I use it carefully — it's a global setting so it affects ALL AJAX calls, which can cause unexpected behavior if I forget it's set.

---

## 20. Walk me through a practical AJAX workflow — loading and displaying data.

**Answer:**
Here's a real-world scenario — fetching users from an API and displaying them in a table:

```javascript
$(function() {
    const API = "https://jsonplaceholder.typicode.com/users";

    // Show loading, fetch data, render it
    function loadUsers() {
        $.ajax({
            url: API,
            method: "GET",
            beforeSend: function() {
                $("#userTable").html('<tr><td colspan="3">Loading...</td></tr>');
            },
            success: function(users) {
                let html = "";
                $.each(users, function(i, user) {
                    html += `<tr>
                        <td>${user.name}</td>
                        <td>${user.email}</td>
                        <td><button class="btn-delete" data-id="${user.id}">Delete</button></td>
                    </tr>`;
                });
                $("#userTable").html(html);
            },
            error: function(xhr) {
                $("#userTable").html('<tr><td colspan="3">Failed to load data</td></tr>');
            }
        });
    }

    // Event delegation for delete buttons (dynamic elements)
    $("#userTable").on("click", ".btn-delete", function() {
        const $row = $(this).closest("tr");
        const userId = $(this).data("id");
        
        $.ajax({
            url: API + "/" + userId,
            method: "DELETE",
            success: function() {
                $row.fadeOut(300, function() { $(this).remove(); });
            }
        });
    });

    loadUsers();
});
```

Key patterns demonstrated:
1. Loading indicator in `beforeSend`
2. `$.each()` to iterate data
3. Template literals for HTML generation
4. **Event delegation** for dynamically created delete buttons
5. Fade animation before removing the row
6. Error handling with user-friendly message
