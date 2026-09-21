# P1. GitVerse CI/CD

## Что такое GitVerse

[GitVerse](https://gitverse.ru) — отечественная платформа для хостинга кода от Сбера с поддержкой CI/CD. Используется как альтернатива GitHub Actions в российских проектах.

## Схема пайплайна

```
push / merge request
        │
        ▼
    [lint]           стадия 1 — все ветки
    mkdocs build --strict
        │
        ▼
    [build]          стадия 2 — все ветки
    pip cache + mkdocs build + artifact
        │
        ▼
    [deploy]         стадия 3 — только main
    rsync SSH → Helios
```

## Конфигурация `.gitverse-ci.yml`

```yaml
stages:
  - lint
  - build
  - deploy

variables:
  PYTHON_VERSION: "3.12"

cache:
  key: pip-$CI_COMMIT_REF_SLUG
  paths:
    - .pip-cache/

lint:
  stage: lint
  image: python:3.12-slim
  script:
    - pip install --cache-dir .pip-cache -r requirements.txt
    - mkdocs build --strict
  rules:
    - when: always

build:
  stage: build
  image: python:3.12-slim
  script:
    - date +%s > /tmp/t_start
    - pip install --cache-dir .pip-cache -r requirements.txt
    - echo "pip took $(($(date +%s) - $(cat /tmp/t_start)))s"
    - mkdocs build --strict
  artifacts:
    paths:
      - site/
    expire_in: 1 hour
  rules:
    - when: always

deploy:
  stage: deploy
  image: python:3.12-slim
  script:
    - apt-get install -y sshpass rsync
    - export SSHPASS="$HELIOS_PASSWORD"
    - sshpass -e rsync -avz --delete
        -e "ssh -p 2222 -o StrictHostKeyChecking=no"
        site/ s506807@helios.cs.ifmo.ru:~/public_html/ssg/
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

## Секреты в GitVerse

Переменная `HELIOS_PASSWORD` добавляется через:

**Settings → CI/CD → Variables → Add Variable**

- Key: `HELIOS_PASSWORD`
- Value: (пароль)
- Protected: ✅
- Masked: ✅

В логах CI значение маскируется: вместо пароля выводится `[MASKED]`.

## Сравнение с GitHub Actions

| Аспект | GitHub Actions | GitVerse |
|---|---|---|
| Конфиг файл | `.github/workflows/*.yml` | `.gitverse-ci.yml` |
| Синтаксис | `jobs` + `steps` | `stages` + `script` |
| Кэш pip | `actions/setup-python cache: pip` | `cache: paths: [.pip-cache/]` |
| Артефакты | `upload-artifact` action | `artifacts: paths:` |
| Условия | `if: github.ref == 'refs/heads/main'` | `if: $CI_COMMIT_BRANCH == "main"` |
| Pages деплой | `deploy-pages` action | нет встроенного, нужен rsync |
| Секреты | Settings → Secrets → Actions | Settings → CI/CD → Variables |
| Маскирование | автоматически | явная опция Masked: ✅ |

**Что изменилось по сравнению с GitHub Actions:**

1. Синтаксис из `jobs/steps` → `stages/script`
2. Нет встроенного деплоя на Pages — только rsync на Helios
3. Кэш pip настраивается явно через `paths`, а не через action
4. Переменные окружения через `$CI_...` вместо `${{ github... }}`
5. Артефакты — встроенный механизм, не нужен отдельный action

## Воспроизводимость сборки

Фиксация версий обеспечивается тремя уровнями:

- **Python**: `image: python:3.12-slim` — точный образ
- **Пакеты**: `requirements.txt` с точными версиями (`==`)
- **Раннер**: чистый Docker-контейнер при каждом запуске
