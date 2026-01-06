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
2. **Permissions:** `chmod 600 traefik/acme.json`
3. **Env:** Update `ACME_EMAIL` in `traefik.yml`.
4. **Auth** Using https://www.web2generators.com/apache-tools/htpasswd-generator generate the HTPasswd and update `DASHBOARD_AUTH` in `middlewared.yml`
5. **Deploy:** `docker compose up -d`



## 🔒 Security Notes
- **Dashboard Auth**: The dashboard is protected by Basic Auth. Never expose it without the admin-auth@file middleware.

- **Docker Socket**: The socket is mounted as :ro (read-only). This prevents Traefik from making changes to your host's Docker engine.

- **HSTS**: The secure-headers middleware is enabled by default in the example above to ensure browsers only connect via HTTPS.

## 📄 License
This project is open-source and available under the MIT License.


## 📦 Exposing Container Publicly
Add these labels to any app you want to be public:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.myapp.rule=Host(`myapp.com`)"
  - "traefik.http.routers.myapp.entrypoints=websecure"
  - "traefik.http.routers.myapp.tls.certresolver=letsencrypt"
  - "traefik.http.services.myapp.loadbalancer.server.port=80"