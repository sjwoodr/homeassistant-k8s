# syntax=docker/dockerfile:1
# You need Docker BuildKit enabled to build this Dockerfile.
#
# brew install docker-buildx
# export DOCKER_BUILDKIT=1
# docker build -t foo --platform=linux/amd64 .
#

# Define Alpine and NGINX versions to use.
ARG ALPINE_VERSION=3.17.3
ARG NGINX_VERSION=1.25.1

# Prepare an Alpine-based image with OpenSSL.
FROM alpine:${ALPINE_VERSION} as alpine
ARG DOMAIN_NAME=localhost
ARG DAYS_VALID=30

RUN apk add --no-cache openssl
RUN echo "Creating self-signed certificate valid for ${DAYS_VALID} days for domain ${DOMAIN_NAME}" && \
    openssl \
    req -x509 \
    -nodes \
    -subj "/CN=${DOMAIN_NAME}}" \
    -addext "subjectAltName=DNS:${DOMAIN_NAME}" \
    -days ${DAYS_VALID} \
    -newkey rsa:2048 -keyout /tmp/self-signed.key \
    -out /tmp/self-signed.crt

# Prepare an NGINX-based image with the certificate created above.
FROM docker.io/nginx:${NGINX_VERSION} as nginx
COPY --from=alpine /tmp/self-signed.key /etc/ssl/private
COPY --from=alpine /tmp/self-signed.crt /etc/ssl/certs
COPY <<EOF /etc/nginx/conf.d/default.conf
upstream websocket {
  server 192.168.0.142:8123;
}
server {
  listen 80;
  server_name homeassistant.n9oh.com;
    location / {
      access_log on;
      proxy_pass http://websocket;
      proxy_buffering off;
      proxy_http_version 1.1;
      proxy_redirect     off;
      proxy_set_header X-Real-IP \$remote_addr;
      proxy_set_header X-Forwarded-Host \$host;
      proxy_set_header X-Forwarded-Port \$server_port;
      proxy_set_header Upgrade \$http_upgrade;
      proxy_set_header Connection "Upgrade";
    }
}
EOF
