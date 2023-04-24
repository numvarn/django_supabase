# การจัดการ Static Files บน Vercel

โดย ผู้ช่วยศาสตราจารย์พิศาล สุขขี

Youtube : https://youtu.be/cqM8sCmVtVA

เนื้อหา : กระบวนการเชื่อมต่อ Django Application กับฐานข้อมูลออนไลน์ Supabase และทำการติดตั้งบน Vercel

โดยมีขั้นตอนในการเตรียมการ

0:00 แนะนำบทเรียน
2:22 แนะนำฐานข้อมูลออนลไน์ Supabase
7:55 สร้าง repository ใหม่
13:20 การตั้งค่าการเชื่อมต่อฐานข้อมูล
15:22 ติดตั้ง psycopg2
22:20 การติดตั้งบน Vercel
24:00 การติดตั้ง psycopg2-library

## การเตรียมค่า Config ก่อนการ Deploy

ไฟล์ /<โปรเจค>/wsgi.py

```python
.
.
application = get_wsgi_application()

app = application
```

ไฟล์ /<โปรเจค>/settings.py

```python
ALLOWED_HOSTS = ['127.0.0.1', '.vercel.app']
```

ไฟล์ /vercel.json

```json
{
  "version": 2,
  "builds": [
    {
      "src": "deploy_demo/wsgi.py",
      "use": "@vercel/python"
    },
    {
      "src": "build_files.sh",
      "use": "@vercel/static-build",
      "config": {
        "distDir": "staticfiles_build"
      }
    }
  ],
  "routes": [
    {
      "src": "/static/(.*)",
      "dest": "/static/$1"
    },
    {
      "src": "/(.*)",
      "dest": "deploy_demo/wsgi.py"
    }
  ]
}
```

ทำการสร้างไฟล์ build_fiiles.sh

```shell
pip install -r requirements.txt
python3.9 manage.py collectstatic --noinput --clear
```

ทำการตั้งต่า Static
settings.py

```python
import os
.
.
STATIC_URL = '/static/'

STATICFILES_DIRS = [BASE_DIR / 'statics', ]
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles_build', 'static')
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

การตั้งค่าการเชื่อมต่อฐานข้อมูล

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '<database_name>',
        'USER': '<database_user>',
        'PASSWORD': '<database_password>',
        'HOST': '<database_host>',
        'POST': '5432',
    }
}
```
