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

1. Create the Network
Traefik needs a dedicated network to communicate with other containers.

```docker network create proxy```

2. Configure Environment Variables
Create a .env file and update it with your information:

```text 

MY_DOMAIN=yourdomain.com
ACME_EMAIL=admin@yourdomain.com
# Generate auth: echo $(htpasswd -nB user) | sed -e s/\\$/\\$\\$/g
DASHBOARD_AUTH=user:$$2y$$05$$J.S...
```

3. Set Permissions
Let's Encrypt requires strict permissions on the certificate storage file:

```text 

touch traefik/acme.json
chmod 600 traefik/acme.json
```

4. Deploy

```docker compose up -d```


## 🔒 Security Notes
- **Dashboard Auth**: The dashboard is protected by Basic Auth. Never expose it without the admin-auth@file middleware.

- **Docker Socket**: The socket is mounted as :ro (read-only). This prevents Traefik from making changes to your host's Docker engine.

- **HSTS**: The secure-headers middleware is enabled by default in the example above to ensure browsers only connect via HTTPS.

## 📄 License
This project is open-source and available under the MIT License.