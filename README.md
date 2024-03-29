# Django Practices #

🎯 Django Practices 🎯

Inspired by [William Vincent](https://wsvincent.com/about/)'s 
[Django Best Practices](https://wsvincent.com/django-best-practices/)

These are the practices we currently use at 
[Lakesite.Net](https://lakesite.net/) when developing 
[Django](https://www.djangoproject.com/) apps.  We take a few positions on what
we consider a best practice, but recognize they're only appropriate to the 
context of work we do.  For example, we prefer Vagrant and ansible over Docker
for a lot of projects.

## Why Django? ##

Django's strength lies in being well documented and maintained, with a superb 
ORM.  With [django-rest-framework](https://www.django-rest-framework.org/), it's 
a great way to create APIs.  It gives you a fast way to setup an app with an 
admin interface, where all you need to do is define your models.

In short: Use Django when you need to rapidly prototype, develop, and maintain
budget projects which need to last and minimally require CRUD which you can
iterate over to gradually replace the stock admin interface.

## Target Platform ##

As of Dec 2019, we develop against:

* Ubuntu 18.04.3 LTS - Bionic - Supported until April 2023.
* Django 2.2 LTS - Supported until April 2022.

## Setup ##

* Use [Pipenv](https://pipenv.kennethreitz.org/en/latest/) to manage virtual environments.
* Always start with a custom user model.
* Use [generic class based views](https://docs.djangoproject.com/en/dev/topics/class-based-views/generic-display/) if possible, otherwise prefer [class based views](https://docs.djangoproject.com/en/dev/topics/class-based-views/) to function based views.
* Use [decouple](https://github.com/henriquebastos/python-decouple) and environment variables for anything secret like SECRET_KEY.
* Use multiple setting files so DEBUG never runs in production.
* Have unit and integration tests.

## Development ##

* Use a development configuration with [Vagrant](https://vagrantup.com/)
* Use a staging environment with the same configuration as Vagrant.
* Use ansible where possible.

## Security ##

* Use [Pony Checkup](https://www.ponycheckup.com/) to check your site's security.
* Target the latest LTS version of Django, or migrate to the latest LTS quickly.
* Use Let's Encrypt and ensure https for staging and production.
* Change the admin url to anything but /admin/

## 3rd Part Packages ##

* Use [django-allauth](https://github.com/pennersr/django-allauth) for social auth and user email confirmation.
* Use [Sentry](https://sentry.io/welcome/) for logging errors.
* Use [Whitenoise](https://whitenoise.readthedocs.io/) for serving static files.

## Naming ##

Django applications should have as a convention a repository/project name of:

django-```{APP_NAME}```

Django projects which use apps don't have this requirement and can simply be
named:

```{PROJECT_NAME}```

## Layout ##

In general, use the following layout for projects and django apps:

### Projects ###

A typical project structure will look like the following:

```
/                       - Main repository.

    * README.md 
    * LICENSE.md
    * .gitignore 
    * manage.py 
    * Vagrantfile
    * provision.sh (optional)
    * requirements.txt (optional)
    * Pipfile

/docs                   - Documents.
/licenses               - License files.
/{PROJECT}              - Django Project.
/{PROJECT}/settings/    - Settings (production.py, development.py, staging.py)
/{PROJECT}/apps/        - Django Applications.
/{PROJECT}/apps/{APP}   - An example application.
/ansible                - ansible plays.
```

### Project Settings ###

Each project should have a settings package API for managing configurations for
development, staging and production environments.  The simple convention is:

```
/settings
    /__init__.py        - Define a regular package.
    /base.py            - Base settings that apply to any environment.
    /development.py     - Settings for development which imports from base.
    /staging.py         - Settings for staging which imports from base.
    /production.py      - Settings for production which imports from base.
```

The first line from development.py, staging.py and production.py are:

```from .base import *```

Using these settings in development, staging or production can be defined 
explicitly e.g.;

```
(pipenv-environment) $ python manage.py runserver --settings=PROJECT_NAME.settings.development
```

Or by using an environment variable with DJANGO_SETTINGS_MODULE:

```
(pipenv-environment) $ export DJANGO_SETTINGS_MODULE=PROJECT_NAME.settings.staging
(pipenv-environment) $ python manage.py runserver
```

### Apps ###

A typical app that we're packaging will look like the following:

```
/                       - Main repository.

    * README.md
    * LICENSE.md
    * .gitignore 
    * setup.py

/docs                   - Documents.
/licenses               - License files.
/example_project        - An example Django project which uses the app in /
/{APP}                  - App folder containing __init__.py

    * __init__.py
    * models.py
    * admin.py
    * views.py
    * urls.py
    * ...
```

/{APP}/migrations       - App specific migrations.

## Documentation ##

Projects should generally contain a /docs folder with the following:

```
/docs/index.md          - Table of contents or main document index.
/docs/rationale.md      - Why are we doing this?  What problem are we solving? etc.
/docs/platform.md       - Target platform (Ubuntu 18.04.3 / Django 2.2.x)
/docs/install.md        - Installation instructions.
/docs/running.md        - How to run the application.
/docs/standards.md      - Standards for the project not already defined through this document.
/docs/development.md    - How to properly setup a development environment.
```

Each section should be referenced where appropriate from README.md.

## Licensing ##

The main repository for any project should have a LICENSE.md file per convention,
and the README.md should summarize what the code is licensed under.  Additional
3rd party licenses should be referenced under /licenses/