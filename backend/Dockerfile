FROM python:3.12-alpine AS builder
WORKDIR /app
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
RUN apk add --no-cache gcc musl-dev postgresql-dev libffi-dev
COPY requirements.txt .
RUN pip install --no-cache-dir --prefix=/install -r requirements.txt

FROM python:3.12-alpine
WORKDIR /app
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
RUN apk add --no-cache libpq
COPY --from=builder /install /usr/local
COPY . .
RUN addgroup -S django && adduser -S django -G django && \
    mkdir -p /app/staticfiles && \
    chown -R django:django /app
USER django
EXPOSE 8000
CMD ["gunicorn", "myproject.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "3"]
