# Django Framework — From Zero to Expert

## Table of Contents
1. [What is Django?](#1-what-is-django)
2. [Django Architecture — MVT Pattern](#2-mvt-pattern)
3. [Installing Django](#3-installing-django)
4. [Creating Your First Django Project](#4-creating-first-project)
5. [Django Project Structure Explained](#5-project-structure)
6. [Django Apps — The Modular System](#6-django-apps)
7. [Settings Configuration](#7-settings-configuration)
8. [Django Models — Database Tables in Python](#8-django-models)
9. [Django ORM — Querying the Database](#9-django-orm)
10. [Migrations — Keeping DB in Sync](#10-migrations)
11. [Django Admin](#11-django-admin)
12. [Django Views — Handling Requests](#12-django-views)
13. [URL Configuration](#13-url-configuration)
14. [Django Templates](#14-django-templates)
15. [Template Inheritance](#15-template-inheritance)
16. [Static Files (CSS, JS, Images)](#16-static-files)
17. [Django Forms](#17-django-forms)
18. [User Authentication](#18-user-authentication)
19. [Running the Development Server](#19-running-server)
20. [Django Workflow Summary](#20-workflow-summary)

---

## 1. What is Django?

**Django** is a high-level Python web framework that makes it easy to build web applications quickly and cleanly. It was created in 2003 by developers at a newspaper company in Kansas, open-sourced in 2005.

**Django's Motto:** *"The web framework for perfectionists with deadlines."*

```
┌──────────────────────────────────────────────────────────┐
│              What Django Does For You                    │
│                                                          │
│  Without Django (doing it manually):                     │
│  • Parse HTTP requests manually                          │
│  • Write raw SQL to talk to database                     │
│  • Build admin interface from scratch                    │
│  • Handle user logins and sessions manually              │
│  • Protect against SQL injection, CSRF, XSS manually     │
│                                                          │
│  With Django:                                            │
│  ✔ Automatic URL routing                                 │
│  ✔ ORM (talk to DB in Python, no SQL needed)             │
│  ✔ Auto-generated admin interface                        │
│  ✔ Built-in user authentication                          │
│  ✔ Security protections built-in                         │
│  ✔ Form handling and validation                          │
│  ✔ Template engine for HTML generation                   │
└──────────────────────────────────────────────────────────┘
```

### Famous Sites Built with Django

- Instagram (massive scale)
- Pinterest
- Disqus
- Mozilla (Firefox)
- National Geographic
- NASA

### Django vs Flask

| | Django | Flask |
|-|--------|-------|
| Size | "Batteries included" — full framework | Microframework — minimal |
| Learning curve | Steeper at first | Easier to start |
| Structure | Opinionated (specific way to do things) | Flexible |
| Best for | Large applications, fast development | Small/custom apps |
| Admin panel | Built-in ✔ | Not built-in |
| ORM | Built-in ✔ | Not built-in |

---

## 2. Django Architecture — MVT Pattern

Django follows the **MVT** (Model-View-Template) pattern, which is similar to the classic MVC (Model-View-Controller):

```
┌────────────────────────────────────────────────────────────┐
│                    Django Request/Response Flow            │
│                                                            │
│  Browser                                                   │
│  sends → HTTP Request → Django URL Dispatcher              │
│                              │                             │
│                              ▼ matches URL                 │
│                           VIEW (views.py)                  │
│                              │                             │
│              ┌───────────────┼────────────────┐            │
│              │               │                │            │
│              ▼               ▼                ▼            │
│           MODEL          Templates         Other Services  │
│           (models.py)    (HTML files)      (APIs, etc.)    │
│              │               │                             │
│              ▼               │                             │
│           DATABASE           │                             │
│           (ORM Query)        │                             │
│              │               │                             │
│              └───────────────┘                             │
│                              │                             │
│                              ▼                             │ 
│                   HTTP Response (HTML)                     │
│                              │                             │
│                              ▼                             │
│                           Browser                          │
│                        renders the page                    │
└────────────────────────────────────────────────────────────┘
```

### The Three Layers

| Layer | File | Responsibility |
|-------|------|----------------|
| **Model** | `models.py` | Defines data structure, communicates with database |
| **View** | `views.py` | Business logic, processes requests, returns response |
| **Template** | `*.html` | Presentation layer, renders HTML for the browser |

---

## 3. Installing Django

### Step 1: Create a Project Folder

```bash
mkdir my_django_project
cd my_django_project
```

### Step 2: Create a Virtual Environment

```bash
# Create venv
python3 -m venv venv

# Activate it
# Mac/Linux:
source venv/bin/activate

# Windows:
venv\Scripts\activate

# Prompt changes to: (venv) $
```

### Step 3: Install Django

```bash
pip install django

# Verify installation
python -m django --version
# Should output: 4.x.x
```

### Step 4: Install VS Code Django Extension (Optional but Helpful)

In VS Code:
1. Press `Ctrl + Shift + X`
2. Search "Django" → Install "Django" by Baptiste Darthenay

---

## 4. Creating Your First Django Project

### `django-admin startproject`

```bash
django-admin startproject mysite .
```

**Note the `.` at the end** — it creates the project in the current directory instead of a subfolder.

After running this, your folder looks like:

```
my_django_project/
├── manage.py               ← Command-line tool for managing the project
├── venv/                   ← Virtual environment (don't touch)
└── mysite/                 ← The project package
    ├── __init__.py
    ├── settings.py         ← All project settings
    ├── urls.py             ← Root URL routing
    ├── asgi.py             ← Async server config
    └── wsgi.py             ← WSGI server config
```

### Run the Development Server

```bash
python manage.py runserver
```

Output:
```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

November 15, 2024 - 10:30:00
Django version 4.2, using settings 'mysite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Open your browser and go to **http://127.0.0.1:8000/**

You should see the Django welcome page — a rocket with "The install worked successfully! Congratulations!"

---

## 5. Django Project Structure Explained

### `manage.py`

The command-line utility for Django. You use it for everything:

```bash
python manage.py runserver          # Start development server
python manage.py makemigrations     # Create DB migration files
python manage.py migrate            # Apply migrations to DB
python manage.py createsuperuser    # Create admin user
python manage.py startapp <name>    # Create a new app
python manage.py shell              # Interactive Django Python shell
python manage.py test               # Run tests
python manage.py collectstatic      # Gather static files
```

### `settings.py` — The Brain of Your Project

```python
# mysite/settings.py

# Absolute path to the project
BASE_DIR = Path(__file__).resolve().parent.parent

# Django's secret key for cryptographic signing — NEVER share in production!
SECRET_KEY = 'django-insecure-...'

# False = production mode (more secure)
DEBUG = True

# Allowed hosts when DEBUG = False
ALLOWED_HOSTS = []

# Installed apps — both Django's built-in and your custom apps
INSTALLED_APPS = [
    'django.contrib.admin',         # Admin panel
    'django.contrib.auth',          # Authentication system
    'django.contrib.contenttypes',  # Content type framework
    'django.contrib.sessions',      # Session framework
    'django.contrib.messages',      # Messaging framework
    'django.contrib.staticfiles',   # Static file management
    # Your apps go here:
    # 'myapp',
]

# Database (default: SQLite3 — perfect for development)
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',  # Can be postgresql, mysql, oracle
        'NAME': BASE_DIR / 'db.sqlite3',         # Location of the DB file
    }
}

# Static and media files
STATIC_URL = '/static/'
MEDIA_URL = '/media/'

# Language and timezone
LANGUAGE_CODE = 'en-us'
TIME_ZONE = 'UTC'
USE_TZ = True
```

### `urls.py` — Root URL Router

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    # Add your app's URLs here:
    # path('', include('myapp.urls')),
]
```

---

## 6. Django Apps — The Modular System

Django organizes functionality into **apps**. An app is a self-contained module that does one thing (blog, shop, users, etc.). One project can have many apps.

```
┌──────────────────────────────────────────────────────────┐
│  Django Project vs App                                   │
│                                                          │
│  PROJECT = the whole website                             │
│    e.g., "Amazon"                                        │
│                                                          │
│  APP = a specific section or feature                     │
│    e.g., "products" app, "orders" app, "accounts" app    │
│                                                          │
│  One project → many apps                                 │
│  One app → reusable across projects                      │
└──────────────────────────────────────────────────────────┘
```

### Creating a New App

```bash
python manage.py startapp blog
```

This creates:

```
blog/
├── __init__.py
├── admin.py        ← Register models for admin panel
├── apps.py         ← App configuration
├── models.py       ← Database models
├── tests.py        ← Unit tests
├── views.py        ← View functions/classes
└── migrations/     ← Database migration files
    └── __init__.py
```

### Register the App

After creating an app, **you must register it** in `settings.py`:

```python
# mysite/settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    # ...
    'blog',    # ← Add your app here (or 'blog.apps.BlogConfig')
]
```

If you don't register the app, Django won't find its models, migrations, or templates.

---

## 7. Settings Configuration

### Environment-Specific Settings

For projects you'll deploy, separate development and production settings:

```python
# Install python-dotenv for environment variables
# pip install python-dotenv

# .env file (NEVER commit to git!)
SECRET_KEY=your-super-secret-key
DEBUG=True
DB_NAME=mydb
DB_USER=postgres
DB_PASSWORD=mypassword
DB_HOST=localhost

# settings.py
from dotenv import load_dotenv
import os

load_dotenv()   # Load variables from .env file

SECRET_KEY = os.environ.get('SECRET_KEY')
DEBUG = os.environ.get('DEBUG', 'False') == 'True'
```

### Database Configuration — PostgreSQL (Production)

```python
# pip install psycopg2-binary

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('DB_NAME'),
        'USER': os.environ.get('DB_USER'),
        'PASSWORD': os.environ.get('DB_PASSWORD'),
        'HOST': os.environ.get('DB_HOST', 'localhost'),
        'PORT': os.environ.get('DB_PORT', '5432'),
    }
}
```

### ALLOWED_HOSTS

```python
# In production, list your domain names:
ALLOWED_HOSTS = ['mysite.com', 'www.mysite.com', '12.34.56.78']

# During development with DEBUG=True, this is ignored
```

---

## 8. Django Models

A **model** is a Python class that:
1. Represents a table in the database
2. Defines the table's columns as class attributes (called **fields**)
3. Provides methods to interact with the database

Each model maps to ONE database table. Each attribute maps to a column.

### Creating Models

```python
# blog/models.py
from django.db import models
from django.utils import timezone


class Author(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    bio = models.TextField(blank=True)
    joined_date = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['last_name', 'first_name']

    def full_name(self):
        return f"{self.first_name} {self.last_name}"

    def __str__(self):
        return self.full_name()


class Category(models.Model):
    name = models.CharField(max_length=100, unique=True)
    slug = models.SlugField(unique=True)

    class Meta:
        verbose_name_plural = "categories"

    def __str__(self):
        return self.name


class Post(models.Model):
    STATUS_DRAFT = 'draft'
    STATUS_PUBLISHED = 'published'
    STATUS_CHOICES = [
        (STATUS_DRAFT, 'Draft'),
        (STATUS_PUBLISHED, 'Published'),
    ]

    title = models.CharField(max_length=200)
    slug = models.SlugField(unique=True)
    author = models.ForeignKey(Author, on_delete=models.CASCADE, related_name='posts')
    category = models.ForeignKey(Category, on_delete=models.SET_NULL, null=True, blank=True)
    tags = models.ManyToManyField('Tag', blank=True)
    content = models.TextField()
    excerpt = models.CharField(max_length=300, blank=True)
    status = models.CharField(max_length=10, choices=STATUS_CHOICES, default=STATUS_DRAFT)
    created_at = models.DateTimeField(auto_now_add=True)   # Set when created, never changes
    updated_at = models.DateTimeField(auto_now=True)        # Updated every save
    published_at = models.DateTimeField(null=True, blank=True)
    views_count = models.PositiveIntegerField(default=0)

    class Meta:
        ordering = ['-created_at']   # Newest first

    def publish(self):
        self.status = self.STATUS_PUBLISHED
        self.published_at = timezone.now()
        self.save()

    def __str__(self):
        return self.title


class Tag(models.Model):
    name = models.CharField(max_length=50, unique=True)

    def __str__(self):
        return self.name
```

### Django Field Types

| Field | Use Case | Options |
|-------|----------|---------|
| `CharField` | Short text | `max_length=X` (required) |
| `TextField` | Long text | — |
| `IntegerField` | Integer | — |
| `FloatField` | Float | — |
| `DecimalField` | Decimal (money) | `max_digits`, `decimal_places` |
| `BooleanField` | True/False | `default=False` |
| `DateField` | Date only | `auto_now`, `auto_now_add` |
| `DateTimeField` | Date + Time | `auto_now`, `auto_now_add` |
| `EmailField` | Email address | — |
| `URLField` | URL | — |
| `SlugField` | URL-friendly text | `unique=True` |
| `ImageField` | Image upload | `upload_to='path/'` |
| `FileField` | File upload | `upload_to='path/'` |
| `ForeignKey` | Many-to-one relation | `on_delete=...` |
| `ManyToManyField` | Many-to-many | — |
| `OneToOneField` | One-to-one | `on_delete=...` |

### Common Field Options

```python
models.CharField(
    max_length=100,
    blank=True,      # Empty string allowed in FORMS (not DB)
    null=True,       # NULL allowed in DATABASE
    default="N/A",   # Default value
    unique=True,     # Must be unique across all rows
    db_index=True,   # Create database index for faster lookup
    verbose_name="First Name",  # Human-readable label for admin
    help_text="Enter your first name.",  # Help text in admin/forms
    choices=[('M', 'Male'), ('F', 'Female')],  # Dropdown choices
)
```

**`blank` vs `null`:**
- `null=True`: The database column allows `NULL` (no value)
- `blank=True`: The form field allows empty input
- For strings: use only `blank=True` (Django stores empty string, not NULL)
- For non-strings (dates, numbers): use `null=True, blank=True`

### Relationships

```python
# ForeignKey (Many-to-One): Many Posts can have one Author
author = models.ForeignKey(
    Author,
    on_delete=models.CASCADE,     # If Author deleted, delete Posts too
    related_name='posts',         # author.posts.all() — reverse lookup
    null=True, blank=True
)

# on_delete options:
# CASCADE — delete related objects
# PROTECT — prevent deletion (raises ProtectedError)
# SET_NULL — set FK to NULL (requires null=True)
# SET_DEFAULT — set FK to its default value
# DO_NOTHING — do nothing (risky!)

# OneToOneField (One-to-One): Extend user model
class Profile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    avatar = models.ImageField(upload_to='avatars/')
    bio = models.TextField(blank=True)

# ManyToManyField (Many-to-Many): Posts can have many Tags, Tags can have many Posts
tags = models.ManyToManyField('Tag', blank=True)
# Access: post.tags.all()
# Reverse access: tag.post_set.all() (or use related_name='posts')
```

---

## 9. Django ORM

The **ORM (Object-Relational Mapper)** lets you query and manipulate the database using Python objects instead of raw SQL. Django translates your Python code to the correct SQL.

### Setting Up the Django Shell

```bash
python manage.py shell
```

Then in the shell:
```python
from blog.models import Author, Post, Category
```

### Creating Records

```python
# Method 1: Create, assign attributes, then save
author = Author()
author.first_name = "John"
author.last_name = "Doe"
author.email = "john@example.com"
author.save()   # Sends INSERT SQL to database

# Method 2: Create with keyword args, then save
author = Author(first_name="Jane", last_name="Smith", email="jane@example.com")
author.save()

# Method 3: create() — does both in one step
author = Author.objects.create(
    first_name="Alice",
    last_name="Wonder",
    email="alice@example.com"
)

# get_or_create() — get if exists, create if not
category, created = Category.objects.get_or_create(
    name="Technology",
    defaults={"slug": "technology"}
)
print(created)   # True if newly created, False if already existed
```

### Reading Records — QuerySets

```python
# ALL records
all_authors = Author.objects.all()
# Returns a QuerySet (lazy — SQL not run until you iterate)

# Get ONE record (raises exception if not found or multiple found)
author = Author.objects.get(id=1)
author = Author.objects.get(email="john@example.com")
# ⚠ Raises DoesNotExist if not found
# ⚠ Raises MultipleObjectsReturned if multiple match

# Safe get
try:
    author = Author.objects.get(id=999)
except Author.DoesNotExist:
    author = None

# Filter — returns QuerySet of matching records
published_posts = Post.objects.filter(status='published')
tech_posts = Post.objects.filter(category__name='Technology')  # Follow FK with __
recent_posts = Post.objects.filter(created_at__year=2024)

# Exclude — opposite of filter
draft_posts = Post.objects.exclude(status='published')

# Check existence
Post.objects.filter(status='published').exists()   # Returns True/False

# Count
Post.objects.filter(status='published').count()    # Returns integer
```

### QuerySet Field Lookups

Django provides powerful lookup patterns using double underscore `__`:

```python
# Exact match (default)
Post.objects.filter(title="Hello World")
Post.objects.filter(title__exact="Hello World")  # Same

# Case-insensitive
Post.objects.filter(title__iexact="hello world")

# Contains
Post.objects.filter(title__contains="Django")
Post.objects.filter(title__icontains="django")  # Case-insensitive

# Starts/ends with
Post.objects.filter(title__startswith="How to")
Post.objects.filter(title__endswith="?")

# Greater than, less than
Post.objects.filter(views_count__gt=100)   # Greater than 100
Post.objects.filter(views_count__gte=100)  # Greater than or equal
Post.objects.filter(views_count__lt=50)    # Less than
Post.objects.filter(views_count__lte=50)

# In a list
Post.objects.filter(id__in=[1, 2, 3])

# Date ranges
Post.objects.filter(created_at__date=today)
Post.objects.filter(created_at__year=2024, created_at__month=3)
Post.objects.filter(created_at__range=('2024-01-01', '2024-12-31'))

# IS NULL / IS NOT NULL
Post.objects.filter(published_at__isnull=True)
Post.objects.filter(published_at__isnull=False)

# Relationship traversal
Post.objects.filter(author__first_name="John")
Post.objects.filter(author__email__icontains="@example.com")
```

### Chaining QuerySets

```python
recent_published_tech = (Post.objects
    .filter(status='published')
    .filter(category__name='Technology')
    .order_by('-created_at')
    [:10])   # Get first 10
```

### Ordering and Slicing

```python
# Order by field (ascending)
Post.objects.order_by('title')

# Order by field (descending) — negative sign
Post.objects.order_by('-created_at')

# Multiple ordering
Post.objects.order_by('category', '-created_at')

# Slicing (translates to SQL LIMIT/OFFSET — no negative indexing!)
first_five = Post.objects.all()[:5]      # First 5
skip_ten = Post.objects.all()[10:20]     # Items 10 through 19
latest = Post.objects.order_by('-created_at').first()  # .first() and .last()
```

### Updating Records

```python
# Update ONE object
post = Post.objects.get(id=1)
post.title = "Updated Title"
post.save()   # Only saves changed fields if you use update_fields
post.save(update_fields=['title'])   # More efficient

# Bulk update — updates ALL matching records in ONE SQL query
Post.objects.filter(status='draft').update(status='published')
# WARNING: This does NOT call save() or trigger signals

# Increment a field
from django.db.models import F
Post.objects.filter(id=1).update(views_count=F('views_count') + 1)
# F() refers to the field's current value in the database (atomic, race-condition safe)
```

### Deleting Records

```python
# Delete ONE object
post = Post.objects.get(id=1)
post.delete()   # Returns (count, {model: count})

# Bulk delete
Post.objects.filter(status='draft').delete()
Post.objects.all().delete()   # Delete EVERYTHING (be careful!)
```

### Aggregation and Annotation

```python
from django.db.models import Sum, Avg, Count, Max, Min, F

# Count total published posts
count = Post.objects.filter(status='published').aggregate(total=Count('id'))
print(count)   # {'total': 42}

# Average views
avg = Post.objects.aggregate(avg_views=Avg('views_count'))

# Annotate — add a calculated field to each object in the QuerySet
from django.db.models import Count
authors_with_post_count = Author.objects.annotate(post_count=Count('posts'))
for author in authors_with_post_count:
    print(f"{author.full_name()}: {author.post_count} posts")
```

### Complex Queries with Q Objects

`Q` objects let you combine conditions with OR, AND, NOT:

```python
from django.db.models import Q

# Find posts that are published OR have more than 1000 views
posts = Post.objects.filter(
    Q(status='published') | Q(views_count__gt=1000)
)

# AND condition (same as chaining .filter())
posts = Post.objects.filter(
    Q(status='published') & Q(category__name='Technology')
)

# NOT
posts = Post.objects.filter(
    ~Q(status='draft')   # NOT draft
)

# Complex combination
posts = Post.objects.filter(
    (Q(status='published') | Q(views_count__gt=100)) & ~Q(author__email__icontains='test')
)
```

---

## 10. Migrations

Django **migrations** keep your database schema in sync with your Python models. Whenever you change a model, you create a migration to update the database.

```
┌──────────────────────────────────────────────────────────┐
│  Migration Workflow:                                     │
│                                                          │
│  1. You change models.py (add field, new model, etc.)    │
│  2. makemigrations → creates migration FILE              │
│  3. migrate → APPLIES the migration to the database      │
└──────────────────────────────────────────────────────────┘
```

```bash
# Step 1: Create migration files from model changes
python manage.py makemigrations
# or for a specific app:
python manage.py makemigrations blog

# Step 2: Apply migrations to the database
python manage.py migrate

# View migration status
python manage.py showmigrations

# Preview the SQL that will be executed
python manage.py sqlmigrate blog 0001

# Roll back to a previous migration
python manage.py migrate blog 0002   # Roll back to migration 0002
python manage.py migrate blog zero   # Roll back ALL migrations for the blog app
```

### Migration File Structure

```
blog/migrations/
├── __init__.py
├── 0001_initial.py           # First migration — creates all tables
├── 0002_add_status_field.py  # Second migration — adds a field
└── 0003_alter_post_title.py  # Third migration — modifies a field
```

**Rule:** NEVER manually edit migration files (unless you really know what you're doing). Always use `makemigrations`.

---

## 11. Django Admin

Django comes with a powerful **auto-generated admin interface** that lets you manage your data without writing any UI code.

### Access the Admin

1. Create a superuser:
```bash
python manage.py createsuperuser
# Enter: username, email, password
```

2. Run the server and go to **http://127.0.0.1:8000/admin/**

3. Log in with the superuser credentials.

### Register Models in Admin

```python
# blog/admin.py
from django.contrib import admin
from .models import Author, Post, Category, Tag


# Simple registration
admin.site.register(Author)
admin.site.register(Tag)
admin.site.register(Category)

# Customized admin
@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    # Columns shown in the list view
    list_display = ['title', 'author', 'status', 'created_at', 'views_count']

    # Clickable links in the list view
    list_display_links = ['title']

    # Filter sidebar
    list_filter = ['status', 'category', 'created_at']

    # Search bar
    search_fields = ['title', 'content', 'author__first_name', 'author__last_name']

    # Prepopulate slug from title
    prepopulated_fields = {'slug': ('title',)}

    # Inline edit fields
    readonly_fields = ['created_at', 'updated_at', 'views_count']

    # Default ordering
    ordering = ['-created_at']

    # Items per page
    list_per_page = 25

    # Allow editing from the list view (no need to open detail page)
    list_editable = ['status']

    # Date-based navigation
    date_hierarchy = 'created_at'

    # Fieldsets: organize fields into groups on the edit page
    fieldsets = (
        ('Content', {
            'fields': ('title', 'slug', 'content', 'excerpt')
        }),
        ('Metadata', {
            'fields': ('author', 'category', 'tags', 'status'),
        }),
        ('Timestamps', {
            'fields': ('created_at', 'updated_at', 'published_at'),
            'classes': ('collapse',),   # Collapsible section
        }),
    )
```

---

## 12. Django Views

A **view** is a Python function (or class) that receives an HTTP request and returns an HTTP response.

### Function-Based Views (FBV)

Simpler and more explicit — great for beginners:

```python
# blog/views.py
from django.shortcuts import render, get_object_or_404, redirect
from django.http import HttpResponse
from .models import Post, Author


def home(request):
    """Homepage — show latest published posts."""
    posts = Post.objects.filter(status='published').order_by('-created_at')[:10]
    return render(request, 'blog/home.html', {'posts': posts})


def post_detail(request, slug):
    """Show a single post."""
    post = get_object_or_404(Post, slug=slug, status='published')
    # get_object_or_404 = get or return 404 page (instead of crashing)

    # Increment view count
    Post.objects.filter(pk=post.pk).update(views_count=F('views_count') + 1)
    post.refresh_from_db()   # Reload from DB after update

    return render(request, 'blog/post_detail.html', {'post': post})


def author_posts(request, author_id):
    """Show all posts by a specific author."""
    author = get_object_or_404(Author, id=author_id)
    posts = author.posts.filter(status='published')
    context = {
        'author': author,
        'posts': posts,
    }
    return render(request, 'blog/author_posts.html', context)


def create_post(request):
    """Create a new post."""
    if request.method == 'POST':
        # Process the submitted form
        title = request.POST.get('title')
        content = request.POST.get('content')
        # ... validate and save
        Post.objects.create(title=title, content=content, status='draft')
        return redirect('home')   # Redirect after successful POST
    else:
        # Show the empty form (GET request)
        return render(request, 'blog/create_post.html')
```

### Class-Based Views (CBV)

More powerful for common patterns. Django provides many generic views:

```python
from django.views.generic import ListView, DetailView, CreateView, UpdateView, DeleteView
from django.urls import reverse_lazy


class PostListView(ListView):
    model = Post
    template_name = 'blog/post_list.html'
    context_object_name = 'posts'   # Default is 'object_list'
    paginate_by = 10                # Automatic pagination!

    def get_queryset(self):
        # Override to filter — only published posts
        return Post.objects.filter(status='published').order_by('-created_at')


class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/post_detail.html'
    context_object_name = 'post'
    slug_field = 'slug'


class PostCreateView(CreateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category', 'tags']
    success_url = reverse_lazy('post-list')   # Redirect after success


class PostUpdateView(UpdateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content', 'category', 'status']
    success_url = reverse_lazy('post-list')


class PostDeleteView(DeleteView):
    model = Post
    template_name = 'blog/post_confirm_delete.html'
    success_url = reverse_lazy('post-list')
```

### The `render()` Function

```python
return render(request, 'template.html', context)
# request:       The HTTP request object
# template.html: Template file to render
# context:       A dict of data to pass to the template
```

### `HttpResponse` Variants

```python
from django.http import HttpResponse, JsonResponse, HttpResponseRedirect, Http404
from django.shortcuts import redirect

# Plain text/HTML
return HttpResponse("Hello, plain text!")
return HttpResponse("<h1>Hello HTML</h1>", content_type="text/html")

# JSON response
return JsonResponse({"name": "Alice", "age": 25})
return JsonResponse({"error": "Not found"}, status=404)

# Redirect
return redirect('home')          # Redirect to URL name
return redirect('/blog/')        # Redirect to URL path
return redirect(post)            # Redirect to model's get_absolute_url()

# Raise 404
raise Http404("Post does not exist")
# Better:
post = get_object_or_404(Post, slug=slug)
```

---

## 13. URL Configuration

URL configuration maps URL patterns to view functions.

### App-Level URLs (Recommended)

Create a `urls.py` inside each app:

```python
# blog/urls.py
from django.urls import path
from . import views

app_name = 'blog'   # Namespace — prevents URL name conflicts between apps

urlpatterns = [
    path('', views.home, name='home'),
    path('posts/', views.PostListView.as_view(), name='post-list'),
    path('posts/<slug:slug>/', views.PostDetailView.as_view(), name='post-detail'),
    path('posts/create/', views.PostCreateView.as_view(), name='post-create'),
    path('posts/<slug:slug>/edit/', views.PostUpdateView.as_view(), name='post-update'),
    path('posts/<slug:slug>/delete/', views.PostDeleteView.as_view(), name='post-delete'),
    path('author/<int:author_id>/', views.author_posts, name='author-posts'),
]
```

### URL Pattern Converters

```python
# <int:pk>    → matches integers, converts to int
# <str:slug>  → matches any non-empty string without /
# <slug:slug> → matches slug strings (letters, numbers, hyphens, underscores)
# <uuid:id>   → matches UUID format
# <path:path> → matches any non-empty string, including /

path('article/<int:pk>/', views.article_detail),
path('user/<str:username>/', views.user_profile),
path('files/<path:filepath>/', views.serve_file),
```

### Connect App URLs to Project URLs

```python
# mysite/urls.py (root)
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls', namespace='blog')),  # All blog URLs at /blog/
    path('', include('blog.urls')),     # Blog at root (/)
]
```

### URL Reversing — Never Hardcode URLs!

```python
# In views:
from django.urls import reverse
from django.shortcuts import redirect

url = reverse('blog:post-detail', kwargs={'slug': 'my-post'})  # '/blog/posts/my-post/'
return redirect('blog:home')

# In templates:
# {% url 'blog:post-detail' post.slug %}
# {% url 'blog:home' %}
```

---

## 14. Django Templates

**Templates** are HTML files with special Django template syntax for dynamic content.

### Template Folder Setup

```python
# settings.py
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / 'templates'],  # Global templates folder
        'APP_DIRS': True,     # Also look in each app's templates/ folder
        ...
    }
]
```

Folder structure:
```
my_project/
├── templates/           ← Global templates
│   └── base.html        ← Base layout template
└── blog/
    └── templates/
        └── blog/        ← App-specific (namespaced by app name)
            ├── home.html
            ├── post_detail.html
            └── post_list.html
```

### Template Syntax

```html
<!-- blog/templates/blog/home.html -->

<!-- Variables: {{ variable }} -->
<h1>Welcome, {{ user.username }}!</h1>
<p>Total posts: {{ posts.count }}</p>

<!-- Tags: {% tag %} -->
{% if user.is_authenticated %}
    <a href="/logout/">Logout</a>
{% else %}
    <a href="/login/">Login</a>
{% endif %}

<!-- For loop -->
{% for post in posts %}
    <article>
        <h2><a href="{% url 'blog:post-detail' post.slug %}">{{ post.title }}</a></h2>
        <p>By {{ post.author.full_name }} on {{ post.created_at|date:"F j, Y" }}</p>
        <p>{{ post.excerpt }}</p>
        {% if post.tags.all %}
            <div>
                {% for tag in post.tags.all %}
                    <span class="tag">{{ tag.name }}</span>
                {% endfor %}
            </div>
        {% endif %}
    </article>
{% empty %}
    <p>No posts found.</p>  <!-- Shown when the list is empty -->
{% endfor %}

<!-- Comments (not visible in HTML source) -->
{# This is a template comment #}

<!-- Filters: {{ variable|filter }} -->
<p>{{ post.title|upper }}</p>                  <!-- UPPERCASE -->
<p>{{ post.content|truncatewords:50 }}</p>     <!-- First 50 words only -->
<p>{{ post.created_at|timesince }}</p>         <!-- "3 days ago" -->
<p>{{ post.views_count|default:"0" }}</p>      <!-- Default if None -->
<p>{{ post.content|linebreaks }}</p>           <!-- Convert newlines to <p> tags -->
<p>{{ post.content|safe }}</p>                 <!-- Don't escape HTML (careful!) -->
{{ price|floatformat:2 }}                     <!-- 19.99 -->
```

### Common Template Tags

```html
<!-- if / elif / else / endif -->
{% if score >= 90 %}
    <span class="grade">A</span>
{% elif score >= 80 %}
    <span class="grade">B</span>
{% else %}
    <span class="grade">C or below</span>
{% endif %}

<!-- for / empty / endfor -->
{% for item in item_list %}
    {{ forloop.counter }}: {{ item }}  <!-- forloop.counter starts at 1 -->
    {% if forloop.first %}(first!){% endif %}
    {% if forloop.last %}(last!){% endif %}
{% empty %}
    No items.
{% endfor %}

<!-- url tag -->
<a href="{% url 'blog:home' %}">Home</a>
<a href="{% url 'blog:post-detail' post.slug %}">{{ post.title }}</a>

<!-- load static -->
{% load static %}
<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}" alt="Logo">

<!-- csrf_token (required for all POST forms!) -->
<form method="post">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Submit</button>
</form>

<!-- include another template -->
{% include 'blog/includes/navbar.html' %}

<!-- block (for template inheritance) -->
{% block content %}
{% endblock %}
```

---

## 15. Template Inheritance

Template inheritance lets you define a **base layout** and have child templates fill in specific sections — avoiding repetition.

### Base Template

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %}My Blog{% endblock %}</title>
    {% load static %}
    <link rel="stylesheet" href="{% static 'css/bootstrap.min.css' %}">
    {% block extra_css %}{% endblock %}
</head>
<body>
    <!-- Navigation bar — same on every page -->
    <nav class="navbar navbar-expand-lg navbar-dark bg-dark">
        <a class="navbar-brand" href="{% url 'blog:home' %}">My Blog</a>
        <div class="navbar-nav">
            {% if user.is_authenticated %}
                <span class="nav-item nav-link">{{ user.username }}</span>
                <a class="nav-link" href="{% url 'logout' %}">Logout</a>
            {% else %}
                <a class="nav-link" href="{% url 'login' %}">Login</a>
            {% endif %}
        </div>
    </nav>

    <!-- Messages (Django's built-in notification system) -->
    {% if messages %}
        {% for message in messages %}
            <div class="alert alert-{{ message.tags }}">{{ message }}</div>
        {% endfor %}
    {% endif %}

    <!-- Main content — each child template defines this -->
    <main class="container mt-4">
        {% block content %}
        {% endblock %}
    </main>

    <!-- Footer — same on every page -->
    <footer class="bg-dark text-light py-3 mt-5">
        <div class="container text-center">
            <p>© 2024 My Blog. All rights reserved.</p>
        </div>
    </footer>

    <script src="{% static 'js/bootstrap.bundle.min.js' %}"></script>
    {% block extra_js %}{% endblock %}
</body>
</html>
```

### Child Template

```html
<!-- blog/templates/blog/home.html -->
{% extends 'base.html' %}  <!-- Must be the FIRST line! -->

{% block title %}Home - My Blog{% endblock %}

{% block content %}
<h1>Latest Posts</h1>

{% for post in posts %}
    <article class="card mb-3">
        <div class="card-body">
            <h2 class="card-title">
                <a href="{% url 'blog:post-detail' post.slug %}">{{ post.title }}</a>
            </h2>
            <p class="card-subtitle text-muted">
                By {{ post.author.full_name }} | {{ post.created_at|date:"N j, Y" }}
            </p>
            <p class="card-text">{{ post.excerpt|default:post.content|truncatewords:30 }}</p>
            <a href="{% url 'blog:post-detail' post.slug %}" class="btn btn-primary btn-sm">
                Read More
            </a>
        </div>
    </article>
{% empty %}
    <div class="alert alert-info">No posts published yet.</div>
{% endfor %}
{% endblock %}
```

---

## 16. Static Files

Static files are CSS, JavaScript, and image files that don't change per request.

### Setup

```python
# settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']  # Where to find static files during development
STATIC_ROOT = BASE_DIR / 'staticfiles'    # Where collectstatic gathers files for production
```

### Folder Structure

```
my_project/
└── static/
    ├── css/
    │   └── style.css
    ├── js/
    │   └── main.js
    └── images/
        └── logo.png
```

### Using in Templates

```html
{% load static %}

<link rel="stylesheet" href="{% static 'css/style.css' %}">
<script src="{% static 'js/main.js' %}"></script>
<img src="{% static 'images/logo.png' %}" alt="Logo">
```

### Media Files (User Uploads)

User-uploaded files are "media" files — different from static files:

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'

# models.py
class Post(models.Model):
    featured_image = models.ImageField(upload_to='posts/%Y/%m/', blank=True, null=True)
    # Files saved to: media/posts/2024/03/image.jpg
```

```python
# Add to urls.py (ONLY for development!)
# mysite/urls.py
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ...
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## 17. Django Forms

Django forms handle HTML form rendering, data validation, and cleaning.

### ModelForm (Most Common)

A `ModelForm` automatically creates a form from a Model:

```python
# blog/forms.py
from django import forms
from .models import Post, Author


class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'excerpt', 'category', 'tags', 'status']
        # Or: exclude = ['created_at', 'updated_at', 'views_count']
        widgets = {
            'title': forms.TextInput(attrs={'class': 'form-control', 'placeholder': 'Enter title'}),
            'content': forms.Textarea(attrs={'class': 'form-control', 'rows': 10}),
            'excerpt': forms.Textarea(attrs={'class': 'form-control', 'rows': 3}),
        }
        labels = {
            'content': 'Post Content',
        }
        help_texts = {
            'slug': 'This will be used in the URL.',
        }

    def clean_title(self):
        """Custom validation for the title field."""
        title = self.cleaned_data.get('title')
        if len(title) < 5:
            raise forms.ValidationError("Title must be at least 5 characters long.")
        return title.strip()   # Return cleaned (stripped) value


class AuthorRegistrationForm(forms.Form):
    """Custom form not tied to a model."""
    first_name = forms.CharField(max_length=100, label="First Name")
    last_name = forms.CharField(max_length=100)
    email = forms.EmailField()
    password = forms.CharField(widget=forms.PasswordInput)
    confirm_password = forms.CharField(widget=forms.PasswordInput)

    def clean(self):
        """Cross-field validation."""
        cleaned_data = super().clean()
        password = cleaned_data.get('password')
        confirm = cleaned_data.get('confirm_password')
        if password and confirm and password != confirm:
            raise forms.ValidationError("Passwords do not match!")
        return cleaned_data
```

### Using Forms in Views

```python
# blog/views.py
def create_post(request):
    if request.method == 'POST':
        form = PostForm(request.POST)     # Bind data to form
        if form.is_valid():
            post = form.save(commit=False)   # Don't save to DB yet
            post.author = request.user       # Set the author
            post.save()
            form.save_m2m()                  # Save ManyToMany fields (tags)
            return redirect('blog:post-detail', slug=post.slug)
    else:
        form = PostForm()   # Empty form for GET request

    return render(request, 'blog/post_form.html', {'form': form, 'action': 'Create'})


def edit_post(request, slug):
    post = get_object_or_404(Post, slug=slug)
    if request.method == 'POST':
        form = PostForm(request.POST, instance=post)  # Bind to existing object
        if form.is_valid():
            form.save()
            return redirect('blog:post-detail', slug=post.slug)
    else:
        form = PostForm(instance=post)   # Pre-fill with existing data

    return render(request, 'blog/post_form.html', {'form': form, 'action': 'Edit'})
```

### Form in Template

```html
<!-- blog/templates/blog/post_form.html -->
{% extends 'base.html' %}
{% block content %}
<h1>{{ action }} Post</h1>

<form method="post" enctype="multipart/form-data">
    {% csrf_token %}  <!-- REQUIRED for security! -->

    {{ form.as_p }}  <!-- Renders all fields wrapped in <p> tags -->
    <!-- Or: {{ form.as_table }} / {{ form.as_ul }} -->

    <!-- Manual rendering: -->
    {% for field in form %}
        <div class="mb-3">
            {{ field.label_tag }}
            {{ field }}
            {% if field.errors %}
                {% for error in field.errors %}
                    <div class="text-danger">{{ error }}</div>
                {% endfor %}
            {% endif %}
            {% if field.help_text %}
                <small class="text-muted">{{ field.help_text }}</small>
            {% endif %}
        </div>
    {% endfor %}

    <!-- Non-field errors (from clean()) -->
    {% if form.non_field_errors %}
        {% for error in form.non_field_errors %}
            <div class="alert alert-danger">{{ error }}</div>
        {% endfor %}
    {% endif %}

    <button type="submit" class="btn btn-primary">{{ action }} Post</button>
    <a href="{% url 'blog:home' %}" class="btn btn-secondary">Cancel</a>
</form>
{% endblock %}
```

---

## 18. User Authentication

Django comes with a complete user authentication system.

### Built-in URLs

```python
# mysite/urls.py
from django.contrib.auth import views as auth_views

urlpatterns = [
    # Django's built-in auth views:
    path('accounts/login/', auth_views.LoginView.as_view(template_name='registration/login.html'), name='login'),
    path('accounts/logout/', auth_views.LogoutView.as_view(), name='logout'),
    path('accounts/password-change/', auth_views.PasswordChangeView.as_view(), name='password_change'),
]
```

### Protect Views — Login Required

```python
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin

# For function-based views:
@login_required(login_url='/accounts/login/')
def create_post(request):
    ...

# For class-based views:
class PostCreateView(LoginRequiredMixin, CreateView):
    login_url = '/accounts/login/'
    ...
```

### Working with Users in Views

```python
def my_view(request):
    # The currently logged-in user
    user = request.user

    if request.user.is_authenticated:
        print(user.username)
        print(user.email)
        print(user.first_name)
        print(user.last_name)
        print(user.is_staff)
        print(user.is_superuser)
```

### Working with Users in Templates

```html
{% if user.is_authenticated %}
    <p>Hello, {{ user.username }}!</p>
    <a href="{% url 'logout' %}">Logout</a>
{% else %}
    <a href="{% url 'login' %}">Login</a>
{% endif %}
```

---

## 19. Running the Development Server

```bash
# Start the server
python manage.py runserver
# Server at http://127.0.0.1:8000/

# Run on a specific port
python manage.py runserver 8080

# Run accessible from other devices on the network
python manage.py runserver 0.0.0.0:8000

# The server auto-reloads when you save Python files
# (but you need to restart for settings changes)
```

### Useful Management Commands

```bash
# Open Django's interactive Python shell with models loaded
python manage.py shell

# Run tests
python manage.py test
python manage.py test blog    # Test specific app

# Check for problems
python manage.py check

# Collect static files for production
python manage.py collectstatic

# Create a superuser
python manage.py createsuperuser

# Clear sessions
python manage.py clearsessions

# Generate a new SECRET_KEY
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

---

## 20. Django Workflow Summary

Here is the complete workflow for building a Django feature from scratch:

```
┌────────────────────────────────────────────────────────────┐
│              Django Feature Build Workflow                 │
│                                                            │
│  1. Create app: python manage.py startapp myapp            │
│  2. Register app in settings.py INSTALLED_APPS             │
│  3. Define models in models.py                             │
│  4. python manage.py makemigrations                        │
│  5. python manage.py migrate                               │
│  6. Register models in admin.py                            │
│  7. Write views in views.py                                │
│  8. Define URL patterns in urls.py                         │
│  9. Include app urls in project urls.py                    │
│  10. Create templates (HTML files)                         │
│  11. Add static files (CSS, JS) if needed                  │
│  12. Test in browser at http://127.0.0.1:8000              │
└────────────────────────────────────────────────────────────┘
```

```
┌────────────────────────────────────────────────────────────┐
│                  Django Summary                            │
│                                                            │
│  MVT: Model  (data) -  View (logic) - Template (HTML)      │
│                                                            │
│  Model: class Post(models.Model): with fields              │
│  Migrations: makemigrations + migrate                      │
│  ORM: Post.objects.all(), .filter(), .get(), .create()     │
│  Admin: register in admin.py, access at /admin/            │
│  View: function(request) → render / redirect / JsonResponse│
│  URL: path('url/', view_func, name='name')                 │
│  Template: {{ var }}, {% tag %}, {% url %}, {% block %}    │
│  {% extends 'base.html' %} → template inheritance          │
│  Forms: ModelForm, is_valid(), cleaned_data, save()        │
│  Auth: login_required, user.is_authenticated               │
└────────────────────────────────────────────────────────────┘
```

**Next:** [08-Django-REST-Framework.md](08-Django-REST-Framework.md) — Building REST APIs with Django REST Framework.
