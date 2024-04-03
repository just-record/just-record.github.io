---
layout: single
title: "Flask - 기본 사용법"
categories: Flask
tag: [Flask, Python]
toc: true
toc_sticky: true
toc_label: "목차"
---

## Flask란?

Flask는 Python으로 작성된 마이크로 웹 프레임워크로 최소한의 기능만을 제공 하지만 다양한 확장을 지원합니다. 또한 규칙이 엄격하지 않아 개발자가 원하는 구조로 설계 할 수 있습니다. 따라서 간단할 수도 있고 복잡할 수도 있습니다.

## Flask 설치

```bash
pip install Flask
```

## Flask 실행

`app.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run()
```

실행(터미털):

```bash
python app.py
```

접속 주소는 `http://localhost:5000/` 입니다.

### Flask 실행 옵션

`app.run()` 인자 값으로 다양한 옵션을 설정 할 수 있습니다.

```python
if __name__ == '__main__':
    app.run(host='0.0.0.0', port='5050', debug=True)
```

- `host`: Flask 서버에 접속할 수 있는 IP 주소를 설정합니다. '0.0.0.0'은 모든 IP 주소를 의미합니다.
- `port`: 서비스 포트를 설정합니다. 기본값은 5000 입니다.
- `debug`: 디버그 모드를 설정합니다. 기본값은 False 입니다.

접속 주소는 `http://localhost:5050/` 입니다.

## 라우팅 (Routing)

```python
from flask import Flask

app = Flask(__name__)   # Flask 객체 생성(__name__ 이름으로)

@app.route('/')
def home():
    return '<h1>Hello Flask!</h1>'
# http://localhost:5000/

@app.route('/member')
def member():
    return '<h1>Member</h1>'
# http://localhost:5000/member    

@app.route('/board')
def board():
    return '<h1>Board</h1>'
# http://localhost:5000/board

if __name__ == '__main__':
    app.run()
```

- `@app.route('/')`: 라우팅 경로를 설정합니다.
- `def home():`: 라우팅 경로에 접속시 실행할 함수를 정의합니다.
  - 함수의 이름이 중복되면 안됩니다.
- `return '<h1>Hello Flask!</h1>'`: 라우팅 경로에 접속시 반환할 값을 설정합니다.

### 경로 변수

```python
from flask import Flask

app = Flask(__name__)

@app.route('/job/<jobname>')
def job(jobname):
    return '<h1>Job: ' + jobname + '</h1>'    
# http://localhost:5000/job/develper

if __name__ == '__main__':
    app.run()
```

- `@app.route('/job/<jobname>')`, `def job(jobname)`: URL 경로를 'jobname' 변수로 설정합니다.

접속 주소는 `http://localhost:5000/job/developer` 입니다.

결과는 `Job: developer` 입니다.

## 템플릿 엔진 (Jinja2)

**이 후의 모든 소스 코드에서 '{' 는 작은 따옴표를 제외하고 {로 변경하여 사용하시기 바랍니다. 블로그 문서가 오류가 발생 하여 할 수 없이 변경하였습니다.**

이전 포스트에 템플릿 엔진에 대한 자세한 설명이 있습니다. 여기서는 Flask에서 템플릿 엔진의 적용법을 간단하게 보겠습니다.

'templates' 디렉토리를 생성하고 HTML(템플릿) 파일을 만듭니다.

`templates/home.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
</head>
<body>
    <h1>Home</h1>
</body>
</html>
```

`member.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Member</title>
</head>
<body>
    <h1>Member</h1>
</body>
</html>
```

`job.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Job</title>
</head>
<body>
    <h1>Job: {{ jobname }}</h1>
</body>
</html>
```

`controll_for.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Controll For</title>
</head>
<body>
    <h1>Controll For</h1>
    <ul>
        {% for board in boards %}
        <li>{{ board }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

`controll_if.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Controll If</title>
</head>
<body>
    <h1>Controll If</h1>
    {% if score >= 90  %}
    <li>A학점</li>
    {% elif score >= 80  %}
    <li>B학점</li>
    {% elif score >= 70  %}
    <li>C학점</li>
    {% elif score >= 60  %}
    <li>D학점</li>
    {% else  %}
    <li>F학점</li>
    {% endif %}
</body>
</html>
```

`app.py`

```python
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def home():
    return render_template('home.html')
# http://localhost:5000/

@app.route('/member')
def member():
    return render_template('member.html')

### 데이터
@app.route('/job/<jobname>')
def job(jobname):
    return render_template('job.html', jobname=jobname)
# http://localhost:5000/job/developer


@app.route('/controll_for')
def controll_for():
    boards = ['title_1', 'title_2', 'title_3']
    return render_template('controll_for.html', boards=boards)
# http://localhost:5000/controll_for


@app.route('/controll_if')
def controll_if():
    score = 72
    return render_template('controll_if.html', score=score)
# http://localhost:5000/controll_if

if __name__ == '__main__':
    app.run()
```

- `render_template('home.html')`: 'home.html' 파일을 렌더링 합니다.
- `render_template('job.html', jobname=jobname)`: 'job.html' 파일을 렌더링 하면서 'jobname' 변수를 전달합니다.
- `for` 문과 `if` 문을 사용하여 반복문과 조건문을 사용할 수 있습니다.

## 정적 파일 (Static Files)

'static' 디렉토리를 생성하고 정적 파일을 저장합니다.

`static/style.css`

```css
body {
    background-color: silver;
}

h1 {
    font-family: "Times New Roman", Georgia, Serif;
    text-align: center;
}

p {
    color: #663399;
    text-decoration:underline;
    font-weight: bold;
}
```

`static/script.js`

```javascript
function alertDialogBox() {
    alert("확인을 누를 때까지 다른 작업을 할 수 없어요!");
}
```

'templates' 디렉토리에 HTML 파일을 만듭니다.

`templates/css_js.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask Render</title>
    <link href="/static/styles.css" rel="stylesheet" />
    <script src="/static/scripts.js"></script>
</head>
<body>
    <h1>Hello Flask</h1>
    <p>아래의 버튼을 Click 하세요!</p>
    <input type="button" value="Click" onclick="alertDialogBox();">
</body>
</html>
```

`app.py`

```python
from flask import Flask, render_template

app = Flask(__name__, static_url_path='/static')


@app.route('/css_js')
def css_js():
    return render_template('css_js.html')
# http://localhost:5000/css_js    

if __name__ == '__main__':
    app.run(host='0.0.0.0', port='5050', debug=True)
```

- `app = Flask(__name__, static_url_path='/static')`: 정적 파일 경로를 설정합니다.

접속 주소는 `http://localhost:5000/css_js` 입니다.

## 요청 객체 (Request Object)

`request` 객체를 사용하여 클라이언트의 요청 정보를 확인 할 수 있습니다.

`templates/.html`: 생략 - 기존과 동일

`templates/request_form.html`

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <title>Flask Request</title>
</head>
<body>
    <h1>A Link - get</h1>
    <p><a href="/request_get?name=hong&age=17">request get</a></p>
    
    <p></p>

    <h1>Form - post</h1>
    <form name="form_post" method="post" action="/request_post">
        <p>Name: <input type="text" name="name" /></p>
        <p>Age: <input type="text" name="age" /></p>
        <p><input type="submit" ></p>
    </form>
</body>
</html>
```

`templates/request_result.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flask Render</title>
</head>
<body>
    <h1>A Link get</h1>
    <p>Name: {{ name }}</p>
    <p>Age: {{ age }}</p>
</body>
</html>
```

`app.py`

```python
from flask import Flask, render_template, request

app = Flask(__name__)


@app.route('/request_get_form')
def request_get_form():
    return render_template('request_form.html')

@app.route('/request_get', methods = ['GET'])
def request_get():
    name = request.args.get('name')
    age = request.args.get('age')
    return render_template('request_result.html', name=name, age=age)    

@app.route('/request_post', methods = ['POST'])
def request_post():
    name = request.form['name']
    age = request.form['age']
    return render_template('request_result.html', name=name, age=age)        

if __name__ == '__main__':
    app.run()
```

- `request.args.get('name')`: GET 방식으로 전달된 데이터를 가져옵니다.
- `request.form['name']`: POST 방식으로 전달된 데이터를 가져옵니다.

접속 주소는 `http://localhost:5000/request_get_form` 입니다.

## Blueprint

Blueprint는 Flask의 모듈화 방법으로 여러 개의 라우트와 뷰를 하나의 모듈로 묶어서 사용할 수 있습니다.

디렉토리 구조

```plaintext
root/
    app.py
    templates/
        home.html
        code/
            code.html
        todo/
            todo.html
    apps/
        __init__.py
        code/
            __init__.py
            code.py
        todo/
            __init__.py
            todo.py
```

`templates/home.html`: 생략

`templates/code/code.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Code</title>
</head>
<body>
    <h1>Code</h1>
    <p>command: {{ command }}</p>
</body>
</html>
```

`templates/todo/todo.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Todo</title>
</head>
<body>
    <h1>Todo</h1>
    <p>command: {{ command }}</p>
</body>
</html>
```

`apps/code/code.py`

```python
from flask import Blueprint, render_template, request
from . import code_app

@code_app.route('/', methods=['GET'])
def code():
    command = request.args.get('command')
    return render_template('code/code.html', command=command)
```

- `from . import code_app`: Blueprint 객체를 가져옵니다.
- `@code_app.route('/')`: Blueprint 객체의 라우팅 경로를 설정합니다.
- 접속 주소는 `http://localhost:5000/code?command=python` 입니다.

`apps/todo/todo.py`

```python
from flask import Blueprint, render_template, request
from . import todo_app

@todo_app.route('/', methods=['GET'])
def todo():
    command = request.args.get('command')
    return render_template('todo/todo.html', command=command)
```

- `from . import todo_app`: Blueprint 객체를 가져옵니다.
- `@todo_app.route('/')`: Blueprint 객체의 라우팅 경로를 설정합니다.
- 접속 주소는 `http://localhost:5000/todo?command=python` 입니다.

`app.py`

```python
from flask import Flask, render_template, request
from apps.code import code_app
from apps.todo import todo_app

app = Flask(__name__, static_url_path='/static')

app.register_blueprint(code_app, url_prefix='/code')
app.register_blueprint(todo_app, url_prefix='/todo')


@app.route('/')
def home():
    return render_template('home.html')

if __name__ == '__main__':
    app.run()
```

- `app.register_blueprint(code_app, url_prefix='/code')`: '/code' 경로로 code_app Blueprint 객체를 등록합니다.
- `app.register_blueprint(todo_app, url_prefix='/todo')`: '/todo' 경로로 todo_app Blueprint 객체를 등록합니다.

---

해시태그: #Python #Flask #라우팅 #템플릿엔진 #정적파일 #요청객체 #Blueprint
