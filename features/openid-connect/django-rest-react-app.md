# Django Rest React App

In this example we show you how to get user data from Defguard to create user session in your django app.

#### Configuring client

As in all of the steps above first step is to add your app in Defguard OpenID apps.

#### Django setup

1. Install [Authlib](https://docs.authlib.org/en/latest/)
2. Create new app and name it oauth using `python3 manage.py createapp oauth`
3. In newly created oauth app go to views.py file

First step is to create our defguard client configuration and import all necessary functions

```
import os

from authlib.integrations.django_client import OAuth
from django.contrib import auth
from django.shortcuts import redirect
from rest_framework import status
from rest_framework.authentication import SessionAuthentication
from rest_framework.authtoken.admin import User
from rest_framework.authtoken.models import Token
from rest_framework.decorators import api_view, authentication_classes
from rest_framework.response import Response

# Create defguard oauth client
oauth = OAuth()
defguard = oauth.register(
    name="defguard",
    client_id=os.getenv("DEFGUARD_CLIENT_ID", "DEFGUARD_CLIENT_ID"),
    client_secret=os.getenv("DEFGUARD_CLIENT_SECRET", "DEFGUARD_CLIENT_SECRET"),
    access_token_url=os.getenv("DEFGUARD_ACCESS_TOKEN_URL", "http://defguard.com/api/v1/oauth/token"),
    access_token_params=None,
    authorize_url=os.getenv("DEFGUARD_AUTHORIZE_URL", "http://defguard.com/v1/oauth/authorize"),
    api_base_url=os.getenv("DEFGUARD_API_BASE_URL", "http://defguard.com/api/v1/oauth/userinfo"),
    client_kwargs={"scope": os.getenv("DEFGUARD_SCOPE", "openid email profile")},
    server_metadata_url=os.getenv("DEFGUARD_METADATA_URL", "http://defguard.com/.well-known/openid-configuration"),
)
```

Remember to add appropriate values to match your defguard instance.

In the next step we have to create login endpoint which will redirect users from our app to Defguard consent page.

```
def defguard_login(request):
    """Build a full authorize callback uri."""
    redirect_uri = request.build_absolute_uri(EXAMPLE_DEFGUARD_REDIRECT_URL)
    return oauth.defguard.authorize_redirect(request, redirect_uri)
```

**Note** `EXAMPLE_DEFGUARD_REDIRECT_URL` is url for our authorize endpoint which we discuss in the next step. We recommend to add it to `settings.py` file like this:

```
# Our app url to consume defguard token
EXAMPLE_DEFGUARD_REDIRECT_URL = os.getenv('EXAMPLE_DEFGUARD_REDIRECT_URL', 'http://localhost:3000/api/oauth/redirect')
```

our endpoint to consume tokens will look like this as our app uses [Django Rest token authentication](https://www.django-rest-framework.org/api-guide/authentication/#tokenauthentication), we need to extract user data from the token received from defguard and transform it to a Django Rest token. at the end of the function, we login user to a session so we can get it later from our react app and at the end, we returns redirect to the frontend main page.

```
def defguard_authorize(request):
    """Exchange authorization code for token."""
    token = oauth.defguard.authorize_access_token(request)
    resp = oauth.defguard.get("userinfo", token=token)
    resp.raise_for_status()
    profile = resp.json()
    user = User.objects.filter(username=profile["sub"]).exists()
    if not user:
        user = User(
            is_active=True,
            username=profile["sub"],
            email=profile["email"],
            first_name=profile["given_name"],
            last_name=profile["family_name"],
        )
    else:
        user = User.objects.get(username=profile["sub"])
    auth.login(request, user)
    # return redirect to frontend
    return redirect("/")
```

Our session endpoint from which our React app can receive token.

```
@api_view(["GET"])
@authentication_classes([SessionAuthentication])
def get_me(request):
    """Return token based on user in session."""
    user = request.user
    token, _ = Token.objects.get_or_create(user=user)
    return Response({"token": token.key}, status=status.HTTP_200_OK)
```

and our logout endpoint where we remove token from session

```
@api_view(["GET"])
def logout(request):
    """Logout user."""
    auth.logout(request)
    data = {
        "message": "You have successfully logged out.",
    }
    return Response(data, status=status.HTTP_200_OK)
```

Then we need to add our views to `oauth/urls.py` file.

```
from django.urls import path

from apps.oauth.views import defguard_authorize, defguard_login, get_me, logout

urlpatterns = [
    path('defguard-login/', defguard_login),
    path('redirect/', defguard_authorize),
    path('me/', get_me),
    path('logout/', logout),
]
```

Thats it on the backend site if we don't want to hold users in our database and only create session for them. But if you want to create users you need to call `user.save()` in `defguard_authorize` function we recommend to set some strong password for this users like uuid or base64 they won't need it anyway because they login through defguard.

#### React app

On your login page add login with defguard button which `onClick` will redirect to our `defguard-login/` endpoint.

```
<button onClick={() => (window.location.href = `${API_URL}/oauth/defguard-login/`)}>
  Login with Defguard
</button>
```

**Note** `API_URL`is url of your django backend api When the user clicks on button he will be redirected to the defguard page where he has to login with his defguard account and permit our app to collect his profile data.

After redirect you can call `me/` endpoint in your react app to receive auth token and add it to your app sate.
