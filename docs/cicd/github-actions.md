# GitHub Actions CI/CD

## Подходы к публикации

Существует два способа публикации на GitHub Pages:

### 1. peaceiris/actions-gh-pages

Пушит собранный сайт в ветку `gh-pages`. Прост, работает везде.

```yaml
- uses: peaceiris/actions-gh-pages@v4
  with:
    github_token: ${{ secrets.GITHUB_TOKEN }}
    publish_dir: ./site
```

### 2. actions/upload-pages-artifact + actions/deploy-pages

Официальный подход. Загружает артефакт и деплоит через API Pages.

```yaml
- uses: actions/upload-pages-artifact@v3
  with:
    path: ./site
- uses: actions/deploy-pages@v4
```

В данной работе используется **второй подход** как официально рекомендованный.

## Схема пайплайна

```
push to main
      │
      ▼
   [lint]
   mkdocs build --strict
      │
      ▼
   [build]
   mkdocs build + upload artifact
      │
      ▼
   [deploy-pages]    [deploy-helios]
   GitHub Pages      SSH → Helios
```

## Триггеры

| Событие | Поведение |
|---|---|
| `push` в `main` | lint → build → deploy (Pages + Helios) |
| `push` в другую ветку | только lint + build |
| `pull_request` | lint + build |
| `workflow_dispatch` | ручной запуск полного пайплайна |
