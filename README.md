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
