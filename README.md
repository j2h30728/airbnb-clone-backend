# 1 Set Up

## 1.0 Python3

## 1.1 Poetry

### poetry 설치

```bash
$ brew install poetry
```

### django 설치

```bash
$ poetry add django
```

### poetry 내부에서 django 명령어 실행

- django를 설치한 환경으로 진입하여 명령어 실행

```bash
$ poetry shell
% django-admin
$ exit

```

> python = "^3.9"
> Django = "~4.0"

## 1.2 Start Project

```bash
$ poetry shell
$ django-admin startproject config .


```

- 이미 폴더를 생성했으므로 `.`을 사용
- 보통 폴더를 생성하기전에 명령어를 실행한다.
  - `$ django-admin startproject config <folder-name>`
  - config, manage.py 파일이 생성된다.

### git ignore 확장프로그램

```bash
$ add gitignore
$ python

```

# 3 Django Basics

## 3.0 Run Server

- manage.py 가 터미널에서 django 명령을 실행한다.

```bash
$ python manage.py runserver
```

- db.sqlite3 이 생성된다.

## 3.1 Migrations

- db 모양을 내가 원하는 대로 변경
- django_session 이라는 테이블을 생성하는 migration

> You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
> Run 'python manage.py migrate' to apply them.

```bash
$ python manage.py migrate
```

- 위의 명령어를 실행해서 18개의 파일을 생성

## 3.2 Recap

## 3.3 Super User

### 어드민 계정 만들기

- django에서 유효성 검증도 진행한다.

```bash
$ python manage.py createsuperuser
```

## 3.4 Framework vs Library

```py
# config/settings.py

LANGUAGE_CODE = "ko-kr"

TIME_ZONE = "Asia/Seoul"
```

## 3.5 Apps

# 4 Django Apps

## 4.0 Models

모델은 어플리케이션에서 데이터의 모양을 묘사한 것

### 1, houses 모델 만들기

```bash
$ python manage.py startapp houses
$ python manage.py startapp <application-name>
```

- 어플리케이션 이름
  - houses
  - users
  - reviews
  - payments
  - messages

### 2. 장고에게 houses 모델을 알리기

```py
# config/settings.py

INSTALLED_APPS = [
  # 중략
    "houses.apps.HousesConfig" # 문구 추가
]

```

## 4.1 Migrations

### 1. admin 패널 생성

```py
# houses/admin.py
from django.contrib import admin
from .models import House


@admin.register(House) # admin 패널에 House라는 model을 등록하고 싶다. ; House model을 통제하겠다.
class HouseAdmin(admin.ModelAdmin):
  # HouseAdmin 클래스는 ModelAdmin 클래스로 부터 상속 받는다.
  # ModelAdmin 은 model을 위한 admin 패널
    pass

```

### 2. 마이그레이션

model.py 에 작성한 모델 코드로 장고는 houses모델을 알고있지만 데이터베이스는 알지 못하는 상태이다.

데이터 베이스와 SQL문으로 대화한다.
파이썬 코드로 작성하면 장고가 SQL 코드로 번역해준다.

```bash
$ python manage.py makemigrations
```

## 4.2 Recap

## 4.3 Admin

```py
# config/settings.py
CUSTOM_APPS = [
    "houses.apps.HousesConfig",
]
SYSTEM_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
]

INSTALLED_APPS = SYSTEM_APPS + CUSTOM_APPS

```

### 어드민 패널

- 선택한 리스트를 볼 수 있다.
- 필터 추가할 수 있다.
- 칼럼을 그룹핑하여 나타낼 수 있다.
- 검색 창을 만들 수 있다.(검색 추가 가능)
- 요소 선택시, 설정한 링크로 이동 가능하게 할 수 있다.
- 선택한 칼럼을 숨길 수 있다.

```py
@admin.register(House)
class HouseAdmin(admin.ModelAdmin):
    fields = (
        "name",
        "address",
        ("price_per_night", "pets_allowed"), # 칼럼을 그룹핑 함
    )
    list_display = ("name", "price_per_night", "address", "pets_allowed") # 선택한 리스트를 보여줌
    list_filter = ("price_per_night", "pets_allowed") # 필터 기능
    search_fields = ("address",) # 검색 기능
    list_display_links = ("name", "address") # 링크 기능
    list_editable = ("pets_allowed",)  # 숨김 기능
    # 튜플의 요소가 1개 라면, 자동으로 string으로 변환됨.
    #리스트 또는 튜플 타입이어야하기 때문에 ,(콤마)를 추가 해주어 튜플로 유지시킨다.
```

### 모델

```py
class House(models.Model):

    """Model Definition for Houses"""

    name = models.CharField(max_length=140)
    price_per_night = models.PositiveIntegerField(
        verbose_name="Price", help_text="Positive Numbers Only"
    )
    description = models.TextField()
    address = models.CharField(max_length=140)
    pets_allowed = models.BooleanField(
        verbose_name="Pets Allowed?",
        default=True, # 기본 값 설정가능
        help_text="Does this house allow pets?", # 필드 값에 대한 추가 설명 가능
    )

    def __str__(self):
        return self.name

```

> 수정 이후에는 항상 마이그레이션 작업 필요
>
> ```bash
> $ python manage.py makemigrations
> $ python manage.py migrate
> ```

```
## 4.4 Documentation
```

# 5 Users App

## 5.0 Introduction

### [Using a custom user model when starting a project](https://docs.djangoproject.com/en/5.1/topics/auth/customizing/#using-a-custom-user-model-when-starting-a-project)

- use model 생성시, 내가 원하는 필드의 값을 넣어주고 싶다.
  - 소셜 로그인 정보 등
  - username대신 email으로 식별 토큰으로 사용하기 등

```py
AUTH_USER_MODEL = "myapp.MyUser"


# user model
from django.contrib.auth.models import AbstractUser


class User(AbstractUser):
    pass

# admin.py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

admin.site.register(User, UserAdmin)
```

- 프로젝트 시작시에 custom user model 생성하기
  - 프로젝트 중간에 테이블을 변경하게 되면 외래 키와 다양한 관계 등에 대해서 영향을 끼칠 수 있기 때문에 어려움이 존재한다.

## 5.1 Custom Model

```bash
$ poetry shell
$ python manage.py startapp users
```

### Django의 user를 상속받는 custom user 만들기

- 아래와 같은 형식은 ㅁ모델을 처음부터 끝까지 새로이 만드는 것
- 장고는 제공하는 것이 많기 때문에 직접 만드는것은 손해

  ```py
  from django.db import models
  class User(models.Model):

  ```

#### 1. `AbstractUser`상속받기

- `AbstractUser`을 상속받아서, django가 지원하는 메서드와 함께 custom user model을 만든다.

```py

from django.contrib.auth.models import AbstractUser

class User(AbstractUser):
    pass
```

#### 2. custom user model을 사용하는 것을 알림

- setting.py

```py
# Auth
AUTH_USER_MODEL = "users.User"
```

#### 3. 생성한 user application 설치

- setting.py

```py
# Application definition

CUSTOM_APPS = ["houses.apps.HousesConfig", "users.apps.UsersConfig"]
```

#### 4. django의 use model을 내가 만든 것으로 교체

1. 사용자가 있을때 교체하는 것은 어렵고 힘들기 떄문에 처음 부터 설정해주는 것
   => db.splite3 , migrations 파일 삭제

2. 서버 재설정

```bash
$ python manage.py makemigrations
```

3. 데이터 베이스 새 정보로 업데이트

```bash
$ python manage.py migrate
```

#### custom user admin 생성

```py
from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

@admin.register(User) # User 에서 관리
class CustomUserAdmin(UserAdmin):
    pass

```

```bash
$ python manage.py runserver
```

#### 데이터 베이스를 비웠으니 super user 재생성

```bash

$ python manage.py createsuperuser
```

## 5.2 Custom Fields

- 상속 받고있는 AbstractUser의 클래스 내에서는 first_name 과 last_name 메서드가 존재하기 떄문에 사용하지 않게 덮어씌운다.

  ```py
      first_name = models.CharField(_("first name"), max_length=150, blank=True)
      last_name = models.CharField(_("last name"), max_length=150, blank=True)
  ```

- `editable=False`를 사용하게 되면 관리자 패널에서 보여지지 않는다.

  ```py
  class User(AbstractUser):
      first_name = models.CharField(max_length=150, editable=False)
      last_name = models.CharField(max_length=150, editable=False)
      name = models.CharField(max_length=150)
      is_host = models.BooleanField()
  ```

> model을 수정할때마다 migrate를 해주는 이유는?
> -> python코드가 데이터베이스의 구조와 같지 않기떄문에 동일하게 맞춰주기 위함임

## 5.3 Defaults

- 기존에 있는 데이터에는 어떻게 추가해야할지에 대한 대책

```py
    name = models.CharField(max_length=150, default="")
    is_host = models.BooleanField(default=False)
```

- `default=<기본값으로 지정하고 싶은 값>`
- `NULL=True` : NULL 값을 허용한다.

## 5.4 Custom Admin

```py

from django.contrib import admin
from django.contrib.auth.admin import UserAdmin
from .models import User

@admin.register(User)
class CustomUserAdmin(UserAdmin):
    fieldsets = ( # fieldset를 사용해 섹션으로 만들수 있다
        (
            "Profile",
            {
                "fields": ("username", "password", "name", "email", "is_host"),
                "classes": ("wide",),
            },
        ),
        (
            "Permissions",
            {
                "fields": (
                    "is_active",
                    "is_staff",
                    "is_superuser",
                    "groups",
                    "user_permissions",
                ),
                "classes": ("collapse",), # 해당 섹션을 접고 펴는 동작으로 스타일링
            },
        ),
        (
            "Important Dates",
            {
                "fields": ("last_login", "date_joined"),
                "classes": ("collapse",),  # 해당 섹션을 접고 펴는 동작으로 스타일링
            },
        ),
    )
    list_display = ("username", "email", "name", "is_host")
```

## 5.5 Foreign Keys

- user 의 Primary Key (id)를 참조한다.

  ```py
  # house model

      owner = models.ForeignKey(
          "users.User",
          on_delete=models.CASCADE,
      )
  ```

## 5.6 Super Mega Recap
