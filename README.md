# ubo-shop

PrestaShop e-commerce storefront for the fictional **UBO** brand.
Part of the `jkjdev.eu` portfolio.

Live at: https://shop.ubo.jkjdev.eu

## Stack

- **PrestaShop 9** (official `prestashop/prestashop:9` image)
- **MariaDB 12** for the shop database
- Exposed via the shared `apps_net` Docker network; TLS termination + routing handled by the `home-page` nginx container.

## Local setup

```bash
cp .env.example .env       # fill in real values, do not commit
docker compose up -d
```

First boot runs PrestaShop's auto-installer (`PS_INSTALL_AUTO=1`). The shop is reachable at `http://localhost` (or the proxy URL if behind `apps_net`).

## Deploy

Deployment is manual via GitHub Actions:

1. Push changes to `main`.
2. Go to Actions → "Deploy ubo-shop to Hetzner" → Run workflow.

On the server, the workflow runs:

```bash
cd ~/ubo-shop
git pull origin main
docker compose pull
docker compose up -d --force-recreate
```

## Volumes

- `shop_db_data` — MariaDB data directory. Persists across container rebuilds.
- `shop_files` — PrestaShop's `/var/www/html` (themes, modules, uploaded images, config). **Do not delete** unless you want to re-run the installer.

## Admin

Admin URL is `https://shop.ubo.jkjdev.eu/admin<random-suffix>` — PrestaShop randomizes the admin folder name on install. Find it by looking inside the `shop_files` volume after install (`docker exec shop ls /var/www/html | grep admin`), or check the installer output.
