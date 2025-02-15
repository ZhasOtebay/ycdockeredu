FROM python:3.12.1 as builder
# -- Медленные операции --
# Установка системных зависимостей (в т.ч. для зависимостей python)
RUN apt-get update \
    && apt-get install -y --no-install-recommends gcc \
    && rm -rf /var/lib/apt/lists/*

# Установка зависмостей python в диррекиторию /app/wheels
RUN --mount=type=bind,source=requirements.txt,target=/app/requirements.txt \
  pip wheel --no-cache-dir --no-deps -r /app/requirements.txt --wheel-dir /app/wheels

# ---------

FROM python:3.12.1-slim

# Копирование собранных файлов python из образа builder
COPY --from=builder /app/wheels /wheels

# Установка зависимостей, которые нужны для работы приложения
RUN apt-get update \
    && apt-get install -y --no-install-recommends libpq-dev \
    && rm -rf /var/lib/apt/lists/*

# Установка зависимости python, не пересобирая их
RUN pip install --no-cache --no-cache-dir /wheels/*

# Копирование кода приложения
COPY app.py /app/

# Добавление пользователя и назначение его на каталог приложения
RUN addgroup --system app \
  && adduser --system --group app \
  && chown -R app:app /app/
USER app

# Установка рабочей директории
WORKDIR /app

CMD ["uvicorn", "app:app", "--host", "0.0.0.0"]
