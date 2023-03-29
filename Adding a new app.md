# Adding a new app

As an example, let’s create a simple Django app called `dashboard`.

Keep the name of the app simple and generic enough. Use `snake_case` if there is more than one word.


## Run startapp

```bash
python manage.py startapp dashboard
```

By default, Django generates a file structure like this:
    
```bash
└── dashboard/
    ├── migrations/
    │   └── __init__.py
    ├── __init__.py
    ├── admin.py
    ├── apps.py
    ├── models.py
    ├── tests.py
    └── views.py
```

## Add a urls.py file

For some reason, Django doesn't create a `urls.py` file when you run `startapp`. Let’s create it.

```bash
touch dashboard/urls.py
```

```python
# dashboard/urls.py

from django.urls import path
# from dashboard import views

app_name = 'dashboard'
urlpatterns = [
    # path('', views.index.as_view(), name='index'),
]

```

## Add the app in the settings

In your project settings file, add the app to the `INSTALLED_APPS` list.

```python
# settings.py

INSTALLED_APPS = [
    # ...
    'dashboard.apps.DashboardConfig',
]
```

Add the `dashboard.urls` module to the `urlpatterns` list in the project `urls.py` file.

```python
# urls.py

from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    # ...
    path('dashboard/', include('dashboard.urls')),
]
```

## Modify the file structure

In a small project, having a single file for your models, views, admins and tests is fine. But in a large project, it’s better to split them into multiple files.

Delete the `models.py`, `views.py`, `admin.py` and `tests.py` files. and create this file structure instead:

```bash
└── dashboard/
    ├── admin/
    │   └── __init__.py
    ├── migrations/
    │   └── __init__.py
    ├── __init__.py
    ├── models/
    │   └── __init__.py
    ├── tests/
        ├── __init__.py
        ├── factories/
        │   └── __init__.py
        ├── test_admin/
        │   └── __init__.py
        ├── test_models/
        │   └── __init__.py
        ├── test_views/
        │   └── __init__.py
        ├── __init__.py
        └── test_factories.py
    ├── views/
    │   └── __init__.py
    ├── apps.py
    └── urls.py
```

As your project grows and depending on your needs, you may want to add more folders to your app. For example:

```bash
└── dashboard/
    ├── # ...
    ├── forms/
    │   ├── __init__.py
    │   └── # ...
    ├── helper/
    │   ├── __init__.py
    │   └── # ...
    ├── locale/
    │   └── # ...
    ├── managers/
    │   ├── __init__.py
    │   └── # ...
    ├── permissions/
    │   ├── __init__.py
    │   └── # ...
    ├── serializers/
    │   ├── __init__.py
    │   └── # ...
    ├── tasks/
    │   ├── __init__.py
    │   └── # ...
    ├── templates/
    │   ├── dashboard
    │   ├── base.html
    │   └── # ...
    └── # ...
```

## Add your files

A general rule of thumb is to create a file for each models, views, admins, etc. For example, if your app has 3 models, here's what your file structure should look like:

```bash
.
└── dashboard/
    # ...
    └── models/
        ├── __init__.py
        ├── projects.py
        ├── report.py
        ├── other_model.py
        └── # ...
    # ...
```

Now, let's say that you want to import the `project` and `report` models in another file, you would need to do this:

```python
from dashboard.models.projects import Project
from dashboard.models.report import Report
```

But it would be better to do this:

```python
from dashboard.models import Project, Report
```

To do this, import your models `dashboard/models/__init__.py` file:

```python
from .projects import Project
from .report import Report
```

The same principle applies for the views, admins, etc.

The groundwork is completed, you are now ready to start working on you app!