---
layout: post
title:  "Config django"
date:   2021-03-19 20:08:00 +0100
categories: django
comment: true 
---

Quand j'ai commencé à utilisé le framework `Django` j'ai trouvé vraiment pas mal de documentation mais j'ai un peu galéré quand j'ai commencé à vouloir mettre en production ma première application. 

L'ennui c'est que le fichier `settings.py`, ne doit pas être le même selon l'environnement de l'application (c'est à dire en local en test sur le 127.0.0.1 Vs en production sur un url publique). En effet la clé secrète par exemple sur l'environnement de production ne doit pas être codée en dure alors que pour l'environnement de test il n'y a pas de souci.

Du coup, il suffit d'avoir un fichier `.env` à la racine du projet et de surtout ne pas oublier de l'ajouter dans son fichier `.gitignore`

Ci dessous un exemple du fichier de config en local qui sera différent en production (typiquement pour la variable `SECRET_KEY`)

```json
{
    "ENV_TYPE": "local",
    "SECRET_KEY": "RANDOM_KEY",
    "ALLOWED_HOSTS":[
        "127.0.0.1"
    ],
    "DEBUG": true,
    "STATIC_ROOT": "ROOT/FOLDER/OF/DJANGO/FOLDER",
    "DATABASES": {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": "ROOT/FOLDER/OF/DJANGO/FOLDER/db.sqlite3"
        }
    }
}
```

Et dans le fichier `settings.py`

```python
# Get back non secret env data such as DEBUG, ALLOWED HOSTS
with open(os.path.join(BASE_DIR, '.env'), 'r') as f:
    env_data = json.load(f)

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = env_data["SECRET_KEY"]

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = env_data["DEBUG"]

ALLOWED_HOSTS = env_data["ALLOWED_HOSTS"]

if env_data["ENV_TYPE"] == "prod":
    # HTTPS settings
    SESSION_COOKIE_SECURE = True
    CSRF_COOKIE_SECURE = True
    SECURE_SSL_REDIRECT = True

    # HSTS settings
    SECURE_HSTS_SECONDS = 31536000  # 1 year
    SECURE_HSTS_PRELOAD = True
    SECURE_HSTS_INCLUDE_SUBDOMAINS = True
```
