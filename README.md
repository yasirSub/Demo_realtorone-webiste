# RealtorOne Website

React 19 + TypeScript + Vite admin website for RealtorOne.

## Local development

Install dependencies and start Vite:

```bash
npm install
npx vite --host localhost
```

For local frontend against the live VPS backend, use:

```env
VITE_API_BASE_URL=http://187.77.184.129:8000/api
```

This can be placed in `.env.local`.

## Production behavior

The app chooses its API base like this:

- On localhost, it defaults to `http://127.0.0.1:8000/api`
- On a deployed website, it defaults to `/api`

That means the production container should proxy `/api` to the existing Laravel backend.

## Auto deploy from GitHub to VPS Docker

This repo is set up for push-to-deploy through GitHub Actions.

Flow:

1. You push changes to `main`
2. GitHub Actions connects to the VPS over SSH
3. GitHub Actions uploads the latest repo snapshot to the VPS
4. `docker compose up -d --build` rebuilds and restarts the website container

The website container serves static files with nginx, and nginx proxies `/api/` to the backend URL from `API_PROXY_URL` using [nginx.conf.template](nginx.conf.template).

Add these GitHub repository secrets before using the workflow in [.github/workflows/deploy.yml](.github/workflows/deploy.yml):

- `VPS_HOST`
- `VPS_USERNAME`
- `VPS_SSH_KEY`
- `VPS_APP_DIR`
- `API_PROXY_URL`

Recommended values for your VPS:

- `VPS_HOST=187.77.184.129`
- `VPS_USERNAME=root`
- `API_PROXY_URL=http://187.77.184.129:8000/api/`
- `VPS_APP_DIR=/root/realtorone/website`

The website stack now defaults to port `80`, so `WEBSITE_PORT` is optional and usually should be left unset unless you intentionally want a different external port.

`VPS_SSH_KEY` must be the full private SSH key contents, not the SSH command.

You do not need a GitHub deploy key for this workflow because the VPS does not pull from GitHub directly.

The frontend code already uses `/api` automatically in production via [src/api/client.ts](src/api/client.ts).

## Build

```bash
npm run build
```
