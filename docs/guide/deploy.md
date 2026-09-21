# Деплой

## Инициализация репозитория и пуш на GitHub

```bash
git init
git remote add origin https://github.com/Spoon221/SSG.git
git add -A
git commit -m "init: MkDocs project"
git push -u origin main
```

## GitHub Pages

### Настройка

В настройках репозитория: **Settings → Pages → Source = GitHub Actions**.

Это включает официальный механизм деплоя через артефакты вместо ветки `gh-pages`.

### Два подхода к публикации

| Подход | Как работает | Когда использовать |
|---|---|---|
| `peaceiris/actions-gh-pages` | Пушит `site/` в ветку `gh-pages` | Простые проекты, совместимость |
| `upload-pages-artifact` + `deploy-pages` | Загружает артефакт через API Pages | Официальный, рекомендованный |

В этом проекте используется **официальный подход** — артефакт загружается и деплоится через GitHub API.

Сайт доступен по адресу: **https://spoon221.github.io/SSG/**

## Helios ИТМО

### Активация аккаунта

Аккаунт создаётся автоматически при поступлении в ИТМО. Пароль получается по адресу [se.ifmo.ru/passwd](https://se.ifmo.ru/passwd/) через авторизацию в ИСУ.

### Подключение

```bash
ssh s506807@helios.cs.ifmo.ru -p 2222
```

### Деплой через rsync

```bash
export SSHPASS="пароль"
sshpass -e rsync -avz --delete \
  -e "ssh -p 2222 -o StrictHostKeyChecking=no" \
  site/ s506807@helios.cs.ifmo.ru:~/public_html/ssg/
```

Сайт публикуется в подкаталог `/ssg/` домашней директории студента.
Доступен по: **https://se.ifmo.ru/~s506807/ssg/**

## Настройка базового URL

Критично для корректной работы в подкаталоге.

В `mkdocs.yml`:

```yaml
site_url: https://se.ifmo.ru/~s506807/ssg/
```

!!! warning "Типичная ошибка"
    Сайт работает на GitHub Pages, но все CSS/JS/ссылки ломаются на Helios в подкаталоге — `site_url` указывает на корень, а не на `/~s506807/ssg/`.

    В CI решается заменой `site_url` перед сборкой под каждую площадку:
    ```bash
    sed -i 's|site_url:.*|site_url: https://se.ifmo.ru/~s506807/ssg/|' mkdocs.yml
    mkdocs build --strict
    ```

## Отладка

### Ошибки и решения

| Ошибка | Причина | Решение |
|---|---|---|
| `SSHPASS: -e option given but SSHPASS not set` | Переменная `SSHPASS` не экспортирована | `export SSHPASS="..."` перед командой |
| `Connection closed` при SSH | Helios блокирует внешние IP | Использовать VPN ИТМО или деплоить вручную |
| MathJax не рендерится | `polyfill.io` заблокирован | Убрать polyfill, добавить локальный `mathjax.js` |
| Битые ссылки при `--strict` | Неверные пути в `docs/` | Исправить пути перед пушем |
| CSS не грузится на Helios | Неверный `site_url` | Указать полный путь с подкаталогом |
