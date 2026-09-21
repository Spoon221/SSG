# Локальная сборка

## Предпросмотр

```bash
mkdocs serve
```

Сервер запускается на `http://127.0.0.1:8000/` с автоперезагрузкой.

## Строгая сборка

```bash
mkdocs build --strict
```

Флаг `--strict` превращает все предупреждения в ошибки — битые ссылки, отсутствующие файлы. Именно этот режим используется в CI.

## Структура собранного сайта

```
site/
├── index.html
├── guide/
│   ├── install/index.html
│   ├── build/index.html
│   └── deploy/index.html
├── cicd/
│   ├── github-actions/index.html
│   └── helios/index.html
└── assets/
```

!!! note "use_directory_urls"
    По умолчанию MkDocs создаёт `page/index.html` вместо `page.html`. Это работает на GitHub Pages, но требует корректного `site_url` для подкаталога на Helios.
