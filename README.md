Below is a full-stack blog application with React (frontend) and Django REST Framework (backend). You can copy this code into separate folders as instructed.

---

### 📁 Backend: Django REST Framework (`/backend`)

#### 1. Setup
```bash
pip install django djangorestframework djangorestframework-simplejwt corsheaders
```

#### 2. blog_backend/settings.py
```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    'corsheaders',
    'blog',
    'django.contrib.sites',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
    ...
]

CORS_ALLOW_ALL_ORIGINS = True

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ),
}
```

#### 3. blog/models.py
```python
from django.db import models
from django.contrib.auth.models import User

class BlogPost(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

#### 4. blog/serializers.py
```python
from rest_framework import serializers
from .models import BlogPost
from django.contrib.auth.models import User

class BlogPostSerializer(serializers.ModelSerializer):
    author = serializers.ReadOnlyField(source='author.username')
    class Meta:
        model = BlogPost
        fields = '__all__'

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ('id', 'username', 'email', 'password')
        extra_kwargs = {'password': {'write_only': True}}

    def create(self, validated_data):
        user = User.objects.create_user(**validated_data)
        return user
```

#### 5. blog/views.py
```python
from rest_framework import viewsets, permissions, generics
from .models import BlogPost
from .serializers import BlogPostSerializer, UserSerializer
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

class BlogPostViewSet(viewsets.ModelViewSet):
    queryset = BlogPost.objects.all().order_by('-created_at')
    serializer_class = BlogPostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]

    def perform_create(self, serializer):
        serializer.save(author=self.request.user)

class UserCreateView(generics.CreateAPIView):
    serializer_class = UserSerializer
```

#### 6. blog/urls.py
```python
from django.urls import path, include
from rest_framework.routers import DefaultRouter
from .views import BlogPostViewSet, UserCreateView
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

router = DefaultRouter()
router.register(r'blogs', BlogPostViewSet)

urlpatterns = [
    path('', include(router.urls)),
    path('register/', UserCreateView.as_view(), name='register'),
    path('token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
    path('token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
]
```

#### 7. blog_backend/urls.py
```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('blog.urls')),
]
```

#### 8. Migrate and Run
```bash
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

---

### 📁 Frontend: React (`/frontend`)

#### 1. Setup
```bash
npx create-react-app blog-frontend
cd blog-frontend
npm install axios react-router-dom jwt-decode
```

#### 2. src/App.js
```jsx
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import Login from './Login';
import Register from './Register';
import BlogList from './BlogList';
import BlogDetail from './BlogDetail';
import CreateBlog from './CreateBlog';
import EditBlog from './EditBlog';

function App() {
  return (
    <Router>
      <Routes>
        <Route path="/" element={<BlogList />} />
        <Route path="/login" element={<Login />} />
        <Route path="/register" element={<Register />} />
        <Route path="/blogs/:id" element={<BlogDetail />} />
        <Route path="/create" element={<CreateBlog />} />
        <Route path="/edit/:id" element={<EditBlog />} />
      </Routes>
    </Router>
  );
}
export default App;
```

#### 3. Components: [Let me know if you want each React component code next.]
- `Login.js`
- `Register.js`
- `BlogList.js`
- `BlogDetail.js`
- `CreateBlog.js`
- `EditBlog.js`

---

Would you like me to paste all React component files now (step-by-step)?
