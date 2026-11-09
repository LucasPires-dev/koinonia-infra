# Koinonia · Infra

Orquestração com Docker do ecossistema **Koinonia + Shepherd's Toolkit**: as duas APIs Django, o worker do Celery, Redis e PostgreSQL. Roda localmente com **Docker Compose** e em produção, numa VPS, com **Docker Swarm**.

> Serviços: [Koinonia-Api](https://github.com/LucasPires-dev/Koinonia-Api) · [Shepherd's Toolkit API](https://github.com/LucasPires-dev/LucasPires-dev-shepherds-toolkit-api)

## 🧩 Serviços

| Serviço | Imagem | Papel |
|---|---|---|
| `koinonia-api` | `koinonia-api` | API Django do Koinonia (Gunicorn) |
| `koinonia-celery` | `koinonia-api` | Worker do Celery: envio de mensagens agendadas |
| `shepherds-toolkit-api` | `shepherds-toolkit-api` | API Django do Shepherd's Toolkit |
| `st-db` | `postgres:15-alpine` | Banco do Shepherd's Toolkit |
| `redis` | `redis:7-alpine` | Fila do Celery |

O banco do Koinonia é um PostgreSQL gerenciado, configurado por variável de ambiente.

## 🧠 Destaques

- **Mesma imagem, dois papéis**: a API e o worker do Celery saem do mesmo código, mudando só o comando.
- **Segredos do Swarm**: em produção, senhas e chaves viram *Docker secrets* (`make secrets`), sem ficar em variáveis de ambiente.
- **Rede interna**: os serviços conversam pela rede `koinonia-net`; Redis e bancos não ficam expostos, e as APIs ficam atrás de um proxy reverso (Nginx, Caddy ou Traefik).
- **Healthcheck e volume persistente** no PostgreSQL.

## 🚀 Como usar

Os repositórios das APIs precisam estar ao lado desta pasta (`../Koinonia-Api` e `../shepherds-toolkit-api`).

```bash
cp .env.example .env     # preencha as variáveis
make build               # builda as imagens das duas APIs
make up                  # sobe tudo com Docker Compose
make migrate             # roda as migrations nas duas APIs
make logs
```

Produção (VPS com Docker Swarm):

```bash
make secrets             # cria/atualiza os Docker secrets a partir do .env
make deploy              # builda e publica o swarm-stack.yml
make ps                  # status dos serviços
```

| Comando | O que faz |
|---|---|
| `make build` | Builda as imagens `koinonia-api` e `shepherds-toolkit-api` |
| `make up` / `make down` | Sobe e derruba o ambiente local |
| `make migrate` | Migrations nas duas APIs |
| `make secrets` | Cria os Docker secrets no Swarm |
| `make deploy` | Publica a stack no Swarm |
| `make rm-stack` | Remove a stack |

---

Desenvolvido por [Lucas C. Pires](https://github.com/LucasPires-dev) · Full-stack Python
