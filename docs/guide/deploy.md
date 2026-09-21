# Деплой

## GitHub Pages

Публикация происходит автоматически при пуше в `main`. Сайт доступен по адресу:

```
https://spoon221.github.io/SSG/
```

Настройка в репозитории: **Settings → Pages → Source = GitHub Actions**.

## Helios ИТМО

Деплой выполняется по SSH в подкаталог `/ssg` домашней директории.

```bash
rsync -avz --delete site/ s506807@helios.cs.ifmo.ru:/home/s506807/public_html/ssg/ \
  -e "ssh -p 2222"
```

Сайт доступен по адресу:

```
https://se.ifmo.ru/~s506807/ssg/
```

!!! warning "base_url для подкаталога"
    Для корректной работы в подкаталоге `site_url` в `mkdocs.yml` должен указывать на полный путь. В противном случае CSS, JS и поиск не загрузятся.
