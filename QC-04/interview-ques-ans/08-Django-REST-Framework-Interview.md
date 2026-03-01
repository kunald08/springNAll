# Django REST Framework (DRF) — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — confident, structured, and to the point.

---

## 1. What is Django REST Framework? Why use it over plain Django?

**Answer:**
Django REST Framework is a powerful toolkit built on top of Django for building **RESTful APIs**. While Django is designed to serve HTML pages (server-rendered templates), DRF is designed to serve **JSON data** consumed by frontend frameworks (React, Angular), mobile apps, or other backend services.

DRF adds:
- **Serializers** — convert models to/from JSON automatically
- **API Views and ViewSets** — handle CRUD operations with minimal code
- **Routers** — auto-generate URL patterns from ViewSets
- **Authentication** — Token, Session, JWT support
- **Permissions** — granular access control
- **Pagination, filtering, throttling** — built-in
- **Browsable API** — interactive HTML UI for testing APIs in the browser

I use DRF whenever I need to build an API backend — which is the standard architecture in modern applications where the frontend is a separate SPA or mobile app.

---

## 2. What is a REST API? What are the key principles?

**Answer:**
REST (Representational State Transfer) is an **architectural style** for designing networked applications. A REST API uses HTTP methods to perform CRUD operations on resources:

| HTTP Method | CRUD | URL Example | Purpose |
|-------------|------|-------------|---------|
| GET | Read | `/api/articles/` | List all articles |
| GET | Read | `/api/articles/5/` | Get article with id 5 |
| POST | Create | `/api/articles/` | Create a new article |
| PUT | Update | `/api/articles/5/` | Replace article 5 entirely |
| PATCH | Partial Update | `/api/articles/5/` | Update specific fields of article 5 |
| DELETE | Delete | `/api/articles/5/` | Delete article 5 |

Key principles:
- **Stateless** — each request contains all info needed; server doesn't store client state
- **Resource-based** — URLs represent resources (nouns), not actions
- **Uniform interface** — consistent use of HTTP methods and status codes
- **Client-server separation** — frontend and backend are independent

---

## 3. What is a Serializer in DRF? Why is it needed?

**Answer:**
A Serializer converts complex data types (like Django model instances and QuerySets) to **Python native datatypes** that can be rendered into JSON, and vice versa. It handles both **serialization** (model → JSON) and **deserialization** (JSON → model), plus **validation**.

```python
from rest_framework import serializers
from .models import Article

class ArticleSerializer(serializers.Serializer):
    id = serializers.IntegerField(read_only=True)
    title = serializers.CharField(max_length=200)
    content = serializers.CharField()

    def create(self, validated_data):
        return Article.objects.create(**validated_data)

    def update(self, instance, validated_data):
        instance.title = validated_data.get('title', instance.title)
        instance.save()
        return instance
```

Think of serializers as the **bridge** between Django models and JSON. They serve a similar purpose to Django Forms but for API data instead of HTML form data. Without them, I'd have to manually convert model instances to dictionaries, validate input, and handle errors — serializers automate all of that.

---

## 4. What is a ModelSerializer? How is it different from a Serializer?

**Answer:**
`ModelSerializer` is a shortcut that **automatically generates** serializer fields based on the model — similar to how `ModelForm` works for forms:

```python
class ArticleSerializer(serializers.ModelSerializer):
    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'published']
        # Or: fields = '__all__'  (all fields)
        # Or: exclude = ['draft']  (all except these)
        read_only_fields = ['author', 'published']
```

Differences from `Serializer`:
- **Fields are auto-generated** from the model — I don't need to declare each one
- **`create()` and `update()` methods** come pre-implemented
- **Validators** are auto-generated based on model field constraints (max_length, unique, etc.)
- **Relationships** are handled automatically (ForeignKey becomes `PrimaryKeyRelatedField`)

I use `ModelSerializer` in 90% of cases because it eliminates boilerplate. I use the base `Serializer` when I need non-model serialization or highly custom logic.

---

## 5. What are the different types of views in DRF?

**Answer:**
DRF provides views at different abstraction levels:

**1. Function-based: `@api_view`** — simplest, most explicit:
```python
@api_view(['GET', 'POST'])
def article_list(request):
    if request.method == 'GET':
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)
```

**2. Class-based: `APIView`** — organizes methods by HTTP verb:
```python
class ArticleList(APIView):
    def get(self, request):
        articles = Article.objects.all()
        serializer = ArticleSerializer(articles, many=True)
        return Response(serializer.data)

    def post(self, request):
        serializer = ArticleSerializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        serializer.save()
        return Response(serializer.data, status=201)
```

**3. Generic Views** — pre-built CRUD operations:
```python
class ArticleList(generics.ListCreateAPIView):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

**4. ViewSets + Routers** — maximum abstraction:
```python
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
```

I choose based on complexity: ViewSets for standard CRUD, generic views for slightly custom CRUD, APIView for custom logic, and `@api_view` for one-off endpoints.

---

## 6. What are ViewSets and Routers? How do they work together?

**Answer:**
A **ViewSet** combines the logic for all standard actions (list, create, retrieve, update, delete) into a **single class**:

```python
from rest_framework import viewsets

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
```

`ModelViewSet` provides all CRUD operations automatically. I just define the queryset and serializer.

A **Router** automatically generates **URL patterns** from a ViewSet:

```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()
router.register('articles', ArticleViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]
```

This auto-generates:
- `GET /api/articles/` — list
- `POST /api/articles/` — create
- `GET /api/articles/{id}/` — retrieve
- `PUT /api/articles/{id}/` — update
- `PATCH /api/articles/{id}/` — partial update
- `DELETE /api/articles/{id}/` — delete

ViewSets + Routers are the most productive approach — I get a full CRUD API with about 5 lines of code. For custom actions, I use the `@action` decorator.

---

## 7. What is the DRF Request and Response?

**Answer:**
DRF extends Django's `HttpRequest` and `HttpResponse` with enhanced versions:

**`Request`** — provides:
- `request.data` — parsed request body (replaces `request.POST`). Handles JSON, form data, and multipart automatically.
- `request.query_params` — cleaner alias for `request.GET`
- `request.user` — authenticated user (works with DRF authentication)

**`Response`** — content-negotiation aware:
```python
from rest_framework.response import Response
from rest_framework import status

return Response(
    {"message": "Created"},
    status=status.HTTP_201_CREATED
)
```

The key advantage of `Response` is **content negotiation** — it automatically renders data as JSON for API clients or as HTML for the browsable API, based on the `Accept` header. I don't need to manually call `JsonResponse` or handle serialization.

---

## 8. What are HTTP status codes and how does DRF use them?

**Answer:**
Status codes tell the client what happened with their request. DRF provides named constants for readability:

```python
from rest_framework import status

status.HTTP_200_OK              # Success
status.HTTP_201_CREATED         # Resource created (POST)
status.HTTP_204_NO_CONTENT      # Success, no body (DELETE)
status.HTTP_400_BAD_REQUEST     # Invalid input
status.HTTP_401_UNAUTHORIZED    # Not authenticated
status.HTTP_403_FORBIDDEN       # Authenticated but no permission
status.HTTP_404_NOT_FOUND       # Resource doesn't exist
status.HTTP_405_METHOD_NOT_ALLOWED  # Wrong HTTP method
status.HTTP_500_INTERNAL_SERVER_ERROR  # Server error
```

Categories:
- **2xx** — Success
- **3xx** — Redirect
- **4xx** — Client error (bad request, not authorized)
- **5xx** — Server error

Using proper status codes is a REST best practice. Instead of returning `200 OK` with an error message in the body, I return `400 Bad Request` or `404 Not Found` so clients can programmatically handle different situations.

---

## 9. What authentication methods does DRF support?

**Answer:**
DRF supports multiple authentication schemes:

**1. Session Authentication** — uses Django's session/cookie system. Good for browser-based clients:
```python
'rest_framework.authentication.SessionAuthentication'
```

**2. Token Authentication** — each user gets a unique token, sent in the `Authorization` header:
```python
'rest_framework.authentication.TokenAuthentication'
# Client sends: Authorization: Token abc123def456
```

**3. JWT (JSON Web Token)** — stateless tokens with expiration, via `djangorestframework-simplejwt`:
```python
'rest_framework_simplejwt.authentication.JWTAuthentication'
# Client sends: Authorization: Bearer eyJhbGci...
```

**4. Basic Authentication** — username:password in every request (only for testing).

I configure them globally in settings or per-view:
```python
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.TokenAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    ]
}
```

For APIs consumed by mobile apps or SPAs, I typically use **JWT** because it's stateless, supports expiration, and doesn't require server-side session storage.

---

## 10. What is Token Authentication? How do you set it up?

**Answer:**
Token Authentication works by issuing a unique token to each user during login. The client sends this token in every subsequent request for identification:

Setup:
```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework.authtoken',
]
# Then run: python manage.py migrate

# urls.py
from rest_framework.authtoken.views import obtain_auth_token
urlpatterns = [
    path('api/token/', obtain_auth_token),   # Login endpoint
]
```

Flow:
1. Client sends username/password to `/api/token/`
2. Server verifies credentials and returns a **token**
3. Client stores the token and includes it in every request header:
   ```
   Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
   ```
4. DRF's `TokenAuthentication` validates the token and sets `request.user`

The token is stored in the database and doesn't expire by default (unlike JWT). For production, I'd typically use **JWT** which supports expiration, refresh tokens, and is stateless.

---

## 11. What are DRF Permissions? What types are available?

**Answer:**
Permissions determine **who can access** a view. They're checked after authentication and before the view logic runs:

```python
from rest_framework.permissions import IsAuthenticated, IsAdminUser

class ArticleViewSet(viewsets.ModelViewSet):
    permission_classes = [IsAuthenticated]
```

Built-in permission classes:
- **`AllowAny`** — no restrictions, anyone can access
- **`IsAuthenticated`** — only logged-in users
- **`IsAdminUser`** — only staff users (`is_staff=True`)
- **`IsAuthenticatedOrReadOnly`** — authenticated users can modify, anyone can read (GET)

**Custom permissions:**
```python
from rest_framework.permissions import BasePermission

class IsOwnerOrReadOnly(BasePermission):
    def has_object_permission(self, request, view, obj):
        if request.method in ['GET', 'HEAD', 'OPTIONS']:
            return True
        return obj.author == request.user
```

There are two levels: `has_permission()` (view-level — checked before the view) and `has_object_permission()` (object-level — checked per-instance, e.g., "can this user edit THIS article?").

---

## 12. What is the difference between authentication and authorization?

**Answer:**
**Authentication** answers: **"Who are you?"** — verifying the user's identity. After authentication, `request.user` is set to the authenticated user object.

**Authorization** (permissions) answers: **"What are you allowed to do?"** — determining whether the authenticated user has permission to perform the requested action.

In DRF:
- Authentication is handled by `authentication_classes` — Token, JWT, Session
- Authorization is handled by `permission_classes` — IsAuthenticated, IsAdminUser, custom permissions

They work in sequence: DRF first **authenticates** the request (identifies the user), then checks **permissions** (decides if the user is allowed). A request can be authenticated (we know who they are) but still unauthorized (they don't have permission for this action).

---

## 13. What is pagination in DRF? How do you implement it?

**Answer:**
Pagination splits large result sets into smaller pages for performance and usability. DRF supports three styles:

```python
# settings.py — global pagination
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.PageNumberPagination',
    'PAGE_SIZE': 10,
}
```

**1. PageNumberPagination** — `?page=2`:
```json
{"count": 100, "next": "/api/articles/?page=3", "results": [...]}
```

**2. LimitOffsetPagination** — `?limit=10&offset=20`:
```python
'rest_framework.pagination.LimitOffsetPagination'
```

**3. CursorPagination** — opaque cursor-based, best for real-time feeds:
```python
'rest_framework.pagination.CursorPagination'
```

Custom pagination per view:
```python
class ArticlePagination(PageNumberPagination):
    page_size = 20
    page_size_query_param = 'page_size'
    max_page_size = 100

class ArticleViewSet(viewsets.ModelViewSet):
    pagination_class = ArticlePagination
```

For most APIs, `PageNumberPagination` is standard. `CursorPagination` is better for real-time data because it handles insertions gracefully.

---

## 14. How does filtering, searching, and ordering work in DRF?

**Answer:**
DRF provides filter backends for these common operations:

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': [
        'django_filters.rest_framework.DjangoFilterBackend',
        'rest_framework.filters.SearchFilter',
        'rest_framework.filters.OrderingFilter',
    ]
}
```

```python
class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    # Exact match filtering: ?category=tech&status=published
    filterset_fields = ['category', 'status', 'author']

    # Search across fields: ?search=django
    search_fields = ['title', 'content', 'author__name']

    # Ordering: ?ordering=-published (descending)
    ordering_fields = ['published', 'title']
    ordering = ['-published']    # Default ordering
```

**DjangoFilterBackend** (from `django-filter`) enables exact and range filtering. **SearchFilter** does case-insensitive partial matching across multiple fields. **OrderingFilter** lets clients control sort order.

These can be combined: `/api/articles/?category=tech&search=django&ordering=-published` filters by category, searches for "django", and sorts by newest first — all in one request.

---

## 15. What are nested serializers? How do you handle relationships?

**Answer:**
Nested serializers represent related objects as full JSON objects instead of just IDs:

```python
class AuthorSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'username', 'email']

class ArticleSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)   # Nested — shows full author object

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author']
```

Response:
```json
{
    "id": 1,
    "title": "Django Guide",
    "content": "...",
    "author": {
        "id": 5,
        "username": "alice",
        "email": "alice@example.com"
    }
}
```

Without nesting, `author` would just show `5` (the ID). Nesting is great for **reading**, but for **writing**, I typically use `PrimaryKeyRelatedField` or separate fields:

```python
class ArticleSerializer(serializers.ModelSerializer):
    author = AuthorSerializer(read_only=True)       # For reading
    author_id = serializers.IntegerField(write_only=True)  # For writing

    class Meta:
        model = Article
        fields = ['id', 'title', 'content', 'author', 'author_id']
```

---

## 16. What is the Browsable API?

**Answer:**
The Browsable API is DRF's **auto-generated HTML interface** that lets me interact with the API directly in a web browser. When I navigate to an API endpoint in a browser, instead of raw JSON, I see a styled HTML page with:

- The response data formatted nicely
- Forms for POST/PUT requests
- Authentication controls
- Navigation between endpoints

It's enabled by default and works through **content negotiation** — when the `Accept` header is `text/html` (browser), DRF renders HTML; when it's `application/json` (API client), DRF returns JSON.

I can disable it in production if needed:
```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',    # JSON only, no browsable UI
    ]
}
```

It's incredibly useful during development and testing — I can explore and test my API without using Postman or curl.

---

## 17. How do you handle validation in DRF?

**Answer:**
DRF provides three levels of validation:

**1. Field-level validation** — validate a single field:
```python
class ArticleSerializer(serializers.ModelSerializer):
    def validate_title(self, value):
        if len(value) < 5:
            raise serializers.ValidationError("Title must be at least 5 characters")
        return value
```

**2. Object-level validation** — validate multiple fields together:
```python
    def validate(self, data):
        if data['start_date'] > data['end_date']:
            raise serializers.ValidationError("End date must be after start date")
        return data
```

**3. Validator classes** — reusable validators:
```python
from rest_framework.validators import UniqueValidator

title = serializers.CharField(
    validators=[UniqueValidator(queryset=Article.objects.all())]
)
```

Validation runs automatically when `serializer.is_valid()` is called. With `raise_exception=True`, DRF automatically returns a `400 Bad Request` response with structured error messages. ModelSerializer also inherits model-level validation (field constraints, unique checks).

---

## 18. What is throttling in DRF?

**Answer:**
Throttling is **rate limiting** — controlling how many requests a user can make in a given time period. It prevents abuse and protects the API from being overwhelmed:

```python
REST_FRAMEWORK = {
    'DEFAULT_THROTTLE_CLASSES': [
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle',
    ],
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',       # Anonymous users: 100 requests per day
        'user': '1000/day',      # Authenticated users: 1000 per day
    }
}
```

DRF provides:
- **`AnonRateThrottle`** — limits anonymous users by IP address
- **`UserRateThrottle`** — limits authenticated users by user ID
- **`ScopedRateThrottle`** — different limits for different endpoints

When a client exceeds the limit, DRF returns `429 Too Many Requests` with a `Retry-After` header. I can also create **custom throttle classes** for specific business rules, like limiting login attempts.

---

## 19. How do you write tests for DRF APIs?

**Answer:**
DRF provides `APITestCase` and `APIClient` for testing:

```python
from rest_framework.test import APITestCase, APIClient
from rest_framework import status
from django.contrib.auth.models import User

class ArticleAPITest(APITestCase):
    def setUp(self):
        self.user = User.objects.create_user('testuser', password='pass123')
        self.client = APIClient()
        self.client.force_authenticate(user=self.user)

    def test_create_article(self):
        data = {'title': 'Test Article', 'content': 'Test content'}
        response = self.client.post('/api/articles/', data, format='json')
        self.assertEqual(response.status_code, status.HTTP_201_CREATED)
        self.assertEqual(response.data['title'], 'Test Article')

    def test_list_articles(self):
        response = self.client.get('/api/articles/')
        self.assertEqual(response.status_code, status.HTTP_200_OK)

    def test_unauthenticated_access(self):
        self.client.force_authenticate(user=None)
        response = self.client.post('/api/articles/', {})
        self.assertEqual(response.status_code, status.HTTP_401_UNAUTHORIZED)
```

Key features:
- `force_authenticate()` — bypass authentication for testing
- `format='json'` — auto-serialize request data
- Test all CRUD operations, permissions, validation, and edge cases
- Run with `python manage.py test`

---

## 20. What is the `@action` decorator in ViewSets?

**Answer:**
The `@action` decorator lets me add **custom endpoints** to a ViewSet beyond the standard CRUD operations:

```python
from rest_framework.decorators import action

class ArticleViewSet(viewsets.ModelViewSet):
    queryset = Article.objects.all()
    serializer_class = ArticleSerializer

    @action(detail=False, methods=['get'])
    def published(self, request):
        """GET /api/articles/published/"""
        articles = self.queryset.filter(status='published')
        serializer = self.get_serializer(articles, many=True)
        return Response(serializer.data)

    @action(detail=True, methods=['post'])
    def publish(self, request, pk=None):
        """POST /api/articles/5/publish/"""
        article = self.get_object()
        article.status = 'published'
        article.save()
        return Response({'status': 'published'})
```

- `detail=False` — the action applies to the **collection** (no pk): `/api/articles/published/`
- `detail=True` — the action applies to a **single instance** (with pk): `/api/articles/5/publish/`

The router automatically generates URLs for custom actions. This is how I add endpoints like "mark as read," "export data," or "bulk operations" while keeping everything organized in one ViewSet.

---

## 21. How do you handle file uploads in DRF?

**Answer:**
DRF handles file uploads through serializer fields and parsers:

```python
# models.py
class UserProfile(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    avatar = models.ImageField(upload_to='avatars/')

# serializers.py
class ProfileSerializer(serializers.ModelSerializer):
    class Meta:
        model = UserProfile
        fields = ['user', 'avatar']

# views.py
class ProfileViewSet(viewsets.ModelViewSet):
    queryset = UserProfile.objects.all()
    serializer_class = ProfileSerializer
    parser_classes = [MultiPartParser, FormParser]  # Handle file uploads
```

Clients send files using `multipart/form-data`. DRF's `MultiPartParser` handles the parsing, and `ImageField`/`FileField` on the serializer handles validation (file type, size). Files are saved to the `MEDIA_ROOT` directory.

For the request: `Content-Type: multipart/form-data` with the file in the request body. I can also add custom validation for file size and type in the serializer.

---

## 22. What is content negotiation in DRF?

**Answer:**
Content negotiation is the process where DRF determines the **format** of the response based on the client's request. The client can specify its preferred format via:

1. **Accept header**: `Accept: application/json`
2. **URL suffix**: `/api/articles.json` or `/api/articles.api` (browsable)
3. **Query parameter**: `/api/articles/?format=json`

DRF uses **renderers** to output in different formats:
```python
REST_FRAMEWORK = {
    'DEFAULT_RENDERER_CLASSES': [
        'rest_framework.renderers.JSONRenderer',       # JSON output
        'rest_framework.renderers.BrowsableAPIRenderer', # HTML output
    ]
}
```

This is what makes the Browsable API work — when a browser requests the endpoint, DRF detects `Accept: text/html` and renders the HTML version. When Postman or frontend code requests it, the `Accept: application/json` header triggers JSON output. Same view, different representation.
