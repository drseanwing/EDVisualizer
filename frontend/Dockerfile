# ----- Build stage (use Node 16 to avoid OpenSSL/webpack breakage) -----
FROM node:16-bullseye AS build
WORKDIR /app

# Install deps from package.json/package-lock.json
COPY package*.json ./
RUN npm ci

# Copy source and build
COPY . .
# Optional: make CRA non-interactive in CI
ENV CI=true
RUN npm run build

# ----- Serve stage (static files via nginx) -----
FROM nginx:alpine
# Basic SPA config: serve index.html for unknown paths
RUN printf 'server { \
  listen 80; \
  server_name _; \
  root /usr/share/nginx/html; \
  index index.html; \
  location / { try_files $uri /index.html; } \
}\n' > /etc/nginx/conf.d/default.conf

COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
