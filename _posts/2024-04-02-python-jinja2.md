---
layout: single
title: "Python - Jinja2(템플릿 엔진)"
categories: Python
tag: [Python, Jinja2]
toc: true
toc_sticky: true
toc_label: "목차"
---

## 템플릿 엔진(Template Engine) 이란?

템플릿 엔진은 정적인 HTML에 동적인 데이터를 결합하여 웹 페이지를 생성합니다. 템플릿 파일에 데이터를 삽입하여 동적인 HTML을 생성합니다.

## Jinja2 란

Python에서 가장 많이 사용되는 템플릿 엔진입니다. 마이크로 웹 프레임워크인 Flask에서 채택되어 사용되고 있습니다.

### Jinja2 설치

```bash
pip install Jinja2
```

### 사용 예시

기본 예시:

```python
from jinja2 import Template

# 템플릿
template = Template('Hello, {{ name }}!')

# 데이터
data = {'name': 'John'}

# 렌더링: 템플릿에 데이터를 삽입하여 최종 HTML 생성
result = template.render(data)
print(result)  # 출력: Hello, John!
```

HTML:

```python
from jinja2 import Template

template_str = """
<h1>{{ title }}</h1>
<p>{{ description }}</p>
"""
template = Template(template_str)

data = {
    "title": "Welcome to Jinja2",
    "description": "This is a simple Jinja2 example."
}

# 렌더링: 템플릿에 데이터를 삽입하여 최종 HTML 생성
result = template.render(data)
print(result)
```

HTML 파일(HTML 파일을 사용할 경우):

`hello.html` - 'templates' 디렉토리에 저장:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Jinja2 Demo</title>
</head>
<body>
    <h1>Hello, {{ name }}!</h1>
</body>
</html>
```

`hello.py`:

```python
from jinja2 import Environment, FileSystemLoader

# 템플릿 파일이 있는 디렉토리 경로을 인자로 전달하여 Environment 객체 생성
env = Environment(loader=FileSystemLoader('templates'))

# Environment 객체로 부터 템플릿 로딩
template = env.get_template('hello.html')

# 데이터
data = {'name': 'Jinja2'}

# 렌더링: 템플릿에 데이터를 삽입하여 최종 HTML 생성
rendered_html = template.render(data)

print(rendered_html)
```

## 변수

변수를 사용 하기 위해 `{{ }}` 를 사용합니다.

```python
from jinja2 import Template

template_str = """
<h1>{{ title | upper}}</h1>
<p>{{ description | capitalize}}</p>
"""
template = Template(template_str)

data = {
    "title": "Welcome to Jinja2",
    "description": "This is a simple Jinja2 example."
}

result = template.render(data)
print(result)
```

### 딕셔너리 타입의 변수

```python
data = {
    "user": {
        "name": "John",
        "age": 30
    }
}

template_str = "{{ user.name }} is {{ user.age }} years old."
template = Template(template_str)
result = template.render(data)
print(result)  # 출력: John is 30 years old.
```

### 변수 연산

```python
data = {
    "variable": 10
}

template_str = "{{ variable + 1 }}"
template = Template(template_str)
result = template.render(data)
print(result)  # 출력: 11
```

```python
data = {
    "variable": "10"
}

template_str = "{{ variable + '1' }}"
template = Template(template_str)
result = template.render(data)
print(result)  # 출력: 101
```

### 변수 필터링

```python
from jinja2 import Template

template_str = """
<h1>{{ title | upper}}</h1>
<p>{{ description | capitalize}}</p>
"""
template = Template(template_str)

data = {
    "title": " Welcome to Jinja2 ",
    "description": "This is a simple Jinja2 example."
}

result = template.render(data)
print(result)
```

- `upper`: 대문자로 변환
- `capitalize`: 첫 글자만 대문자로 변환

이 외에 다양한 필터가 있습니다.

- `lower`: 소문자로 변환
- `title`: 각 단어의 첫 글자를 대문자로 변환
- `trim`: 문자열 앞뒤 공백 제거
- `replace`: 문자열 치환
- ...

## 주석

`{# ... #}` 를 사용하여 주석을 작성할 수 있습니다. 랜더링 결과에 포함되지 않습니다.

```python
from jinja2 import Template

template_str = """
<h1>{{ title }}</h1>
{# This is a comment #}
<p>{{ description }}</p>
"""
template = Template(template_str)

data = {
    "title": "Welcome to Jinja2",
    "description": "This is a simple Jinja2 example."
}

result = template.render(data)
print(result)
```

## 제어문

### 조건문

`{% if ... %} ... {% endif %}` 를 사용하여 조건문을 작성할 수 있습니다.

```python
from jinja2 import Template

template_str = """
{% if user.age >= 18 %}
    <p>{{ user.name }} is an adult.</p>
{% else %}
    <p>{{ user.name }} is a minor.</p>
{% endif %}
"""

template = Template(template_str)

data = {
    "user": {
        "name": "John",
        "age": 30
    }
}

result = template.render(data)
print(result)
```

### 반복문

```python
from jinja2 import Template

template_str = """
<ul>
{% for item in items %}
    <li>{{ item }}</li>
{% endfor %}
</ul>
"""

template = Template(template_str)

data = {
    "items": ["Apple", "Banana", "Cherry"]
}

result = template.render(data)
print(result)
```

### 매크로(Macro)

매크로는 템플릿에서 사용할 수 있는 함수를 정의할 수 있습니다.

**이 후의 모든 소스 코드에서 '{' 는 작은 따옴표를 제외하고 {로 변경하여 사용하시기 바랍니다. 블로그 문서가 오류가 발생 하여 할 수 없이 변경하였습니다.**

```python
from jinja2 import Template

template_str = """
'{'% macro input(name, value='', type='text') %}
    <input type="{{ type }}" name="{{ name }}" value="{{ value }}">
'{'% endmacro %}

{{
    input('username')
}}
{{
    input('password', type='password')
}}
"""

template = Template(template_str)
result = template.render()
print(result)
```

- `macro` 키워드를 사용하여 매크로를 정의합니다.
- `input` 매크로는 `name`, `value`, `type` 세 개의 인자를 받습니다.
- `input` 매크로를 호출할 때 인자를 전달합니다.

#### 다른 파일의 매크로 가져오기

`macro.html` - 'templates' 디렉토리에 저장:

```html
'{'% macro input(name, value='', type='text') %}
    <input type="{{ type }}" name="{{ name }}" value="{{ value }}">
'{'% endmacro %'}'
```

`macro_test.html` - 'templates' 디렉토리에 저장:

```html
'{'% from 'macro.html' import input %'}'

{{
    input('username')
}}
{{
    input('password', type='password')
}}
```

`macro_test.py`:

```python
from jinja2 import Environment, FileSystemLoader

env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('macro_test.html')

result = template.render()
print(result)
```

## 템플릿 상속

템플릿 상속은 기본 템플릿(골격: 머리글, 바닥글, 메뉴, ...)을 만들고 하위 템플릿에서 기본 템플릿을 상속받아 해당 페이지의 특정한 콘텐츠만 변경할 수 있습니다.

`base.html` - 'templates' 디렉토리에 저장:

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>'{'% block title %}My Website'{'% endblock %}</title>
</head>
<body>
    <header>
        <h1>My Website</h1>
        <nav>
            <!-- Navigation links -->
        </nav>
    </header>

    <main>
        '{'% block content %}
        <!-- Default content goes here if not overridden -->
        '{'% endblock %}
    </main>

    <footer>
        <!-- Footer content -->
    </footer>
</body>
</html>
```

`home.html` - 'templates' 디렉토리에 저장:

```html
'{'% extends 'base.html' %}

'{'% block title %}Home'{'% endblock %}

'{'% block content %}
    <h2>Welcome to My Website</h2>
    <p>This is the home page.</p>
'{'% endblock %}
```

`about.html` - 'templates' 디렉토리에 저장:

```html
'{'% extends 'base.html' %}

'{'% block title %}About'{'% endblock %}

'{'% block content %}
    <h2>About Us</h2>
    <p>This is the about page.</p>
'{'% endblock %}
```

`home.py`:

```python
from jinja2 import Environment, FileSystemLoader

env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('home.html')

result = template.render()
print(result)

template = env.get_template('about.html')
result = template.render()
print(result)
```

## 템플릿 include

`include` 키워드를 사용하여 다른 템플릿을 포함할 수 있습니다.

`header.html` - 'templates' 디렉토리에 저장:

```html
<header>
    <h1>My Website</h1>
    <nav>
        <!-- Navigation links -->
    </nav>
</header>
```

`footer.html` - 'templates' 디렉토리에 저장:

```html
<footer>
    <!-- Footer content -->
</footer>
```

`base.html` - 'templates' 디렉토리에 저장:

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>'{'% block title %}My Website'{'% endblock %}</title>
</head>
<body>
    '{'% include 'header.html' %}

    <main>
        '{'% block content %}
        <!-- Default content goes here if not overridden -->
        '{'% endblock %}
    </main>

    '{'% include 'footer.html' %}
</body>
</html>
```

`home.py`:

```python
from jinja2 import Environment, FileSystemLoader

env = Environment(loader=FileSystemLoader('templates'))
template = env.get_template('base.html')

result = template.render()
print(result)
```

## Flask 에서 Jinja2 사용

Flask 설치

```bash
pip install Flask
```

`templates/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Flask with Jinja2</title>
</head>
<body>
    <h1>{{ message }}</h1>
</body>
</html>
```

`app.py`:

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route("/")
def index():
    message = "Hello, Flask!"
    return render_template("index.html", message=message)

if __name__ == "__main__":
    app.run()    
```

`app.py` 실행:

```bash
python app.py
```

브라우저에서 `http://localhost:5000` 접속:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Flask with Jinja2</title>
</head>
<body>
    <h1>Hello, Flask!</h1>
</body>
</html>
```

## FastAPI 에서 Jinja2 사용

FastAPI 설치

```bash
pip install fastapi uvicorn
```

`templates/index.html`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>FastAPI with Jinja2</title>
</head>
<body>
    <h1>{{ message }}</h1>
</body>
</html>
```

`main.py`:

```python
from fastapi import FastAPI, Request
from fastapi.responses import HTMLResponse
from fastapi.templating import Jinja2Templates

app = FastAPI()

templates = Jinja2Templates(directory="templates")

@app.get("/", response_class=HTMLResponse)
async def read_item(request: Request):
    message = "Hello, FastAPI!"
    return templates.TemplateResponse("index.html", {"request": request, "message": message})
```

`main.py` 실행:

```bash
uvicorn main:app --reload
```

브라우저에서 `http://localhost:8000` 접속:

```html
<!DOCTYPE html>
<html>
<head>
    <title>FastAPI with Jinja2</title>
</head>
<body>
    <h1>Hello, FastAPI!</h1>
</body>
</html>
```

---

해시태그: #Python #Jinja2 #템플릿엔진 #템플릿 #변수 #주석 #제어문 #매크로 #템플릿상속 #템플릿include #Flask #FastAPI
