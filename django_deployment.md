# Andreas Jud's Cheat Sheet

## Django Deployment

### Table of Contents

1. Environment Variables
2. Static Files
3. Media Files
4. CSS
5. Database
6. Email
7. Github
8. Web Server
9. Deploy

<br />

## 1. Environment Variables

### Store sensitive data in .env file within the core folder

#### terminal

```sh
  pip install django-environ
```

#### settings.py

```sh
  from environ import Env
  env = Env()
  Env.read_env()

  ENVIRONMENT = env('ENVIRONMENT', default="production")
```

### ,env

```sh
  ENVIRONMENT=development
```

#### SECRET KEY

```sh
  SECRET_KEY=abcde12345
  SECRET_KEY = env('SECRET_KEY')
```

<br />

### Debug mode

#### settings.py

```sh
if ENVIRONMENT == 'development':
    DEBUG = True
else:
    DEBUG = False
```

<br />

### Allowed Hosts

#### settings.py

```sh
  ALLOWED_HOSTS = ['*']
```

<br />

## 2. Static Files

### Serving Static files in Production

#### terminal

```sh
  pip install whitenoise
```

#### settings.py

```sh
  MIDDLEWARE = [
      ...
      'whitenoise.middleware.WhiteNoiseMiddleware',
  ]

  STATIC_ROOT = BASE_DIR / 'staticfiles' 
```

#### terminal

```sh
  python manage.py collectstatic
```

<br />

## 3. Media Files

### Example for using Cloudinary as Media Server

#### terminal

```sh
  pip install django-cloudinary-storage
```

#### settings.py

```sh
  INSTALLED_APPS = [
      'cloudinary_storage',
      'cloudinary',
  ]
```

```sh
  MEDIA_URL = 'media/'

  if ENVIRONMENT == 'development':
      MEDIA_ROOT = BASE_DIR / 'media'
  else:
      DEFAULT_FILE_STORAGE = 'cloudinary_storage.storage.MediaCloudinaryStorage'
      CLOUDINARY_STORAGE = {
          'CLOUDINARY_URL': env('CLOUDINARY_URL')
```

#### .env

```sh
  CLOUDINARY_URL=cloudinary://abcde12345
```

<br />

## 4. CSS

### TailwindCSS implementation

https://gist.github.com/andyjud/2d878abe17588aa7e42c9793e8c2cff2

<br />

## 5. Database PostgreSQL

### Sqlite3 to PostgresQL Migration

#### terminal

```sh
  pip install dj-database-url
  pip install psycopg2-binary
```

#### settings.py

```sh
if ENVIRONMENT == 'development':
    DATABASES = {
        'default': {
            'ENGINE': 'django.db.backends.sqlite3',
            'NAME': BASE_DIR / 'db.sqlite3',
        }
    }
else:
    import dj_database_url
    DATABASES = {
        'default': dj_database_url.parse(env('DATABASE_URL'))
    }
```

#### .env

```sh
  DATABASE_URL=postgres://abcde12345
```

Render.com offers free PostgreSQL for 90 days

1. Create Github Account to Sign In
2. Create New PostgreSQL
3. Get External Database URL

<br />

#### settings.py

```sh
ENVIRONMENT = "production"
```

#### terminal

```sh
python manage.py migrate
python manage.py createsuperuser
```

<br>

## 6. Email

### Gmail offers a free service

#### settings.py

```sh
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_HOST_USER = env('EMAIL_ADDRESS') 
EMAIL_HOST_PASSWORD = env('EMAIL_HOST_PASSWORD') 
EMAIL_PORT = 587
EMAIL_USE_TLS = True
DEFAULT_FROM_EMAIL = f"Django App Name {env('EMAIL_ADDRESS')}"
ACCOUNT_EMAIL_SUBJECT_PREFIX = '' 
```

#### .env

```sh
EMAIL_ADDRESS=your_email@gmail.com
EMAIL_HOST_PASSWORD=abcde12345
```

<br />

#### Sites framework

##### settings.py

```sh
INSTALLED_APPS = [
    'django.contrib.sites',
}

SITE_ID = 1
```

##### Terminal

```sh
python manage.py migrate
```

##### Admin Interface > Sites

```sh
Domain name (add later)
Display Name 
```

<br>

#### !! IF ERROR: Missing table socialaccount_socialapp_sites

Reason: 'allauth.socialaccount' was installed before 'django.contrib.sites'

```sh
# 'django.contrib.sites', Delete or comment out
```

```sh
python manage.py migrate socialaccount zero
```

```sh
'django.contrib.sites',
```

```sh
python manage.py migrate socialaccount
```

<br>

## 7. Github

- Create a .gitignore file in the root before hidden .git folder exists

### .gitignore

```sh
venv/
node/
media/
migrations/
__pycache__/
db.sqlite3
.env
.DS_Store
```

### Update requirements.txt

```sh
pip freeze > requirements.txt
```

<br />

#### Install Github desktop

https://desktop.github.com/

<br />

#### # Github desktop

1. Add Existing Repository
2. Create Repository
3. Publish Repository

<br />

## 8. Web Server

Render.com offers a free tier

1. Create New Webservice
2. Connect to Git Repository
3. Add the environment variables
4. Get the domain

<br>

### Start Command

```sh
gunicorn your_core_folder.wsgi
```

#### terminal

```sh
pip install gunicorn
```

<br />

#### # Environment Variables

```sh
ENVIRONMENT=production
```

<br />

#### # Set Allowed Host & CSRF Trusted Origins

```sh
ALLOWED_HOSTS = ['localhost', '127.0.0.1', 'your_domain.com']

CSRF_TRUSTED_ORIGINS = [ 'https://your_domain.com' ]
```

<br />

#### # Change Site Domain Name

```sh
Domain name : your_site_domain
```

<br />

### Optional: Procfile and Python Version configurations

Place files in the root

#### # Procfile

```sh
web: gunicorn your_core_folder.wsgi
```

#### # runtime.txt

```sh
python-3.11.3
```

<br>

## 9. Deploy

### Update requirements.txt

```sh
pip freeze > requirements.txt
```

### Update staticfiles

```sh
python manage.py collectstatic --noinput
```

### Github

1. Commit to main
2. Publish Repository
