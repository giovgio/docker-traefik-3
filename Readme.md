# Docker Traefik v3 Template (2026 Edition)

This repository provides a production-ready, highly secure **Traefik v3** configuration for Docker. It includes automated Let's Encrypt SSL/TLS, global HTTP-to-HTTPS redirection, and security hardening for the Docker socket.

Did it work? offer me a coffee: https://buymeacoffee.com/theunfollower



## 🚀 Features
- **Traefik v3.x**: Optimized for the latest performance and security standards.
- **Automated SSL**: Automatic certificate generation via Let's Encrypt (HTTP Challenge).
- **Global Redirect**: Automatic port 80 to 443 redirection handled at the entrypoint level.
- **Security Hardened**: Read-only Docker socket and pre-configured security headers (HSTS, XSS protection).
- **Clean Labels**: No more duplicate labels for HTTP and HTTPS.

---

## 📂 Project Structure
```text
.
├── docker-compose.yml     # Traefik service definition
├── .env                   # Variables (Domain, Email, etc.)
├── .gitignore             # Files to exclude from Git
└── traefik/
    ├── traefik.yml        # Static configuration
    ├── acme.json          # SSL Certificates (Keep private!)
    └── dynamic/
        └── middlewares.yml # Reusable security/auth rules
```

## 🚀 Setup

1. **Network:** `docker network create proxy`
2. **Acme.json** `touch traefik/acme.json`
3. **Permissions:** `chmod 600 traefik/acme.json`
4. **Env:** Update `ACME_EMAIL` and `MY_DOMAIN` in `.env`.
5. **Auth** Using https://www.web2generators.com/apache-tools/htpasswd-generator generate the HTPasswd and update `DASHBOARD_AUTH` in `middlewared.yml`
6. **Deploy:** `docker compose up -d`


## 🔒 Security Notes
- **Dashboard Auth**: The dashboard is protected by Basic Auth. Never expose it without the admin-auth@file middleware.

- **Docker Socket**: The socket is mounted as :ro (read-only). This prevents Traefik from making changes to your host's Docker engine.

- **HSTS**: The secure-headers middleware is enabled by default in the example above to ensure browsers only connect via HTTPS.

## 📝 TO DO
- update code so that .env contains DASHBOARD_AUTH

## 📄 License
This project is open-source and available under the MIT License.


## 📦 Exposing Container Publicly
Add these labels to any app you want to be public:

```yaml
    labels:
      - "traefik.enable=true"
      # Tells Traefik which port the container is listening on internally
      - "traefik.http.services.[myappcontainername].loadbalancer.server.port=3000"

      # --- 1. Insecure Router (HTTP / Port 80) ---
      # Catches requests on Port 80
      - "traefik.http.routers.[myappcontainername]-http.rule=Host(`myapp.com`)"
      - "traefik.http.routers.[myappcontainername]-http.entrypoints=web"

      - "traefik.http.middlewares.[myappcontainername]-redirect.redirectscheme.scheme=https"
      - "traefik.http.middlewares.[myappcontainername]-redirect.redirectscheme.permanent=true"
      
      # Apply that unique middleware
      - "traefik.http.routers.[myappcontainername]-http.middlewares=[myappcontainername]-redirect"

      # --- 2. Secure Router (HTTPS / Port 443) ---
      # Catches requests on Port 443
      - "traefik.http.routers.[myappcontainername].rule=Host(`myapp.com`)"
      - "traefik.http.routers.[myappcontainername].entrypoints=websecure"
      # Enables TLS and uses Let's Encrypt
      - "traefik.http.routers.[myappcontainername].tls=true"
      - "traefik.http.routers.[myappcontainername].tls.certresolver=letsencrypt"
      # Applies your external security headers middleware
      - "traefik.http.routers.[myappcontainername].middlewares=secure-headers@file"
```

to add more than a url or the www use the following:

```yaml
"traefik.http.routers.[myappcontainername].rule=Host(`website`) || Host(`www.website`)"

```