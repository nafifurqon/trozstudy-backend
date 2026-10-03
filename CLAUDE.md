# trozstudy-backend

API for Trozstudy, a fashion company's ecommerce system. Serves two frontends in separate repos: `trozstudy-dashboard` (internal admin) and `trozstudy-ecommerce` (public storefront).

## Development rules

- This project is in the development phase. Nothing is deployed; there are no real users or data.
- When building a feature, refactor, rename, restructure, or delete existing code freely. Do not keep backward compatibility, deprecation shims, or legacy code paths.
- The dev database may be dropped or reset. Do not write code to preserve existing data.
- Build one feature at a time, and only what the current request asks for. Do not add placeholder pages, future modules, or speculative abstractions.
- A feature is done when it runs and has been verified. Then stop.

## Stack

- NestJS 12, ESM (`"type": "module"`): relative imports end in `.js`.
- PostgreSQL 17 (Docker), TypeORM via `@nestjs/typeorm`.
- Input validation: DTO classes + class-validator. Global `ValidationPipe` (`whitelist`, `forbidNonWhitelisted`, `transform`) in `src/main.ts`.
- API docs: `@nestjs/swagger` builds the OpenAPI document; Scalar serves it at `/docs`.
- Health: `GET /health` (`@nestjs/terminus`). `info.database.status: "up"` means the DB is connected; HTTP 503 when it is down.

## Commands

- Local dev: `docker compose up -d db`, then `npm run start:dev`.
- Everything in Docker: `docker compose up -d --build`.
- `npm run build`, `npm run lint` (oxlint), `npm test` (vitest).

## Configuration

- Ports and credentials live only in `.env` (template: `.env.example`). Read them with `ConfigService.getOrThrow()`. Never hardcode them or add fallback values in code, `Dockerfile`, or `docker-compose.yml`.
- In Docker, compose overrides `DB_HOST=db` and `DB_PORT=5432` for the `api` container.
- Compose project name: `trozstudy-ecommerce-backend`.

## Structure

- One Nest module per feature in `src/<feature>/` (module, controller, service, `dto/`, `entities/`). Example: `src/health/`.
- TypeORM: `synchronize: false`, `autoLoadEntities: true`. Decide the schema strategy (synchronize vs migrations) with the user when the first entity is added.
