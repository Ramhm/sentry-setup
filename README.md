# Sentry

How to setup Sentry.io (open source) server in Docker Compose

![Sentry Architecture](./img/sentry_arch.png)

## Docker Compose

#### Redis

- [x] Service #1

Image:
```bash
redis:latest
```
Volumes:
```bash
- './data/sentry/redis/data:/data'
```
<br/>

#### PostgreSQL

- [x] Service #2

Image:
```bash
postgres:latest
```
Environment:
```bash
POSTGRES_USER: sentry
POSTGRES_PASSWORD: 89PsZXyRStOT2
POSTGRES_DB: sentry
```
Volumes:
```bash
- './data/sentry/postgres:/var/lib/postgresql/data'
```
<br/>

#### Sentry Base

- [x] Service #3

Image:
```bash
sentry:latest
```
Ports:
```bash
- '9000:9000'
```
Environment File:
```bash
.env
```
depends_on:
```
- sentry-redis
- sentry-postgres
```
Volumes:
```bash
- './data/sentry/sentry:/var/lib/sentry/files'
```
.env:
```bash
SENTRY_SECRET_KEY==(r&r()bsat53avyq5a-4tpe8eibsfa5m6ut@42afjdkx@5*s
SENTRY_POSTGRES_HOST=sentry-postgres
SENTRY_POSTGRES_PORT=5432
SENTRY_DB_NAME=sentry
SENTRY_DB_USER=sentry
SENTRY_DB_PASSWORD=a6HlxEp72ONSg
SENTRY_REDIS_HOST=sentry-redis
SENTRY_REDIS_PORT=6379
```
<br/>

#### Sentry Cron

- [x] Service #4

Image:
```bash
sentry:latest
```
Environment File:
```bash
.env
```
depends_on:
```bash
- sentry-redis
- sentry-postgres
```
Volumes:
```bash
- './data/sentry/sentry:/var/lib/sentry/files'
```
Command:
```bash
sentry run cron
```
<br/>

#### Sentry Worker

- [x] Service #5

Image:
```bash
sentry:latest
```
Environment File:
```bash
.env
```
depends_on:
```bash
- sentry-redis
- sentry-postgres
```
Volumes:
```bash
- './data/sentry/sentry:/var/lib/sentry/files'
```
Command:
```bash
sentry run worker
```

<br/>


# # Let's GO

#### Generate secret key

```bash
docker-compose run --rm sentry-base config generate-secret-key
```

And then set generated key to `SENTRY_SECRET_KEY` in `.env`.

#### Initialize database

If this is a new database, you'll need to run `upgrade`.

```bash
docker-compose run --rm sentry-base upgrade
```

And **create** an initial user, if you need.


### Service Start 

```bash
docker-compose up -d
```

And open `http://localhost:9000`
