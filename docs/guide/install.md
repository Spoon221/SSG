# Установка окружения

## Python и pip

```bash
python3 --version
pip3 --version
```

Результат на момент выполнения работы:

```
Python 3.12.0
pip 25.2 from /Library/Frameworks/Python.framework/Versions/3.12/...
```

## Установка virtualenv

```bash
pip3 install virtualenv
virtualenv --version
```

## Создание виртуального окружения

```bash
python3 -m virtualenv venv
source venv/bin/activate
pip install -r requirements.txt
```

## Установка MkDocs

```bash
pip install mkdocs mkdocs-material mkdocs-minify-plugin
pip freeze > requirements.txt
```

Зафиксированные зависимости (`requirements.txt`):

```
mkdocs==1.6.1
mkdocs-material==9.5.47
mkdocs-minify-plugin==0.8.0
```
