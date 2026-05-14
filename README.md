# Online Blog Application

A minimal full-stack blog (MongoDB, Express, React, Node) in the same style as the MERN student manager: list CRUD from a single page, REST API with Mongoose, and Vite for the frontend.

## What you get

- **Backend** (`server`): Express + Mongoose, `/api/posts` CRUD, JSON body limit, `/api/health` for checks.
- **Frontend** (`client`): React + Vite + Axios; create, list, edit, and delete posts (title, author, content).
- **Database**: MongoDB (local or Docker). Defaults to `mongodb://127.0.0.1:27017/blogdb`.
- **Deployment**: Multi-stage `Dockerfile` (Node 20 LTS) builds the client and serves it from the API in production; `docker-compose.yml` runs MongoDB and the app together.

Dependencies use **pinned semver ranges** compatible with **Node 18+ / 20 / 22** (current LTS lines). Avoid `latest` in production.

## Local development

1. Start MongoDB (local install or only the `mongo` service from Docker:  
   `docker compose up mongo -d`).

2. **Server**

   ```bash
   cd server
   cp .env.example .env
   npm install
   npm run dev
   ```

   API: `http://localhost:5000` (see `http://localhost:5000/api/health`).

3. **Client** (separate terminal)

   ```bash
   cd client
   npm install
   npm run dev
   ```

   The Vite dev server proxies `/api` to `http://127.0.0.1:5000`, so leave `VITE_API_URL` unset and the UI will call `/api/posts` on port 5173.

## Environment variables

| Variable | Purpose |
|----------|---------|
| `PORT` | API port (default `5000`). |
| `MONGO_URI` | Mongo connection string. |
| `CLIENT_ORIGIN` | CORS origin in development (default `http://localhost:5173`). In production, CORS reflects any origin for simplicity; tighten this if you expose the API separately. |
| `NODE_ENV` | Set to `production` to serve the built SPA from `client-dist` and enable the production CORS behavior above. |

Client (optional):

| Variable | Purpose |
|----------|---------|
| `VITE_API_URL` | Base URL for the API (no trailing slash). Empty = same origin (used in Docker build). |

## API

- `GET /api/health` — service check.
- `GET /api/posts` — all posts, newest first.
- `GET /api/posts/:id` — one post.
- `POST /api/posts` — body: `{ title, author, content }`.
- `PUT /api/posts/:id` — same fields.
- `DELETE /api/posts/:id`.

## Docker (full stack)

From the repo root:

```bash
docker compose up --build
```

Open `http://localhost:5000`. MongoDB data persists in the `blog_mongo_data` volume.

## Production build without Docker

```bash
cd client && npm install && npm run build
cd ../server && npm install --omit=dev
```

Copy `client/dist` to `server/client-dist`, set `NODE_ENV=production` and `MONGO_URI`, then `node index.js` from `server`.
