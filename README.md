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
