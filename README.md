# Autoscaler Service

## Описание

Этот репозиторий содержит сервис горизонтального масштабирования LLM-развертываний. Сервис хранит политики autoscaling, читает метрики Prometheus и обновляет количество реплик в LLMDeployment.

## Основные возможности
- создание autoscaling policy
- обновление min/max/target/cooldown параметров
- расчет желаемого числа реплик по метрикам
- запись истории autoscaling events
- обновление LLMDeployment через Kubernetes client manager
- служебная tick-ручка для autoscaling loop

## Структура проекта

- `app/` — основной код приложения
  - `main.py` — FastAPI-приложение и HTTP-ручки
  - `config.py` — настройки сервиса

- `deploy/` — файлы и переменные для развертывания
- `.env.example` — пример переменных окружения
- `Dockerfile` — сборка Docker-образа
- `pyproject.toml` — зависимости и настройки Python-проекта
- `requirements.txt` — список зависимостей для совместимого запуска без uv

## Быстрый старт локально

1. Установите зависимости:
   ```bash
   uv sync
   ```

2. Создайте `.env` на основе `.env.example`:
   ```bash
   cp .env.example .env
   ```

3. Запустите сервис:
   ```bash
   uv run uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```

Если `uv` не используется, можно запустить через обычный virtualenv:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
```

## Переменные окружения
- `DATABASE_URL`
- `PROMETHEUS_URL`
- `K8S_CLIENT_MANAGER_URL`
- `DEPLOYMENT_SERVICE_URL`
- `SECURITY_SERVICE_URL`
- `SERVICE_TOKEN`
- `LOG_LEVEL`

Пример `.env`:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/llm_platform
SERVICE_TOKEN=change-me
LOG_LEVEL=INFO
```

## Основные API-ручки
- `GET /health`
- `GET /livez`
- `GET /service-info`
- `GET /policies`
- `POST /policies`
- `GET /policies/{policy_id}`
- `PATCH /policies/{policy_id}`
- `DELETE /policies/{policy_id}`
- `GET /events`
- `POST /tick`

## Сборка и запуск в Docker

```bash
docker build -t hse-llm-project-2026/autoscaler_service:local .
docker run --env-file .env -p 8000:8000 hse-llm-project-2026/autoscaler_service:local
```

## Деплой в Kubernetes

Файлы развертывания лежат в папке `deploy/`. Для сервисов, которые уже подключены к стенду, используются Helm values и deploy-скрипты из соответствующего репозитория или общего инфраструктурного пайплайна.

## Метрики и документация

- Swagger UI: `/docs`
- OpenAPI: `/openapi.json`
- Health check: `/health`
- Liveness check: `/livez`

## Автор

Igor Malysh
