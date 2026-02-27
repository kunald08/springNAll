# Django REST Framework (DRF) — From Zero to Expert

## Table of Contents
1. [What is Django REST Framework?](#1-what-is-drf)
2. [Installing and Setting Up DRF](#2-installing-drf)
3. [What is a REST API?](#3-what-is-rest-api)
4. [Serializers — Converting Data](#4-serializers)
5. [ModelSerializer](#5-modelserializer)
6. [API Views — @api_view Decorator](#6-api-views)
7. [Class-Based API Views — APIView](#7-apiview)
8. [Generic Views — Less Code, More Power](#8-generic-views)
9. [ViewSets and Routers — Even Less Code](#9-viewsets-routers)
10. [Request and Response Objects](#10-request-response)
11. [Status Codes](#11-status-codes)
12. [Authentication](#12-authentication)
13. [Permissions](#13-permissions)
14. [Filtering, Searching, and Ordering](#14-filtering-searching-ordering)
15. [Pagination](#15-pagination)
16. [Nested Serializers and Relationships](#16-nested-serializers)
17. [Token Authentication — Full Walkthrough](#17-token-auth-walkthrough)
18. [Browsable API](#18-browsable-api)
19. [Complete Blog API Example](#19-complete-example)
20. [DRF Cheat Sheet](#20-cheat-sheet)

---

## 1. What is Django REST Framework?

**Django REST Framework (DRF)** is a powerful, flexible toolkit for building **REST APIs** on top of Django.

```
┌──────────────────────────────────────────────────────────┐
│  Traditional Django vs DRF                               │
│                                                          │
│  Traditional Django:                                     │
│  Browser → Django View → HTML Template → HTML Response   │
│                                                          │
│  Django REST Framework:                                  │
│  Client (Browser/Mobile/App) → DRF View → JSON Response  │
│                                                          │
│  DRF adds:                                               │
│  ✔ Serializers: Convert models ↔ JSON                    │
│  ✔ Generic API views (CRUD with a few lines)             │
│  ✔ ViewSets + Routers (auto URL generation)              │
│  ✔ Authentication (Token, Session, JWT)                  │
│  ✔ Permissions (who can access what)                     │
│  ✔ Filtering, pagination, throttling                     │
│  ✔ Browsable HTML API (test in browser)                  │
└──────────────────────────────────────────────────────────┘
```

**When to use DRF:**
- Building a REST API that mobile apps, React apps, or other services consume
- Creating a backend for a Single Page Application (SPA)
- Building a microservice

---

## 2. Installing and Setting Up DRF

### Step 1: Install

```bash
pip install djangorestframework

# Optional but commonly used with DRF:
pip install django-filter          # Advanced filtering
pip install djangorestframework-simplejwt  # JWT authentication
```

### Step 2: Add to INSTALLED_APPS

```python
# settings.py
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    # ...
    'rest_framework',           # ← Add this
    'rest_framework.authtoken', # ← Add this if using token authentication
    'django_filters',           # ← Add this if you installed django-filter
    'blog',                     # Your app
]
```

### Step 3: Global DRF Settings (Optional)

```python
# settings.py
REST_FRAMEWORK = {
    # Default authentication methods
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.SessionAuthentication',
        'rest_framework.authentication.TokenAuthentication',
    ],

    # Default permission: who can access the API
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticatedOrReadOnly',
        # Or: 'rest_framework.permissions.AllowAny'  (no authentication required)
        # Or: 'rest_framework.permissions.IsAuthenticated'  (must be logged in)
    ],

    # Default pagination
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,

    # Default filter backend
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ],

    # Throttling (rate limiting)
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
}
```

### Step 4: Run Migrations (Needed for Token Auth)

```bash
python manage.py migrate
```

---

## 3. What is a REST API?

**REST** (Representational State Transfer) is an architectural style for APIs. It uses HTTP methods to perform operations on resources.

```
┌──────────────────────────────────────────────────────────────────┐
│  REST API Conventions                                            │
│                                                                  │
│  URL (noun)             HTTP Method   Action                     │
│  ───────────────────────────────────────────────────────         │
│  /api/posts/            GET           List all posts             │
│  /api/posts/            POST          Create a new post          │
│  /api/posts/42/         GET           Get post with id=42        │
│  /api/posts/42/         PUT           Replace post 42 entirely   │
│  /api/posts/42/         PATCH         Update part of post 42     │
│  /api/posts/42/         DELETE        Delete post 42             │
└──────────────────────────────────────────────────────────────────┘
```

### HTTP Status Codes

| Code | Meaning |
|------|---------|
| 200 OK | Success |
| 201 Created | Resource created successfully |
| 204 No Content | Success, but no response body (used for DELETE) |
| 400 Bad Request | Client sent invalid data |
| 401 Unauthorized | Not authenticated |
| 403 Forbidden | Authenticated but not authorized |
| 404 Not Found | Resource doesn't exist |
| 405 Method Not Allowed | Correct URL, wrong HTTP method |
| 500 Internal Server Error | Something went wrong on the server |

---

## 4. Serializers

A **serializer** is the most important concept in DRF. It:
1. **Serializes**: Converts Python/Django model objects → JSON (for sending to client)
2. **Deserializes**: Converts JSON → validated Python data (for creating/updating models)

```
┌────────────────────────────────────────────────────────────┐
│  Serializer Role                                           │
│                                                            │
│  Model Object  ──(serialize)──→  JSON                      │
│  { title: "...", author: {...} }                           │
│                                                            │
│  JSON  ──(deserialize+validate)──→  Model Object           │
│  { "title": "...", "author": 1 }                           │
└────────────────────────────────────────────────────────────┘
```

### Basic Serializer

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post, Author, Category, Tag


class TagSerializer(serializers.Serializer):
    """Basic Serializer — explicitly define every field."""
    id = serializers.IntegerField(read_only=True)
    name = serializers.CharField(max_length=50)

    def create(self, validated_data):
        """Called when serializer.save() is used to create."""
        return Tag.objects.create(**validated_data)

    def update(self, instance, validated_data):
        """Called when serializer.save() is used to update."""
        instance.name = validated_data.get('name', instance.name)
        instance.save()
        return instance
```

### Using a Serializer in the Shell

```python
# Django shell: python manage.py shell
from blog.models import Tag
from blog.serializers import TagSerializer

# Serialize an object to JSON-compatible data
tag = Tag.objects.get(id=1)
serializer = TagSerializer(tag)
print(serializer.data)
# {'id': 1, 'name': 'Python'}

# Serialize a queryset (many=True)
tags = Tag.objects.all()
serializer = TagSerializer(tags, many=True)
print(serializer.data)
# [{'id': 1, 'name': 'Python'}, {'id': 2, 'name': 'Django'}]

# Deserialize (create a new object)
data = {'name': 'JavaScript'}
serializer = TagSerializer(data=data)
if serializer.is_valid():
    tag = serializer.save()   # Calls create()
else:
    print(serializer.errors)

# Deserialize (update an existing object)
existing_tag = Tag.objects.get(id=1)
serializer = TagSerializer(existing_tag, data={'name': 'Python 3'})
if serializer.is_valid():
    tag = serializer.save()   # Calls update()
```

---

## 5. ModelSerializer

`ModelSerializer` automatically creates serializer fields from a model — much less code:

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Author, Category, Tag, Post


class TagSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tag
        fields = ['id', 'name']
        # Or: fields = '__all__'  (all fields)
        # Or: exclude = ['some_field']  (all EXCEPT these)


class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = Category
        fields = ['id', 'name', 'slug']


class AuthorSerializer(serializers.ModelSerializer):
    full_name = serializers.SerializerMethodField()

    class Meta:
        model = Author
        fields = ['id', 'first_name', 'last_name', 'full_name', 'email', 'joined_date']
        read_only_fields = ['id', 'joined_date']   # Cannot be changed via API

    def get_full_name(self, obj):
        """SerializerMethodField: custom computed field."""
        return f"{obj.first_name} {obj.last_name}"


class PostSerializer(serializers.ModelSerializer):
    # Read: show author_name string. Write: accept author id.
    author_name = serializers.SerializerMethodField()
    category_name = serializers.CharField(source='category.name', read_only=True)
    tags = TagSerializer(many=True, read_only=True)
    tag_ids = serializers.PrimaryKeyRelatedField(
        many=True,
        write_only=True,
        queryset=Tag.objects.all(),
        source='tags'
    )

    class Meta:
        model = Post
        fields = [
            'id', 'title', 'slug', 'excerpt', 'content', 'status',
            'author_name', 'category', 'category_name',
            'tags', 'tag_ids',
            'views_count', 'created_at', 'updated_at',
        ]
        read_only_fields = ['id', 'slug', 'views_count', 'created_at', 'updated_at']
        extra_kwargs = {
            'content': {'write_only': True},   # Only shown when writing, not reading
        }

    def get_author_name(self, obj):
        return obj.author.full_name() if obj.author else None

    def validate_title(self, value):
        """Field-level validation: method name = validate_<fieldname>."""
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters.")
        return value.strip()

    def validate(self, data):
        """Object-level validation: check multiple fields together."""
        if data.get('status') == 'published' and not data.get('excerpt'):
            raise serializers.ValidationError("Published posts must have an excerpt.")
        return data

    def create(self, validated_data):
        """Override create to handle ManyToMany and set author."""
        tags = validated_data.pop('tags', [])
        post = Post.objects.create(**validated_data)
        post.tags.set(tags)
        return post

    def update(self, instance, validated_data):
        """Override update to handle ManyToMany."""
        tags = validated_data.pop('tags', None)
        for attr, value in validated_data.items():
            setattr(instance, attr, value)
        instance.save()
        if tags is not None:
            instance.tags.set(tags)
        return instance
```

---

## 6. API Views — @api_view Decorator

The simplest way to write an API view — a function with `@api_view`:

```python
# blog/views.py
from rest_framework.decorators import api_view, permission_classes
from rest_framework.permissions import IsAuthenticated, AllowAny
from rest_framework.response import Response
from rest_framework import status
from .models import Post, Tag
from .serializers import PostSerializer, TagSerializer


@api_view(['GET'])
def post_list(request):
    """
    GET /api/posts/
    Returns a list of all published posts.
    """
    posts = Post.objects.filter(status='published').order_by('-created_at')
    serializer = PostSerializer(posts, many=True)
    return Response(serializer.data)   # DRF Response auto-converts to JSON


@api_view(['GET', 'POST'])
def tag_list(request):
    """
    GET  /api/tags/  → List all tags
    POST /api/tags/  → Create a new tag
    """
    if request.method == 'GET':
        tags = Tag.objects.all()
        serializer = TagSerializer(tags, many=True)
        return Response(serializer.data)

    elif request.method == 'POST':
        serializer = TagSerializer(data=request.data)   # request.data = parsed JSON body
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


@api_view(['GET', 'PUT', 'PATCH', 'DELETE'])
@permission_classes([IsAuthenticated])   # Override global permissions
def tag_detail(request, pk):
    """
    GET    /api/tags/1/  → Get tag with id=1
    PUT    /api/tags/1/  → Replace tag with id=1
    PATCH  /api/tags/1/  → Partially update tag with id=1
    DELETE /api/tags/1/  → Delete tag with id=1
    """
    try:
        tag = Tag.objects.get(pk=pk)
    except Tag.DoesNotExist:
        return Response({'error': 'Tag not found'}, status=status.HTTP_404_NOT_FOUND)

    if request.method == 'GET':
        serializer = TagSerializer(tag)
        return Response(serializer.data)

    elif request.method in ['PUT', 'PATCH']:
        partial = (request.method == 'PATCH')   # PATCH = partial update
        serializer = TagSerializer(tag, data=request.data, partial=partial)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    elif request.method == 'DELETE':
        tag.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

---

## 7. Class-Based API Views — APIView

`APIView` provides more structure and power than `@api_view`:

```python
# blog/views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
from django.shortcuts import get_object_or_404


class PostListAPIView(APIView):
    """
    GET  /api/posts/  → List all posts
    POST /api/posts/  → Create a post
    """
    def get(self, request):
        posts = Post.objects.filter(status='published')
        serializer = PostSerializer(posts, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = PostSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save(author=request.user)   # Pass extra data to save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)


class PostDetailAPIView(APIView):
    """
    GET    /api/posts/<pk>/  → Get a post
    PUT    /api/posts/<pk>/  → Update it
    DELETE /api/posts/<pk>/  → Delete it
    """
    def get_object(self, pk):
        return get_object_or_404(Post, pk=pk)

    def get(self, request, pk):
        post = self.get_object(pk)
        serializer = PostSerializer(post)
        return Response(serializer.data)

    def put(self, request, pk):
        post = self.get_object(pk)
        serializer = PostSerializer(post, data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def patch(self, request, pk):
        post = self.get_object(pk)
        serializer = PostSerializer(post, data=request.data, partial=True)  # partial=True!
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)

    def delete(self, request, pk):
        post = self.get_object(pk)
        post.delete()
        return Response(status=status.HTTP_204_NO_CONTENT)
```

---

## 8. Generic Views — Less Code, More Power

DRF's **generic views** (also called "mixins") implement the most common patterns automatically. You only need to specify the model and serializer:

```python
# blog/views.py
from rest_framework import generics
from rest_framework.permissions import IsAuthenticated, IsAuthenticatedOrReadOnly
from .models import Post, Author, Category, Tag
from .serializers import PostSerializer, AuthorSerializer, TagSerializer


class TagListCreateView(generics.ListCreateAPIView):
    """
    GET  /api/tags/  → List all tags
    POST /api/tags/  → Create a tag
    No code needed — generics does it all!
    """
    queryset = Tag.objects.all()
    serializer_class = TagSerializer


class TagRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    """
    GET    /api/tags/<pk>/  → Get one
    PUT    /api/tags/<pk>/  → Replace
    PATCH  /api/tags/<pk>/  → Update partially
    DELETE /api/tags/<pk>/  → Delete
    """
    queryset = Tag.objects.all()
    serializer_class = TagSerializer


class PostListCreateView(generics.ListCreateAPIView):
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]  # Read-only for unauthenticated

    def get_queryset(self):
        """Override to customize the queryset."""
        qs = Post.objects.filter(status='published').order_by('-created_at')
        # Filter by author
        author_id = self.request.query_params.get('author')
        if author_id:
            qs = qs.filter(author_id=author_id)
        return qs

    def perform_create(self, serializer):
        """Override to set the author to the current user."""
        serializer.save(author=self.request.user)


class PostRetrieveUpdateDestroyView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    lookup_field = 'slug'   # Use slug instead of pk in URL
```

### All Generic View Classes

| Generic View | HTTP Methods | Use Case |
|---|---|---|
| `ListAPIView` | GET | Read-only list |
| `CreateAPIView` | POST | Create only |
| `RetrieveAPIView` | GET | Read-only detail |
| `UpdateAPIView` | PUT, PATCH | Update only |
| `DestroyAPIView` | DELETE | Delete only |
| `ListCreateAPIView` | GET, POST | List + Create |
| `RetrieveUpdateAPIView` | GET, PUT, PATCH | Read + Update |
| `RetrieveDestroyAPIView` | GET, DELETE | Read + Delete |
| `RetrieveUpdateDestroyAPIView` | GET, PUT, PATCH, DELETE | Full CRUD on one object |

---

## 9. ViewSets and Routers

**ViewSets** combine related views (list + detail + CRUD) into a single class. **Routers** automatically generate URLs for them.

```
┌──────────────────────────────────────────────────────────────┐
│  Without Router (manual URL setup):                          │
│    path('posts/', PostListView.as_view(), name='post-list')  │
│    path('posts/<pk>/', PostDetailView.as_view(), name='...')  │
│    ...6 lines for each model                                  │
│                                                              │
│  With Router (auto URL generation):                          │
│    router.register('posts', PostViewSet)                     │
│    Just 1 line per model!                                    │
└──────────────────────────────────────────────────────────────┘
```

### ModelViewSet

```python
# blog/views.py
from rest_framework import viewsets
from rest_framework.decorators import action
from rest_framework.response import Response


class PostViewSet(viewsets.ModelViewSet):
    """
    Automatically provides:
    GET    /api/posts/        → list()
    POST   /api/posts/        → create()
    GET    /api/posts/<pk>/   → retrieve()
    PUT    /api/posts/<pk>/   → update()
    PATCH  /api/posts/<pk>/   → partial_update()
    DELETE /api/posts/<pk>/   → destroy()
    """
    queryset = Post.objects.all().order_by('-created_at')
    serializer_class = PostSerializer

    def get_queryset(self):
        """Filter to show only published posts to unauthenticated users."""
        qs = super().get_queryset()
        if not self.request.user.is_authenticated:
            return qs.filter(status='published')
        return qs

    def perform_create(self, serializer):
        """Set author automatically on create."""
        serializer.save(author=self.request.user)

    @action(detail=True, methods=['POST'])
    def publish(self, request, pk=None):
        """
        Custom action: POST /api/posts/<pk>/publish/
        Makes a post live.
        """
        post = self.get_object()
        post.publish()
        return Response({'status': 'Post published.'})

    @action(detail=False, methods=['GET'])
    def my_posts(self, request):
        """
        Custom action: GET /api/posts/my_posts/
        Returns the current user's posts.
        """
        posts = Post.objects.filter(author=request.user)
        serializer = self.get_serializer(posts, many=True)
        return Response(serializer.data)


class TagViewSet(viewsets.ModelViewSet):
    queryset = Tag.objects.all()
    serializer_class = TagSerializer


class AuthorViewSet(viewsets.ReadOnlyModelViewSet):
    """
    ReadOnlyModelViewSet: Only list() and retrieve() — no create/update/delete.
    """
    queryset = Author.objects.all()
    serializer_class = AuthorSerializer
```

### Router Setup

```python
# blog/urls.py
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register('posts', views.PostViewSet)
router.register('tags', views.TagViewSet)
router.register('authors', views.AuthorViewSet)

# router.urls automatically generates:
# GET/POST /posts/
# GET/PUT/PATCH/DELETE /posts/<pk>/
# POST /posts/<pk>/publish/
# GET /posts/my_posts/
# ... same for tags, authors

urlpatterns = router.urls

# To add to the project:
# mysite/urls.py
# path('api/', include('blog.urls')),
```

---

## 10. Request and Response Objects

### `request` in DRF

DRF's `request` is an extended version of Django's `HttpRequest`:

```python
def my_view(request):
    # Parsed request body (JSON, form data, etc.)
    request.data         # Like request.POST but works for JSON too

    # Query parameters from URL: /api/posts/?status=published&page=2
    request.query_params.get('status')    # 'published'
    request.query_params.get('page', 1)   # '2' (or default '1')

    # The authenticated user
    request.user         # AnonymousUser if not authenticated
    request.auth         # Authentication token (if using token auth)

    # HTTP method
    request.method       # 'GET', 'POST', etc.

    # Request headers
    request.META.get('HTTP_AUTHORIZATION')
```

### `Response` in DRF

```python
from rest_framework.response import Response
from rest_framework import status

# Return data (dict or list) — automatically rendered to JSON
return Response(serializer.data)

# With HTTP status code
return Response(serializer.data, status=status.HTTP_201_CREATED)

# Error response
return Response({'error': 'Not found'}, status=status.HTTP_404_NOT_FOUND)

# Multiple error messages (same format as serializer.errors)
return Response(
    {'title': ['This field is required.'], 'content': ['This field is required.']},
    status=status.HTTP_400_BAD_REQUEST
)

# Empty response (for DELETE)
return Response(status=status.HTTP_204_NO_CONTENT)
```

---

## 11. Status Codes

DRF provides constants for all HTTP status codes:

```python
from rest_framework import status

# 2xx — Success
status.HTTP_200_OK                  # 200 — Default success
status.HTTP_201_CREATED             # 201 — Resource created
status.HTTP_204_NO_CONTENT          # 204 — Success, no body (DELETE)

# 4xx — Client errors
status.HTTP_400_BAD_REQUEST         # 400 — Invalid data from client
status.HTTP_401_UNAUTHORIZED        # 401 — Not authenticated
status.HTTP_403_FORBIDDEN           # 403 — Authenticated, not permitted
status.HTTP_404_NOT_FOUND           # 404 — Resource not found
status.HTTP_405_METHOD_NOT_ALLOWED  # 405 — Wrong HTTP method

# 5xx — Server errors
status.HTTP_500_INTERNAL_SERVER_ERROR  # 500 — Server crash
```

---

## 12. Authentication

Authentication identifies **who is making the request**.

### Session Authentication (for browser-based apps)

Built-in with Django. Works with cookies. Used by the browsable API.

No extra setup needed — it's in `DEFAULT_AUTHENTICATION_CLASSES` by default.

### Token Authentication

Each user gets a unique token. Client sends it in the header:

```
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

**Setup:**

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken',
]
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
    ],
}
```

```bash
python manage.py migrate  # Creates auth_token table
```

**Create token for a user:**

```python
# In Django shell or automatically via signal
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token

user = User.objects.get(username='alice')
token, created = Token.objects.get_or_create(user=user)
print(token.key)   # e.g., '9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b'
```

**Auto-create tokens when users are created:**

```python
# signals.py in your app
from django.db.models.signals import post_save
from django.dispatch import receiver
from django.contrib.auth.models import User
from rest_framework.authtoken.models import Token

@receiver(post_save, sender=User)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        Token.objects.create(user=instance)
```

**Obtain token via API (login endpoint):**

```python
# urls.py
from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    # ...
    path('api/token/', obtain_auth_token, name='api_token_auth'),
]
```

```bash
# Client sends:
curl -X POST http://127.0.0.1:8000/api/token/ \
     -d '{"username": "alice", "password": "pass123"}' \
     -H 'Content-Type: application/json'

# Server returns:
{"token": "9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b"}
```

**Using the token in subsequent requests:**

```bash
curl http://127.0.0.1:8000/api/posts/ \
     -H 'Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b'
```

### JWT Authentication (Popular for Modern APIs)

JWT (JSON Web Tokens) are stateless — no database lookup needed.

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('api/token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

```bash
# Login to get tokens
curl -X POST http://127.0.0.1:8000/api/token/ \
     -d '{"username": "alice", "password": "pass123"}' \
     -H 'Content-Type: application/json'

# Returns:
{
    "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...",    # Expires in 5 minutes
    "refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."   # Expires in 24 hours
}

# Use access token in requests:
curl http://127.0.0.1:8000/api/posts/ \
     -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9...'

# Refresh access token using refresh token:
curl -X POST http://127.0.0.1:8000/api/token/refresh/ \
     -d '{"refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9..."}' \
     -H 'Content-Type: application/json'
```

---

## 13. Permissions

Permissions determine **what an authenticated user is allowed to do.**

```
┌────────────────────────────────────────────────────────────┐
│  Authentication vs Permissions                             │
│                                                            │
│  Authentication: "Who are you?"                            │
│    Identifies the user (token, session, etc.)              │
│                                                            │
│  Permissions:    "What are you allowed to do?"             │
│    Decides if the request should proceed                   │
└────────────────────────────────────────────────────────────┘
```

### Built-in Permissions

```python
from rest_framework.permissions import (
    AllowAny,                    # Anyone can access (no auth required)
    IsAuthenticated,             # Must be logged in
    IsAdminUser,                 # Must be staff/admin
    IsAuthenticatedOrReadOnly,   # Read-only for anon, full access for authenticated
)
```

### Setting Permissions

```python
# Global (in settings.py):
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': ['rest_framework.permissions.IsAuthenticated'],
}

# Per-view:
@api_view(['GET'])
@permission_classes([AllowAny])
def public_posts(request):
    ...

class PostViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated]
    ...
```

### Custom Permission

```python
from rest_framework.permissions import BasePermission, SAFE_METHODS

class IsOwnerOrReadOnly(BasePermission):
    """
    Read access: anybody with authentication.
    Write access: only the object's owner.
    """
    def has_object_permission(self, request, view, obj):
        # SAFE_METHODS = ('GET', 'HEAD', 'OPTIONS')
        if request.method in SAFE_METHODS:
            return True
        # Write access only for the post's author
        return obj.author == request.user


class IsAdminOrReadOnly(BasePermission):
    """Allow any GET, but only admins can POST/PUT/DELETE."""
    def has_permission(self, request, view):
        if request.method in SAFE_METHODS:
            return True
        return request.user and request.user.is_staff


# Use in a view:
class PostDetailAPIView(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticated, IsOwnerOrReadOnly]
```

---

## 14. Filtering, Searching, and Ordering

### Basic Manual Filtering

```python
def get_queryset(self):
    qs = Post.objects.all()
    status = self.request.query_params.get('status')
    author = self.request.query_params.get('author')
    if status:
        qs = qs.filter(status=status)
    if author:
        qs = qs.filter(author__id=author)
    return qs
# URL: /api/posts/?status=published&author=1
```

### Django-Filter Integration

```bash
pip install django-filter
```

```python
# settings.py
INSTALLED_APPS = [..., 'django_filters']
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend'],
}

# blog/filters.py
import django_filters
from .models import Post


class PostFilter(django_filters.FilterSet):
    status = django_filters.ChoiceFilter(choices=Post.STATUS_CHOICES)
    created_after = django_filters.DateFilter(field_name='created_at', lookup_expr='gte')
    created_before = django_filters.DateFilter(field_name='created_at', lookup_expr='lte')
    title = django_filters.CharFilter(lookup_expr='icontains')

    class Meta:
        model = Post
        fields = ['status', 'author', 'category', 'tags']

# blog/views.py
from django_filters.rest_framework import DjangoFilterBackend
from rest_framework.filters import SearchFilter, OrderingFilter


class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    filter_backends = [DjangoFilterBackend, SearchFilter, OrderingFilter]
    filterset_class = PostFilter
    filterset_fields = ['status', 'author', 'category']   # Simple alternative to PostFilter
    search_fields = ['title', 'content', 'author__first_name']   # SearchFilter
    ordering_fields = ['created_at', 'views_count', 'title']     # OrderingFilter
    ordering = ['-created_at']   # Default ordering
```

```bash
# Filter: /api/posts/?status=published&author=1
# Search: /api/posts/?search=django
# Order:  /api/posts/?ordering=-views_count
# Combine: /api/posts/?status=published&search=python&ordering=-created_at
```

---

## 15. Pagination

DRF supports pagination out of the box. Configure it globally or per-view:

### Global Configuration

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

Response format with pagination:
```json
{
    "count": 42,
    "next": "http://api.example.com/posts/?page=3",
    "previous": "http://api.example.com/posts/?page=1",
    "results": [...]   // The actual data
}
```

### Custom Pagination

```python
from rest_framework.pagination import PageNumberPagination, LimitOffsetPagination


class StandardPagination(PageNumberPagination):
    page_size = 10
    page_size_query_param = 'page_size'   # Allow client to set page size
    max_page_size = 100                   # But not more than 100

class PostViewSet(viewsets.ModelViewSet):
    pagination_class = StandardPagination
    ...
```

```bash
# Page-based: /api/posts/?page=2&page_size=5
# Limit-offset: /api/posts/?limit=10&offset=20
```

---

## 16. Nested Serializers and Relationships

### Read Nested (show full related object)

```python
class PostSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)       # Full author object
    category = CategorySerializer(read_only=True)   # Full category object
    tags = TagSerializer(many=True, read_only=True) # Full list of tags
    author_id = serializers.PrimaryKeyRelatedField(
        queryset=Author.objects.all(), source='author', write_only=True
    )
    category_id = serializers.PrimaryKeyRelatedField(
        queryset=Category.objects.all(), source='category', write_only=True
    )
    tag_ids = serializers.PrimaryKeyRelatedField(
        many=True, queryset=Tag.objects.all(), source='tags', write_only=True
    )

    class Meta:
        model = Post
        fields = ['id', 'title', 'author', 'author_id', 'category', 'category_id',
                  'tags', 'tag_ids', 'content', 'status']

# GET response: full author dict { "id": 1, "first_name": "John", ... }
# POST/PUT payload: just send author_id: 1, category_id: 2, tag_ids: [1, 3]
```

### SerializerMethodField (Computed Fields)

```python
class PostSerializer(serializers.ModelSerializer):
    url = serializers.SerializerMethodField()
    is_owner = serializers.SerializerMethodField()
    tag_names = serializers.SerializerMethodField()

    class Meta:
        model = Post
        fields = ['id', 'title', 'url', 'is_owner', 'tag_names', ...]

    def get_url(self, obj):
        request = self.context.get('request')
        return request.build_absolute_uri(f'/api/posts/{obj.pk}/')

    def get_is_owner(self, obj):
        request = self.context.get('request')
        if request and request.user.is_authenticated:
            return obj.author == request.user
        return False

    def get_tag_names(self, obj):
        return [tag.name for tag in obj.tags.all()]
```

---

## 17. Token Authentication — Full Walkthrough

Let's build a complete login/register flow with Token Authentication:

### Models, Serializers, Views, URLs

```python
# accounts/serializers.py
from django.contrib.auth.models import User
from rest_framework import serializers

class RegisterSerializer(serializers.ModelSerializer):
    password = serializers.CharField(write_only=True, min_length=8)
    password2 = serializers.CharField(write_only=True, label='Confirm Password')

    class Meta:
        model = User
        fields = ['username', 'email', 'first_name', 'last_name', 'password', 'password2']

    def validate(self, data):
        if data['password'] != data['password2']:
            raise serializers.ValidationError("Passwords do not match.")
        return data

    def create(self, validated_data):
        validated_data.pop('password2')
        # Use create_user so password is hashed
        user = User.objects.create_user(**validated_data)
        return user


class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email', 'first_name', 'last_name']
        read_only_fields = ['id']
```

```python
# accounts/views.py
from django.contrib.auth.models import User
from rest_framework import generics
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework.permissions import IsAuthenticated, AllowAny
from rest_framework.authtoken.models import Token
from .serializers import RegisterSerializer, UserSerializer


class RegisterView(generics.CreateAPIView):
    queryset = User.objects.all()
    serializer_class = RegisterSerializer
    permission_classes = [AllowAny]

    def create(self, request, *args, **kwargs):
        response = super().create(request, *args, **kwargs)
        user = User.objects.get(username=response.data['username'])
        token, _ = Token.objects.get_or_create(user=user)
        return Response({
            'user': UserSerializer(user).data,
            'token': token.key
        }, status=201)


class ProfileView(generics.RetrieveUpdateAPIView):
    serializer_class = UserSerializer
    permission_classes = [IsAuthenticated]

    def get_object(self):
        return self.request.user   # Return the CURRENT user's data


class LogoutView(APIView):
    permission_classes = [IsAuthenticated]

    def post(self, request):
        # Delete the token — user must login again to get a new one
        request.user.auth_token.delete()
        return Response({"message": "Logged out successfully."})
```

```python
# accounts/urls.py
from django.urls import path
from rest_framework.authtoken.views import obtain_auth_token
from . import views

urlpatterns = [
    path('register/', views.RegisterView.as_view(), name='register'),
    path('login/', obtain_auth_token, name='login'),         # Built-in: username + password → token
    path('logout/', views.LogoutView.as_view(), name='logout'),
    path('profile/', views.ProfileView.as_view(), name='profile'),
]

# mysite/urls.py
# path('api/auth/', include('accounts.urls')),
```

---

## 18. Browsable API

DRF's **Browsable API** is an HTML interface that lets you explore and test your API directly in the browser — no Postman or curl needed.

```
Visit http://127.0.0.1:8000/api/posts/ in your browser.

You'll see:
┌──────────────────────────────────────────────────────────────┐
│  Django REST framework                                       │
│  Post List                                                   │
│  GET /api/posts/                                             │
│  ┌────────────────────────────────────────────────────────┐  │
│  │  HTTP 200 OK                                           │  │
│  │  Allow: GET, POST, HEAD, OPTIONS                       │  │
│  │  Content-Type: application/json                        │  │
│  │  {                                                     │  │
│  │    "count": 5,                                         │  │
│  │    "next": null,                                       │  │
│  │    "previous": null,                                   │  │
│  │    "results": [...]                                    │  │
│  │  }                                                     │  │
│  └────────────────────────────────────────────────────────┘  │
│                                                              │
│  [HTML Form to POST new data]                                │
│  Content: ___________                                        │
│  [POST]                                                      │
└──────────────────────────────────────────────────────────────┘
```

No setup needed — it's automatic when `DEBUG=True`.

To disable it in production:
```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',
        # Remove BrowsableAPIRenderer (commented out below — add it for dev only)
        # 'rest_framework.renderers.BrowsableAPIRenderer',
    ],
}
```

---

## 19. Complete Blog API Example

Here's a complete blog API with all pieces connected:

```
my_project/
├── mysite/
│   ├── settings.py
│   └── urls.py
├── blog/
│   ├── models.py
│   ├── serializers.py
│   ├── views.py
│   ├── urls.py
│   └── filters.py
└── accounts/
    ├── serializers.py
    ├── views.py
    └── urls.py
```

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('blog.urls')),
    path('api/auth/', include('accounts.urls')),
    path('api-auth/', include('rest_framework.urls')),   # Login/logout for Browsable API
]
```

```python
# blog/urls.py (using ViewSets + Router)
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register('posts', views.PostViewSet, basename='post')
router.register('tags', views.TagViewSet, basename='tag')
router.register('authors', views.AuthorViewSet, basename='author')

urlpatterns = router.urls
```

All endpoints generated:
```
GET  /api/               ← Root API — lists all endpoints (DRF feature)
GET  /api/posts/         ← List published posts (paginated)
POST /api/posts/         ← Create a post (auth required)
GET  /api/posts/1/       ← Get post with id=1
PUT  /api/posts/1/       ← Replace post 1 (owner only)
PATCH /api/posts/1/      ← Update post 1 (owner only)
DELETE /api/posts/1/     ← Delete post 1 (owner only)
POST /api/posts/1/publish/ ← Custom action: publish post 1
GET  /api/posts/my_posts/  ← Custom action: current user's posts
GET  /api/tags/          ← List all tags
...same for /api/authors/
POST /api/auth/register/ ← Register new user
POST /api/auth/login/    ← Get auth token
POST /api/auth/logout/   ← Delete auth token
GET  /api/auth/profile/  ← Get/update current user
```

---

## 20. DRF Cheat Sheet

```
┌────────────────────────────────────────────────────────────┐
│  DRF Building Blocks                                       │
│                                                            │
│  Serializer        → Convert model ↔ JSON                  │
│  ModelSerializer   → Auto-generate fields from model       │
│  @api_view         → Simple function-based API view        │
│  APIView           → Class-based, method-per-HTTP-verb     │
│  generics.*        → Auto CRUD views (list, create, etc.)  │
│  ModelViewSet      → All CRUD in one class                 │
│  Router            → Auto-generate URLs for ViewSets       │
│  @action           → Custom endpoint on a ViewSet          │
│  Response          → DRF JSON response                     │
│  request.data      → Parsed request body (JSON/form)       │
│  request.query_params → URL query params (?key=value)       │
│  status.HTTP_*     → Status code constants                 │
│  permissions       → Who can access what                   │
│  authentication    → Who made the request                  │
└────────────────────────────────────────────────────────────┘
```

| What you want | Use |
|---|---|
| Simple function view | `@api_view` |
| Handle multiple methods | `APIView` |
| List + Create | `generics.ListCreateAPIView` |
| Full CRUD on one object | `generics.RetrieveUpdateDestroyAPIView` |
| Full CRUD on a resource | `ModelViewSet` + `Router` |
| Auto-generate from model | `ModelSerializer` |
| Computed/custom fields | `SerializerMethodField` |
| Validate fields | `validate_<fieldname>()` |
| Cross-field validation | `validate()` |
| Require login | `IsAuthenticated` permission |
| Owner only writes | Custom `BasePermission` |
| Filter by URL param | `DjangoFilterBackend` |
| Full-text search | `SearchFilter` |
| Sort results | `OrderingFilter` |
| Paginate results | `PageNumberPagination` |
| Token login | `obtain_auth_token` view |
| JWT login | `simplejwt` library |

**You have now completed the full Python + Django + DRF curriculum!**

```
┌────────────────────────────────────────────────────────────┐
│  Learning Path Recap:                                      │
│                                                            │
│  01-Python-Basics.md          → Python syntax, types       │
│  02-Python-Operators-Flow.md  → Operators, loops, if/else  │
│  03-Python-Functions.md       → Functions, closures, deco  │
│  04-Python-Collections.md     → List, dict, set, tuple     │
│  05-Python-OOP.md             → Classes, inheritance, etc. │
│  06-Python-FileIO-Exceptions  → Files, errors, modules     │
│  07-Django-Framework.md       → MVT, models, views, URLs   │
│  08-Django-REST-Framework.md  → APIs, serializers, auth    │
└────────────────────────────────────────────────────────────┘
```
