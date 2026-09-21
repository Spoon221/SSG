# Сборка сайта

## Предпросмотр

```bash
mkdocs serve
```

Сервер запускается на `http://127.0.0.1:8000/` с автоперезагрузкой при изменении файлов. Удобен для разработки.

## Строгая сборка

```bash
mkdocs build --strict
```

Флаг `--strict` превращает все предупреждения в ошибки:

- битые внутренние ссылки
- отсутствующие файлы
- неверные якоря

Именно этот режим используется в CI — ошибка сборки блокирует деплой.

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
    ├── stylesheets/
    └── javascripts/
```

!!! note "use_directory_urls"
    По умолчанию MkDocs создаёт `page/index.html` вместо `page.html`.
    Это позволяет использовать чистые URL `/guide/build/` вместо `/guide/build.html`.

## Проверка результата

Помимо визуального просмотра нужно проверить:

```bash
curl -o /dev/null -s -w "%{http_code}" https://spoon221.github.io/SSG/
```

Ожидаемый ответ: `200`.

Наличие контрольной строки в HTML:

```bash
curl -s https://spoon221.github.io/SSG/ | grep "Static Site Generator"
```

Работа поиска проверяется вручную — поисковый индекс находится в `site/search/search_index.json`.

## Проверка MathJax

Формулы рендерятся через локальный конфиг `docs/javascripts/mathjax.js` + CDN MathJax v3.

Пример инлайн: $e^{i\pi} + 1 = 0$

Пример блочный:

$$\int_{-\infty}^{\infty} e^{-x^2}\,dx = \sqrt{\pi}$$

!!! warning "Внешние CDN"
    Если CDN недоступен — MathJax не загрузится. Локальный файл конфигурации гарантирует корректную настройку, но сам MathJax всё равно требует сети. Для полной автономности нужно бандлить MathJax локально.
