# Django Framework — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What is Django? Why would you choose it?

**Answer:**
Django is a **high-level Python web framework** that follows the "batteries included" philosophy — it provides almost everything I need out of the box to build web applications quickly and securely.

I'd choose Django when I need to build a **full-featured web application** fast because it includes:
- An **ORM** to talk to databases in Python (no raw SQL)
- An auto-generated **admin panel** for content management
- Built-in **user authentication** (login, registration, permissions)
- **Form handling** and validation
- **Template engine** for HTML generation
- **Security protections** — CSRF, XSS, SQL injection, clickjacking — all built-in
- **URL routing and middleware**

Major companies like Instagram, Pinterest, Mozilla, and Disqus use Django in production. I'd pick Django for medium-to-large applications; for small standalone APIs or microservices, I might consider Flask or FastAPI instead.

---

## 2. What is the MVT pattern? How is it different from MVC?

**Answer:**
Django follows the **MVT (Model-View-Template)** pattern, which is Django's version of the classic MVC:

- **Model** — defines the data structure (database tables). Each model maps to a database table through the ORM.
- **View** — handles the **business logic**. It receives HTTP requests, queries the database using models, and returns HTTP responses.
- **Template** — handles the **presentation layer**. HTML files with Django template language for dynamic content.

The key difference from traditional MVC: what MVC calls a "Controller" is **split in Django**. The URL dispatcher (urls.py) acts as the routing portion of the controller, while the View handles the logic. Django's "View" is closest to MVC's "Controller," and Django's "Template" is closest to MVC's "View."

The flow: **Browser → URL Dispatcher → View → (Model + Template) → HTTP Response → Browser**

---

## 3. Explain the Django project structure.

**Answer:**
When I run `django-admin startproject myproject`, Django creates:

```
myproject/
├── manage.py              # CLI tool for running commands (runserver, migrate, etc.)
└── myproject/             # Project settings package
    ├── __init__.py        # Makes it a Python package
    ├── settings.py        # All project configuration (DB, apps, middleware, etc.)
    ├── urls.py            # Root URL routing
    ├── asgi.py            # ASGI entry point (async deployment)
    └── wsgi.py            # WSGI entry point (production deployment)
```

Then for each feature, I create an **app**: `python manage.py startapp blog`

```
blog/
├── models.py      # Database models
├── views.py       # Request handling logic
├── urls.py        # App-level URL routing (I create this)
├── admin.py       # Admin panel customization
├── apps.py        # App configuration
├── tests.py       # Unit tests
└── migrations/    # Database migration files
```

A Django project is made of **multiple apps**, each responsible for a distinct feature. Apps are designed to be reusable across projects.

---

## 4. What is a Django App? How is it different from a Project?

**Answer:**
A **project** is the entire web application — it contains settings, URL configurations, and orchestrates multiple apps. There's only **one project**.

An **app** is a self-contained module that handles a specific feature — like `blog`, `users`, `payments`. There can be **many apps** within a project.

```python
# settings.py — registering apps
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'blog',           # My custom app
    'accounts',       # Another custom app
]
```

The philosophy is: each app should do **one thing well**. An app can potentially be reused in another project. For example, Django's built-in `auth` app handles user authentication and is shared across all Django projects.

I create an app with `python manage.py startapp appname` and must register it in `INSTALLED_APPS`.

---

## 5. What are Django Models? How do they map to database tables?

**Answer:**
A Django model is a **Python class** that represents a database table. Each attribute becomes a **column**, and each instance becomes a **row**:

```python
from django.db import models

class Article(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    published = models.DateTimeField(auto_now_add=True)
    author = models.ForeignKey('auth.User', on_delete=models.CASCADE)

    def __str__(self):
        return self.title
```

This creates a table with columns: `id` (auto-generated primary key), `title`, `content`, `published`, and `author_id` (foreign key).

Django supports all common field types — `CharField`, `IntegerField`, `BooleanField`, `DateTimeField`, `ForeignKey`, `ManyToManyField`, etc. Relationships are defined using `ForeignKey` (one-to-many), `OneToOneField`, and `ManyToManyField`.

The beauty is I never write SQL to create or modify tables — Django's migration system handles that.

---

## 6. What are migrations in Django?

**Answer:**
Migrations are Django's way of **propagating model changes to the database schema**. They're version control for the database.

The workflow is two commands:

```bash
python manage.py makemigrations    # Detects model changes → creates migration files
python manage.py migrate           # Applies migration files → updates the database
```

When I add a field, change a type, or modify a relationship in my model, I run `makemigrations` and Django generates a migration file (Python code) describing the changes. Then `migrate` executes those changes against the database.

Key points:
- Migrations are **incremental** — each one builds on the previous
- They're **reversible** — I can roll back with `python manage.py migrate app_name 0003`
- They should be **committed to version control** so all developers and environments stay in sync
- I can view the SQL a migration would run: `python manage.py sqlmigrate app_name 0001`

---

## 7. What is the Django ORM? How do you query the database?

**Answer:**
The Django ORM (Object-Relational Mapper) lets me interact with the database using **Python code** instead of raw SQL. Every model gets a `Manager` (default is `objects`) that provides query methods:

```python
# CREATE
article = Article.objects.create(title="Hello", content="World")

# READ
all_articles = Article.objects.all()                          # SELECT *
article = Article.objects.get(id=1)                           # Single object
recent = Article.objects.filter(published__gte='2024-01-01')  # WHERE clause
first = Article.objects.first()                               # First record

# UPDATE
Article.objects.filter(id=1).update(title="New Title")

# DELETE
Article.objects.filter(id=1).delete()
```

The ORM supports **field lookups** using double underscores: `title__contains="hello"`, `price__gte=10`, `author__name="Alice"` (following relationships).

**QuerySets are lazy** — they don't hit the database until they're evaluated (iterated, sliced, or converted). This allows chaining: `Article.objects.filter(...).exclude(...).order_by(...)` builds one efficient SQL query.

---

## 8. What is a QuerySet? What does "lazy evaluation" mean?

**Answer:**
A QuerySet is a collection of database queries represented as a Python object. It represents a **SQL query** that hasn't been executed yet.

**Lazy evaluation** means the QuerySet doesn't actually hit the database when it's created — it only executes the SQL when the data is actually **needed**:

```python
# No database query yet — just building the QuerySet
qs = Article.objects.filter(status="published").order_by("-date")

# Database is hit NOW when we actually use the data
for article in qs:         # Iteration triggers the query
    print(article.title)
```

QuerySets are also **chainable** — I can keep adding filters, ordering, and other operations, and Django combines them into a single optimized SQL query.

This is important for **performance**: I can build complex queries step by step without worrying about multiple database round-trips. The QuerySet is cached after the first evaluation, so iterating over it a second time doesn't hit the database again.

---

## 9. What is the Django Admin? Why is it powerful?

**Answer:**
Django Admin is an **auto-generated web interface** for managing application data. By simply registering a model, I get a fully functional CRUD interface:

```python
# admin.py
from django.contrib import admin
from .models import Article

@admin.register(Article)
class ArticleAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'published']
    list_filter = ['published', 'author']
    search_fields = ['title', 'content']
    prepopulated_fields = {'slug': ('title',)}
```

With just a few lines, I get:
- List view with **sorting**, **filtering**, **search**, and **pagination**
- Detail forms for **creating and editing** records
- **User permissions** (who can add, change, delete)
- **Bulk actions** (delete multiple records at once)
- **Inline editing** of related models

It's powerful because it saves weeks of development for internal tools. Many companies use the Django Admin as their primary **content management system** without building a custom admin panel. I can also customize it extensively with custom views, actions, and templates.

---

## 10. What are Django Views? What types are there?

**Answer:**
A view is a Python function or class that takes an **HTTP request** and returns an **HTTP response**. It contains the business logic — querying data, processing forms, calling services.

**Function-Based Views (FBVs):**
```python
from django.shortcuts import render

def article_list(request):
    articles = Article.objects.all()
    return render(request, 'articles/list.html', {'articles': articles})
```

**Class-Based Views (CBVs):**
```python
from django.views.generic import ListView

class ArticleListView(ListView):
    model = Article
    template_name = 'articles/list.html'
    context_object_name = 'articles'
```

FBVs are **simpler and more explicit** — I use them for unique or simple logic. CBVs are **more powerful** — they provide mixins and inheritance for common patterns like listing, creating, updating, and deleting. Django ships with generic CBVs: `ListView`, `DetailView`, `CreateView`, `UpdateView`, `DeleteView`.

In practice, I use CBVs for standard CRUD operations and FBVs when the logic is custom or doesn't fit a generic pattern.

---

## 11. How does URL routing work in Django?

**Answer:**
Django uses a URL dispatcher that maps **URL patterns** to **views**:

```python
# Project urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),      # Delegates to app-level URLs
]

# blog/urls.py
from django.urls import path
from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.article_list, name='article-list'),
    path('<int:pk>/', views.article_detail, name='article-detail'),
    path('<slug:slug>/', views.article_by_slug, name='article-slug'),
]
```

When a request comes in (e.g., `/blog/42/`), Django tries each URL pattern in order. `<int:pk>` captures the `42` as an integer and passes it to the view as a keyword argument.

I use `name` parameters to create **named URLs**, so I can reference them in templates and code without hardcoding paths: `{% url 'blog:article-detail' pk=42 %}` or `reverse('blog:article-detail', kwargs={'pk': 42})`. This makes the code resilient to URL changes.

---

## 12. What is Django Template Language?

**Answer:**
Django's Template Language (DTL) is the syntax for creating dynamic HTML. Templates combine static HTML with Django tags and variables:

```html
<!-- Variables -->
<h1>{{ article.title }}</h1>
<p>{{ article.content|truncatewords:30 }}</p>

<!-- Tags (logic) -->
{% if articles %}
    {% for article in articles %}
        <h2>{{ article.title }}</h2>
    {% endfor %}
{% else %}
    <p>No articles found.</p>
{% endif %}

<!-- Filters (transform values) -->
{{ name|upper }}
{{ date|date:"F j, Y" }}
{{ text|linebreaks }}
```

Key concepts:
- `{{ variable }}` — outputs a value
- `{% tag %}` — performs logic (if, for, block, extends, include)
- `{{ variable|filter }}` — transforms the output

DTL is **intentionally limited** — it doesn't allow arbitrary Python code. This enforces separation of concerns: logic stays in views, presentation stays in templates. For complex operations, I create **custom template tags** or handle it in the view.

---

## 13. What is template inheritance in Django?

**Answer:**
Template inheritance lets me define a **base template** with common structure and **override specific blocks** in child templates — eliminating HTML duplication:

```html
<!-- base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Site{% endblock %}</title>
</head>
<body>
    <nav>...</nav>
    {% block content %}{% endblock %}
    <footer>...</footer>
</body>
</html>
```

```html
<!-- article_list.html -->
{% extends "base.html" %}

{% block title %}All Articles{% endblock %}

{% block content %}
    <h1>Articles</h1>
    {% for article in articles %}
        <p>{{ article.title }}</p>
    {% endfor %}
{% endblock %}
```

`{% extends %}` says "start with the base template." `{% block %}` defines overridable sections. The child only needs to define the blocks it wants to change. I can have **multi-level inheritance**: base → section_base → page_template.

This is like inheritance in OOP — the base template is the parent class, child templates override specific methods (blocks).

---

## 14. What is CSRF protection in Django?

**Answer:**
CSRF (Cross-Site Request Forgery) is an attack where a malicious website tricks a user's browser into making unwanted requests to a site where they're authenticated.

Django protects against this with the **CSRF middleware**. For every form that uses POST, PUT, PATCH, or DELETE, I must include the `{% csrf_token %}` tag:

```html
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Save</button>
</form>
```

This generates a hidden input with a **unique token** tied to the user's session. When the form is submitted, Django checks that the token matches. If it doesn't (like when a request comes from a different site), Django returns a **403 Forbidden** error.

The CSRF middleware is enabled by default in `MIDDLEWARE`. For REST APIs using token authentication, CSRF is typically not needed because the API uses its own authentication mechanism.

---

## 15. What is Django middleware?

**Answer:**
Middleware is a framework of **hooks** that processes requests and responses **globally** — every request passes through each middleware before reaching the view, and every response passes through them again on the way back:

```
Request → Middleware 1 → Middleware 2 → View → Middleware 2 → Middleware 1 → Response
```

Django ships with essential middleware:
- `SecurityMiddleware` — adds security headers (HSTS, etc.)
- `SessionMiddleware` — manages sessions
- `CsrfViewMiddleware` — CSRF protection
- `AuthenticationMiddleware` — associates users with requests

I can write **custom middleware** for cross-cutting concerns:
```python
class RequestTimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        start = time.time()
        response = self.get_response(request)
        duration = time.time() - start
        response['X-Request-Duration'] = str(duration)
        return response
```

The order of middleware in `settings.py` matters — they're processed top-to-bottom for requests and bottom-to-top for responses.

---

## 16. How does Django handle static files and media files?

**Answer:**
**Static files** are CSS, JavaScript, images that are part of the application code and don't change per user:
```python
# settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']   # Development
STATIC_ROOT = BASE_DIR / 'staticfiles'      # Production (collectstatic)
```

```html
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
```

**Media files** are user-uploaded content (profile photos, documents):
```python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

In **development**, Django serves both through `runserver`. In **production**, I use `python manage.py collectstatic` to gather all static files into `STATIC_ROOT`, then serve them through **nginx or a CDN** — never through Django in production, because it's too slow.

---

## 17. What is Django's authentication system?

**Answer:**
Django ships with a complete **authentication system** out of the box:

- **User model** — `django.contrib.auth.models.User` with fields: username, password, email, first_name, last_name
- **Authentication views** — built-in views for login, logout, password reset, password change
- **Permission system** — model-level permissions (add, change, delete, view), group-based permissions
- **Session management** — server-side sessions using cookies

```python
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required

# Authenticate
user = authenticate(request, username='alice', password='secret')
if user:
    login(request, user)

# Protect views
@login_required
def dashboard(request):
    return render(request, 'dashboard.html')
```

For custom user models (which is the **recommended** approach for new projects), I extend `AbstractUser` or `AbstractBaseUser`:
```python
class CustomUser(AbstractUser):
    phone = models.CharField(max_length=15, blank=True)
```

This should be done at the **start** of the project, as changing the user model later is complex.

---

## 18. What is Django Forms?

**Answer:**
Django Forms handle **HTML form generation, data validation, and cleaning** in a single, reusable class:

```python
from django import forms

class ContactForm(forms.Form):
    name = forms.CharField(max_length=100)
    email = forms.EmailField()
    message = forms.CharField(widget=forms.Textarea)

# ModelForm — automatically creates a form from a model
class ArticleForm(forms.ModelForm):
    class Meta:
        model = Article
        fields = ['title', 'content', 'category']
```

In the view:
```python
def contact(request):
    if request.method == 'POST':
        form = ContactForm(request.POST)
        if form.is_valid():
            cleaned_data = form.cleaned_data    # Validated & cleaned data
            send_email(cleaned_data)
            return redirect('success')
    else:
        form = ContactForm()
    return render(request, 'contact.html', {'form': form})
```

`ModelForm` is especially powerful — it generates form fields from model fields, handles validation, and can save directly to the database with `form.save()`. It eliminates the boilerplate of manually mapping forms to models.

---

## 19. What is the `manage.py` file? What are the most important commands?

**Answer:**
`manage.py` is Django's **command-line utility** for administrative tasks. It's a thin wrapper around `django-admin` that sets the project's settings module.

Most important commands:

```bash
python manage.py runserver             # Start development server
python manage.py makemigrations        # Detect model changes → create migration files
python manage.py migrate               # Apply migrations to database
python manage.py createsuperuser       # Create admin user
python manage.py shell                 # Interactive Python shell with Django context
python manage.py startapp appname      # Create a new app
python manage.py test                  # Run tests
python manage.py collectstatic         # Gather static files for production
python manage.py showmigrations        # Show migration status
python manage.py dbshell               # Open database CLI
python manage.py flush                 # Delete all data from database
```

I can also create **custom management commands** by creating a `management/commands/` directory inside an app — useful for scheduled tasks, data imports, or maintenance scripts.

---

## 20. How do you deploy a Django application?

**Answer:**
Deploying Django to production involves several steps beyond just the code:

1. **Set `DEBUG = False`** — never run debug mode in production (exposes sensitive info)
2. **Set `ALLOWED_HOSTS`** — whitelist the domain names
3. **Use a production database** — PostgreSQL is the go-to; SQLite is only for development
4. **Collect static files** — `python manage.py collectstatic`
5. **Use a WSGI/ASGI server** — **Gunicorn** (WSGI) or **Daphne/Uvicorn** (ASGI) instead of `runserver`
6. **Use a reverse proxy** — **Nginx** in front of Gunicorn to handle static files, SSL, and load balancing
7. **Environment variables** — store secrets (SECRET_KEY, DB passwords) in env vars, not in code
8. **HTTPS** — use Let's Encrypt for SSL certificates
9. **Set up logging** and monitoring

A typical stack: **Nginx → Gunicorn → Django → PostgreSQL**, deployed on a Linux server or a platform like AWS, DigitalOcean, or Heroku. For containerized deployments, I'd use Docker with Docker Compose.

---

## 21. What is the `settings.py` file? What are the most important settings?

**Answer:**
`settings.py` is the central configuration file for a Django project. The critical settings:

- **`SECRET_KEY`** — cryptographic key for security (sessions, CSRF). Must be kept secret and unique per deployment.
- **`DEBUG`** — `True` in development (shows detailed errors), `False` in production.
- **`ALLOWED_HOSTS`** — list of domains this Django site can serve.
- **`INSTALLED_APPS`** — all installed Django and third-party apps.
- **`DATABASES`** — database connection configuration.
- **`MIDDLEWARE`** — request/response processing pipeline.
- **`TEMPLATES`** — template engine configuration.
- **`STATIC_URL` / `MEDIA_URL`** — URLs for static and media files.
- **`AUTH_USER_MODEL`** — custom user model (should be set before first migration).

Best practice: use **environment variables** (via `python-decouple` or `django-environ`) for sensitive settings and have separate settings files for development and production.

---

## 22. What is the difference between `null=True` and `blank=True` in model fields?

**Answer:**
These are frequently confused but serve different purposes:

- **`null=True`** — database level. Allows the column to store **NULL** in the database. Used for non-string fields (integers, dates, foreign keys).

- **`blank=True`** — validation level. Allows the field to be submitted as **empty** in forms. It doesn't affect the database.

```python
class Profile(models.Model):
    bio = models.TextField(blank=True)              # Can be empty string in forms
    birth_date = models.DateField(null=True, blank=True)  # Can be NULL in DB + empty in forms
    name = models.CharField(max_length=100)          # Required everywhere
```

For **string fields** (CharField, TextField), avoid `null=True` — Django convention is to use empty strings (`""`) instead of NULL for text. So use `blank=True` alone. For **non-string fields**, use both `null=True, blank=True` if the field is optional.

---

## 23. What are signals in Django?

**Answer:**
Signals allow certain senders to notify receivers when specific actions occur — it's a way to **decouple** components. Think of it as an event system:

```python
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User

@receiver(post_save, sender=User)
def create_profile(sender, instance, created, **kwargs):
    if created:
        Profile.objects.create(user=instance)
```

Common built-in signals:
- `pre_save` / `post_save` — before/after a model is saved
- `pre_delete` / `post_delete` — before/after a model is deleted
- `request_started` / `request_finished` — HTTP request lifecycle

I use signals for **side effects** that should happen automatically — like creating a profile when a user registers, sending an email when an order is placed, or logging actions. However, overusing signals can make code hard to follow, so I use them sparingly and prefer explicit method calls when the logic is in the same app.

---

## 24. What is the `select_related` and `prefetch_related` in Django ORM?

**Answer:**
These solve the **N+1 query problem** — one of the most common Django performance issues.

**`select_related`** — for **ForeignKey and OneToOne** relationships. Uses a SQL JOIN to fetch related objects in a **single query**:
```python
# Without: 1 query for articles + N queries for authors (N+1 problem!)
articles = Article.objects.all()

# With: 1 query with JOIN — fetches articles AND authors together
articles = Article.objects.select_related('author').all()
```

**`prefetch_related`** — for **ManyToMany and reverse ForeignKey** relationships. Runs a **separate query** for the related objects and joins them in Python:
```python
# Fetches articles in one query, then all related tags in a second query
articles = Article.objects.prefetch_related('tags').all()
```

The rule: `select_related` for "forward" single-value relationships (JOIN), `prefetch_related` for "reverse" or multi-value relationships (separate query). Always use these when accessing related objects in loops — it can reduce hundreds of queries to just one or two.
