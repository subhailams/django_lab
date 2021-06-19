Please Follow Below Steps

1. [Download Python](https://www.python.org/downloads/)
2. Ensure pip is installed with pip --version if not [install pip](https://pip.pypa.io/en/stable/installing/#installing-with-get-pip-py)

#Django Sending Emails and REST APIs

Step 1 : Setup Environment
```
pip install virtualenv
```
```
virtualenv denv
```
```
denv\Scripts\activate
```
Step 2: Install django-rest-authemail

Django REST Framework will be installed.

```
pip install django-rest-authemail
```

Step 3: Start django project
```
django-admin startproject django_project
```

Step 4: Add REST to django_project/settings.py

```
INSTALLED_APPS = [
	...
	'rest_framework',
	'rest_framework.authtoken',
	...
]

REST_FRAMEWORK = {
	'DEFAULT_AUTHENTICATION_CLASSES': (
		'rest_framework.authentication.TokenAuthentication',
	)
}
```

Step 5: Create a Django application for your user data.  For example,

```
python manage.py startapp accounts
```

In the `settings.py` file of your project, include `authemail` and your application in `INSTALLED_APPS`. 
Set `AUTH_USER_MODEL` to the class of your user model.  For example,

```
INSTALLED_APPS = [
	...
	'rest_framework',
	'rest_framework.authtoken',
	'authemail',
	'accounts',
	...
]

AUTH_USER_MODEL = 'accounts.MyUser'

```

Create Super User Admin
```
python manage.py createsuperuser
```

Check your setup by starting a Web server on your local machine:

```
python manage.py runserver
```

Step 6: Create Database model for your user data.

In the `accounts/models.py` file of your application,

```
from django.db import models
from authemail.models import EmailUserManager, EmailAbstractUser

class MyUser(EmailAbstractUser):
	# Custom fields
	date_of_birth = models.DateField('Date of birth', null=True, blank=True)

	# Required
	objects = EmailUserManager()
```

Step 7 : In the `accounts/admin.py` file of your project, extend `EmailUserAdmin` to add your custom fields.  For example,

```
from django.contrib import admin
from django.contrib.auth import get_user_model
from authemail.admin import EmailUserAdmin

class MyUserAdmin(EmailUserAdmin):
	fieldsets = (
		(None, {'fields': ('email', 'password')}),
		('Personal Info', {'fields': ('first_name', 'last_name')}),
		('Permissions', {'fields': ('is_active', 'is_staff', 
									   'is_superuser', 'is_verified', 
									   'groups', 'user_permissions')}),
		('Important dates', {'fields': ('last_login', 'date_joined')}),
		('Custom info', {'fields': ('date_of_birth',)}),
	)

admin.site.unregister(get_user_model())
admin.site.register(get_user_model(), MyUserAdmin)
```

Step 8 :  Create the database tables with Django's `makemigrations`, `migrate`, and create a superuser with `createsuperuser`.

```
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
```

Check your setup by starting a Web server on your local machine:

```
python manage.py runserver
```

Go to  http://127.0.0.1:8000/admin/


You should see `Users`, `Tokens`, `Password reset codes`, `Signup codes`, and `Groups`.  If you click on `Users`, you should see your superuser account.

Step 9 : Add the `authemail` API endpoints to your project's `django_project/urls.py` file.  For example,

```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
	path('admin/', admin.site.urls),

	path('api/accounts/', include('authemail.urls')),
]
```
When users signup or reset their password, they will be sent an email with a link and verification code.  

Step 10: Include email settings as environment variables or in your project's `django_project/settings.py` file.  For example,

```

# Email settings
# https://docs.djangoproject.com/en/3.1/topics/email/
# https://docs.djangoproject.com/en/3.1/ref/settings/#email-host

import os

EMAIL_FROM = os.environ.get('AUTHEMAIL_DEFAULT_EMAIL_FROM') or '<YOUR DEFAULT_EMAIL_FROM HERE>'
EMAIL_BCC = os.environ.get('AUTHEMAIL_DEFAULT_EMAIL_BCC') or '<YOUR DEFAULT_EMAIL_BCC HERE>'

EMAIL_HOST = os.environ.get('AUTHEMAIL_EMAIL_HOST') or 'smtp.gmail.com'
EMAIL_PORT = os.environ.get('AUTHEMAIL_EMAIL_PORT') or 587
EMAIL_HOST_USER = os.environ.get('AUTHEMAIL_EMAIL_HOST_USER') or '<YOUR EMAIL_HOST_USER HERE>'
EMAIL_HOST_PASSWORD = os.environ.get('AUTHEMAIL_EMAIL_HOST_PASSWORD') or '<YOUR EMAIL_HOST_PASSWORD HERE>'
EMAIL_USE_TLS = True
EMAIL_USE_SSL = False
```

Final Step : 


Django REST Framework Browsable API
-----

If you are having trouble getting your code to execute, or are just curious, try out the Django REST Framework Browsable API.  If you type an `authemail` API endpoint into your browser, the Browsable API should appear (`runserver` should still be executing).  For example,


Go to  http://127.0.0.1:8000/api/accounts/signup

Enter information in the HTML form fields of the Browsable API


Then click on `POST`.  You will either receive an error message to help in your debugging, or, if your signup was successful:

If not working turn on less secure apps
https://myaccount.google.com/u/1/lesssecureapps?pli=1&pageId=none

After receiving the code goto below url by adding the code like below. For examples,


Goto http://127.0.0.1:8000/api/accounts/signup/verify/?code=e53c0e8fb6ede000f7e4b260443348b162b85229


Link from email directly will not work, you have to append 'api/accounts/' before 'signup/'

