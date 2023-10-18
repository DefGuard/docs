# Django

This article aims to show the basic integration of authenticating users through **Defguard** via **OpenID Connect**. So you can have a solid start to adjust it for your own use case.

## The Setup

### Domain

This guide assumes both **Defguard** and **Django** are running on **localhost**.

### Defguard

We will run Defguard instance on default port <mark style="color:blue;">8000</mark>.

{% hint style="info" %}
You can learn how to launch your Defguard instance in the following article: [setting-up-your-instance](../setting-up-your-instance/ "mention")
{% endhint %}

#### Configuration

For our example to work on localhost we will need to change the following variables in Defguard:

| Variable                   | Value                 |
| -------------------------- | --------------------- |
| DEFGUARD\_URL              | http://localhost:8000 |
| DEFGUARD\_COOKIE\_DOMAIN   | localhost             |
| DEFGUARD\_COOKIE\_INSECURE | true                  |

{% hint style="danger" %}
Because we use **localhost** domain we need to set cookies to insecure, **DON'T** do this in a production environment.
{% endhint %}

Next, we need to configure the OpenID module to use RSA key instead of the default HMAC, this is due to Authlib being incompatible with HMAC.

Generate RSA key with the following command:

```bash
openssl genpkey -out rsakey.pem -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Now we need to set **DEFGUARD\_OPENID\_KEY** variable to path pointing to that _<mark style="color:purple;">rsakey.pem</mark>_ file.

When starting Defguard now you should be able to see the following info log :&#x20;

```log
INFO defguard: Using RSA OpenID signing key
```

### Django

This section will explain how to setup a fresh Django example project.

We will use [poetry](https://python-poetry.org/) as a package manager but [pip](https://pip.pypa.io/en/stable/) will also work fine.

#### Project

Setup a new project with poetry, we will name it _django-project_.

```bash
poetry new django-project && cd ./django-project
```

Delete the generated _**django\_project**_ directory, we don't need it.

```bash
rm -rd ./django_project/
```

#### Packages

Install the following Python packages:

* django
* django-jazzmin
* Authlib
* requests

```bash
poetry add django django-jazzmin Authlib requests
```

#### Django

Now we will make Django project and add **oauth** app.

```bash
poetry run django-admin startproject example .
```

```
poetry run ./manage.py startapp oauth
```

With this, we should have a directory structure close to this:

```
├── example
│   ├── asgi.py
│   ├── __init__.py
│   ├── __pycache__
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── manage.py
├── oauth
│   ├── admin.py
│   ├── apps.py
│   ├── __init__.py
│   ├── migrations
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── poetry.lock
├── pyproject.toml
└── README.md
```

## Register OpenID App

We need to register our Django application as an OpenID client in Defguard.

To do that, navigate to OpenID panel and add new client as shown below.

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Redirect URL should point to **http://localhost:9000/oauth/redirect**

Scopes should include at least **OpenID**, **Profile,** and **Email**.

## Authentication app setup

### Register app in Django

We will use the created **oauth** Django app to handle our authentication.

Register oauth app in _**settings.py**_ file.

```python
INSTALLED_APPS = [
    # ...rest of your apps
    'oauth.apps.OauthConfig',
]
```

### Views

Modify _<mark style="color:purple;">oauth/views.py</mark> file._

```python
from authlib.integrations.django_client import OAuth
from os import getenv

from django.contrib import auth
from django.contrib.auth.models import User
from django.shortcuts import redirect

oauth = OAuth()

defguard = oauth.register(
    name="defguard",
    client_id=getenv("DEFGUARD_CLIENT_ID"),
    client_secret=getenv("DEFGUARD_CLIENT_SECRET"),
    access_token_url=getenv("DEFGUARD_ACCESS_TOKEN_URL", "http://localhost:8000/api/v1/oauth/token"),
    access_token_params=None,
    authorize_url=getenv("DEFGUARD_AUTHORIZE_URL", "http://localhost:8000/api/v1/oauth/authorize"),
    api_base_url=getenv("DEFGUARD_API_BASE_URL", "http://localhost:8000/api/v1/oauth/userinfo"),
    client_kwargs={"scope": getenv("DEFGUARD_SCOPE", "openid email profile")},
    server_metadata_url=getenv("DEFGUARD_METADATA_URL", "http://localhost:8000/.well-known/openid-configuration"),
)

REDIRECT_URL = getenv("DEFGUARD_REDIRECT_URL")

def defguard_login(request):
    redirect_uri = request.build_absolute_uri(REDIRECT_URL)
    return oauth.defguard.authorize_redirect(request, redirect_uri)

def defguard_authorize(request):

    token = oauth.defguard.authorize_access_token(request)

    resp = oauth.defguard.get("userinfo", token=token)

    resp.raise_for_status()
    profile = resp.json()
    user = None
    user_exists = User.objects.filter(username=profile["sub"]).exists()
    if not user_exists:
        user = User(
            is_active=True,
            is_staff=True,
            is_superuser=True,
            username=profile["sub"],
            email=profile["email"],
            first_name=profile["given_name"],
            last_name=profile["family_name"],
        )
        user.save()
    else:
        user = User.objects.get(username=profile["sub"])
    auth.login(request, user)
    return redirect("/admin")
```

With the provided example, you will need to fill out only **DEFGUARD\_CLIENT\_ID** and **DEFGUARD\_CLIENT\_SECRET**.&#x20;

Either provide them as environment variables or modify the views file and pass them as strings to oauth register function.

Both Client **ID** and **Secret** can be found on OpenID apps page in Defguard, **click** our Django app **row** on the list and you will be able to copy needed values from the opened modal.

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

### URLS

We will need to add our views to _<mark style="color:purple;">**oauth/urls.py**</mark>_.

```python
from django.urls import path
from oauth.views import defguard_authorize, defguard_login

urlpatterns = [
    path("defguard-login", defguard_login),
    path("redirect", defguard_authorize),
]
```

Modify _<mark style="color:purple;">**example/urls.py**</mark>_ file, so it includes oauth app urls:

```python
from django.contrib import admin
from django.urls import path, include
from django.contrib.auth.models import Group

admin.site.unregister(Group)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('oauth/', include('oauth.urls')),
]

```

## Custom admin login template

With use of [Jazzmin](https://django-jazzmin.readthedocs.io/) admin theme we will modify login template and add an additional button to login with Defguard.

### Register Jazzmin app

Modify _<mark style="color:purple;">**example/settings.py**</mark>_

```python
INSTALLED_APPS = [
    'jazzmin',
    'django.contrib.admin',
    # rest of the apps
]

# rest of config

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [BASE_DIR / "templates"],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

{% hint style="warning" %}
jazzmin app **needs** to be registered **before** django.contrib.admin
{% endhint %}

### Add template file

Make _<mark style="color:purple;">**templates/admin/auth/login.html**</mark>_ file:

```django
{% raw %}
{% extends "registration/base.html" %}

{% load i18n jazzmin %}
{% get_jazzmin_settings request as jazzmin_settings %}
{% get_jazzmin_ui_tweaks as jazzmin_ui %}

{% block content %}
    <p class="login-box-msg">{{ jazzmin_settings.welcome_sign }}</p>
    <form action="{{ app_path }}" method="post">
        {% csrf_token %}
        {% if user.is_authenticated %}
            <p class="errornote">
                <div class="callout callout-danger">
                    <p>
                        {% blocktrans trimmed %}
                            You are authenticated as {{ username }}, but are not authorized to
                            access this page. Would you like to login to a different account?
                        {% endblocktrans %}
                    </p>
                </div>
            </p>
        {% endif %}
        {% if form.errors %}
            {% if form.username.errors %}
                <div class="callout callout-danger">
                    <p>{{ form.username.label }}: {{ form.username.errors|join:', ' }}</p>
                </div>
            {% endif %}
            {% if form.password.errors %}
                <div class="callout callout-danger">
                    <p>{{ form.password.label }}: {{ form.password.errors|join:', ' }}</p>
                </div>
            {% endif %}
            {% if form.non_field_errors %}
                <div class="callout callout-danger">
                    {% for error in form.non_field_errors %}
                        <p>{{ error }}</p>
                    {% endfor %}
                </div>
            {% endif %}
        {% endif %}
        <div class="input-group mb-3">
            <input type="text" name="username" class="form-control" placeholder="{{ form.username.label }}" required>
            <div class="input-group-append">
                <div class="input-group-text">
                    <span class="fas fa-user"></span>
                </div>
            </div>
        </div>
        <div class="input-group mb-3">
            <input type="password" name="password" class="form-control" placeholder="{{ form.password.label }}" required>
            <div class="input-group-append">
                <div class="input-group-text">
                    <span class="fas fa-lock"></span>
                </div>
            </div>
        </div>
        {% url 'admin_password_reset' as password_reset_url %}
        {% if password_reset_url %}
            <div class="mb-3">
                <div class="password-reset-link" style="text-align: center;">
                    <a href="{{ password_reset_url }}">
                        {% trans 'Forgotten your password or username?' %}
                    </a>
                </div>
            </div>
        {% endif %}
        <div class="row">
            <div class="col-12">
                <button type="submit" class="btn {{ jazzmin_ui.button_classes.primary }} btn-block">
                    {% trans "Log in" %}
                </button>
            </div>
        </div>
    </form>
    <div class="row" style="padding-top: 8px">
        <div class="col-12">
            <button class="btn {{ jazzmin_ui.button_classes.secondary }} btn-block"
                    onclick="window.location.href = '/oauth/defguard-login'">
              Login with Defguard
            </button>
        </div>
    </div>
{% endblock %}
{% endraw %}

```

### Register login route

Modify _<mark style="color:purple;">**example/urls.py**</mark>_

```python
from django.contrib import admin
from django.urls import path, include
from django.contrib.auth.models import User, Group
from django.contrib.auth.views import LoginView

admin.site.unregister(Group)

urlpatterns = [
    path('admin/login/', LoginView.as_view(template_name="admin/auth/login.html"), name="admin_login"),
    path('admin/', admin.site.urls),
    path('oauth/', include('oauth.urls')),
]

```

## Conclusion

Now we need to start our Django server.

{% hint style="warning" %}
If you started a fresh project don't forget to make migrations!

**`poetry run ./manage.py migrate`**
{% endhint %}

```bash
poetry run ./manage.py runserver 0.0.0.0:9000
```

After accessing _http://localhost:9000/admin we should see our custom login page_

Button "_Login with Defguard_" should redirect us to our Defguard instance. Depending on if Defguard session is active or not we should be able to see app authorization page or login page.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

When we authorize Django App to our Defguard account we are redirected back to our Django admin and logged in with a user from Defguard.
