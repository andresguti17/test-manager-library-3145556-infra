# test-manager-library-3145556-infra

Containers, network, volumes, and the empty database engine for *Manager Library* (T-20).

## Up

```powershell
Copy-Item .env.example .env   # fill in JWT_SECRET and ADMIN_PASSWORD first
docker compose up -d --build
```

The database starts **empty**: no tables until the api runs its migrations
(ADR-001 — the schema belongs to the application migrations). This repo
contains no `CREATE TABLE` by design; verification:

```powershell
docker compose exec db psql -U library -d library -c "\dt library.*"
# Did not find any relation — expected before migrations
```

## Services

| Service | Image / build | Port |
|---|---|---|
| `db` | `postgres:16`, UTC, volume `pgdata` | 5432 |
| `api` | `../api` (Laravel), volume `covers` at `/var/covers` | 8000 |
| `app` | `../app` (React) | 5173 |

Single network `library-net`. `api` waits for `db` to be healthy; `app` waits for `api`.

## Down

```powershell
docker compose down        # keep data
docker compose down -v     # wipe data (fresh engine)
```
