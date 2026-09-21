# Helios ИТМО

## Подключение

```bash
ssh s506807@helios.cs.ifmo.ru -p 2222
```

Сайт публикуется в подкаталог `/home/s506807/public_html/ssg/`, доступный по адресу `https://se.ifmo.ru/~s506807/ssg/`.

## Деплой через rsync

```bash
rsync -avz --delete site/ \
  s506807@helios.cs.ifmo.ru:/home/s506807/public_html/ssg/ \
  -e "ssh -p 2222"
```

## Базовый URL

Критично: `site_url` в `mkdocs.yml` должен совпадать с реальным URL подкаталога:

```yaml
site_url: https://se.ifmo.ru/~s506807/ssg/
```

Без этого все внутренние ссылки будут указывать на корень сервера.

## В GitHub Actions

Деплой настроен через SSH-ключ (секрет `HELIOS_SSH_KEY`) и только с ветки `main`.

```yaml
- name: deploy to helios
  if: github.ref == 'refs/heads/main'
  uses: appleboy/scp-action@v0.1.7
  with:
    host: helios.cs.ifmo.ru
    port: 2222
    username: s506807
    key: ${{ secrets.HELIOS_SSH_KEY }}
    source: site/
    target: ~/public_html/ssg/
    strip_components: 1
```
