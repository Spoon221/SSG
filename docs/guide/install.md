# Установка окружения

## 1. Python и pip

```bash
python3 --version
pip3 --version
```

Результат:

```
Python 3.12.0
pip 25.2 from /Library/Frameworks/Python.framework/Versions/3.12/...
```

## 2. virtualenv

Проверка наличия:

```bash
python3 -m virtualenv --version
```

Если не найден — установка по [официальной инструкции](https://virtualenv.pypa.io/en/latest/installation.html):

```bash
pip3 install virtualenv
```

Альтернатива — `uv` (встроенный менеджер venv):

```bash
uv venv venv
```

## 3. Создание проекта и виртуального окружения

```bash
mkdir ssg && cd ssg
python3 -m virtualenv venv
source venv/bin/activate
```

После активации приглашение терминала меняется на `(venv)`.

## 4. requirements.txt и .gitignore

Установка пакетов и фиксация точных версий:

```bash
pip install mkdocs mkdocs-material mkdocs-minify-plugin
pip freeze > requirements.txt
```

Содержимое `requirements.txt`:

```
mkdocs==1.6.1
mkdocs-material==9.5.47
mkdocs-minify-plugin==0.8.0
```

Содержимое `.gitignore`:

```
venv/
.venv/
site/
_build/
__pycache__/
*.py[cod]
.DS_Store
```

## 5. Инициализация MkDocs

```bash
mkdocs new .
```

Создаётся базовая структура:

```
.
├── docs/
│   └── index.md
└── mkdocs.yml
```
