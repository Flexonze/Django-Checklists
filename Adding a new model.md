# Adding a new model

As an example, let’s create a simple model called `Project`.


## The model

- [ ]  Create a file (*optional)
- [ ]  Create the model class
- [ ]  Import the model in the `__init__.py` file (*optional)
- [ ]  Define the `__str__` method
- [ ]  Define any other needed methods. But, make sure it’s the right place to put this logic. Try to keep your models as logic free as possible.

**Example result:**

```python
# app_name/models/project.py

from django.db import models
from django_extensions.db.models import TimeStampedModel
from core.models import UUIDModel

class Project(UUIDModel, TimeStampedModel):
    title = models.CharField(max_length=512)
    description = models.CharField(max_length=2000, null=True, blank=True)
    created_by = models.ForeignKey(User, on_delete=models.CASCADE, related_name="projects")

    def __str__(self):
        return self.title
```

```python
# app_name/models/__init__.py
from .project import Project
```


## The migration

- [ ]  Generate the migration file

```bash
python manage.py makemigrations
```

- [ ]  Take a look at the generated migration file
- [ ]  Make sure the file is added to git

```bash
git add .
```

- [ ]  Execute the migration

```bash
python manage.py migrate
```


## The model tests

- [ ]  Create a file (*optional)
- [ ]  Create the model test class
- [ ]  Implement a test called `test_can_create_a_<model_name>`
- [ ]  Implement a test called `test_can_print_a_<model_name>`
- [ ]  Implement any other test that would be relevant with your model

**Example result:**

In its minimal form, here’s what the model tests of our `Project` model could look like:

```python
# app_name/tests/test_models/test_project.py

from django.test import TestCase
from app_name.models import Project, User

class TestProject(TestCase):
    def test_can_create_a_project(self):
        project = Project(
            title="title",
            description="description",
	    user = User.objects.create(email="email@example.com")
        )

        project.full_clean()
        project.save()

    def test_can_print_a_project(self):
        project = Project(
            title="title",
            description="description",
            user = User.objects.create(email="email@example.com")
        )
	expected_str = "title"

        self.assertEqual(str(journey), expected_str)
```


## The factory

- [ ]  Create a file (*optional)
- [ ]  Create the factory
- [ ]  Use `factory.Faker` to create dummy data for each fields
- [ ]  Import the factory in the `__init__.py` file (*optional)

**Example result:**

In its minimal form, here’s what the factory of our `Project` model could look like:

```python
# app_name/tests/factories/project_factory.py

import factory
from app_name import models
from app_name.tests.factories import UserFactory

class ProjectFactory(factory.django.DjangoModelFactory):
    title = factory.Faker("sentence", nb_words=5)
    description = factory.Faker("text", max_nb_chars=300)
    user = factory.SubFactory(UserFactory)

    class Meta:
        model = models.Project
```

```python
# app_name/tests/factories/__init__.py
from .project_factory import ProjectFactory
```


## The factory tests

Testing factories may seem overkill at first, but they can actually save you a lot of headaches. The main purpose of testing factories is simply to make sure they are working as expected. You can waste a lot of time debugging a test before realising that the problem actually comes from one of your factories. They are really quick to write, so let’s not cut corners.

- [ ]  Implement a test called `test_<model_name>_factory`

**Example result:**

In its minimal form, here’s what the factory tests of our `ProjectFactory` could look like:

```python
# app_name/tests/test_factories.py

from django.test import TestCase
from app_name import models
from app_name.tests import factories

class TestFactories(TestCase):
    def test_project_factory(self):
	initial_project_count = models.Project.objects.count()
        project_factory = factories.ProjectFactory()
        project_factory.full_clean()
	project_count = models.Project.objects.count()

        self.assertEqual(project_count, initial_project_count + 1)
```


## The admin pages

- [ ]  Create a file (*optional)
- [ ]  Create the admin class
- [ ]  Import the admin in the `__init__.py` file (*optional)
- [ ]  Optionally, customise the pages. Override default behaviours, change what is displayed, etc.
- [ ]  Optionally, on more complex models, you may need to make sure the admin pages does not make too many database queries. You may need to override methods like `get_queryset`, `formfield_for_manytomany` or `formfield_for_foreignkey`.

**Example result:** 

In its minimal form, here’s what the admin page of our `Project` model could look like:

```python
# app_name/admin/project_admin.py

from django.contrib import admin
from django.contrib.admin import ModelAdmin
from app_name.models import Project

@admin.register(Project)
class ProjectAdmin(ModelAdmin):
    pass
```

```python
# app_name/admin/__init__.py
from .project_admin import ProjectAdmin
```

## The admin pages tests

The main purpose of testing the admin pages is to make sure there are no optimisation issues. (If the number of queries directly relates to the number of objects displayed in the page, the test will tell you. You can then go back to the admin page code to fix the issue)

- [ ]  Create a file (*optional)
- [ ]  Create the admin test class
- [ ]  Implement create dummy data in `setUpTestData` using your factories
- [ ]  Login as a superuser in `setUp`
- [ ]  Implement a test called `test_changelist`
- [ ]  Implement a test called `test_add`
- [ ]  Implement a test called `test_change`

**Example result:** 

In its minimal form, here’s what the admin tests of our `ProjectAdmin` pages could look like:

```python
# app_name/tests/test_admin/test_project_admin.py

from django.test import TestCase, Client
from django.urls import reverse
from app_name.tests import factories

class TestProjectAdmin(TestCase):
    @classmethod
    def setUpTestData(cls):
        cls.user = factories.UserFactory(is_superuser=True)
	cls.projects = factories.ProjectFactory.create_batch(6)

    def setUp(self):
        self.client.force_login(self.user)

    def test_changelist(self):
        url = reverse("admin:app_name_project_changelist")

        with self.assertNumQueries(5):  # Make sure this number doesn't increase if the number if objects created in setUpTestData inscreases.
            response = self.client.get(url)

        self.assertEqual(response.status_code, 200)

    def test_add(self):
        url = reverse("admin:app_name_project_add")

        with self.assertNumQueries(5):
            response = self.client.get(url)

        self.assertEqual(response.status_code, 200)

	def test_change(self):
        url = reverse(
            "admin:app_name_project_change", kwargs={"object_id": self.projects[0].id}
        )

        with self.assertNumQueries(5):
            response = self.client.get(url)

        self.assertEqual(response.status_code, 200)
```  

---
<aside>
📌 *Optional: I personally prefer having each of my models in a separate file. The same principle applies for model test classes, admin classes, etc. You can skip these steps if you prefer working with a different file structure. (Ex: all models in the same models.py file)
</aside>
