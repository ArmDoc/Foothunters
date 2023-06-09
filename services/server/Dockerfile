FROM nginx:1.21.6-alpine@sha256:efc09388b15fb423c402f0b8b28ca70c7fd20fe31f8d7531ae1896bbb4944999 AS base

LABEL maintainer="Aparg"

EXPOSE 443 80

COPY nginx.conf /etc/nginx/nginx.conf.template
CMD envsubst "$(printf '${%s} ' $(env|cut -d'=' -f1))" </etc/nginx/nginx.conf.template> /etc/nginx/nginx.conf && \
    nginx -g 'daemon off;' 

FROM base AS dev
RUN apk upgrade --update-cache --available && \
	apk add openssl && \
    openssl req -x509 -nodes -days 3650 \
    -subj "/C=AM/ST=Yerevan/L=Armenia/O=selfsigned/CN=localhost" -addext "subjectAltName=DNS:localhost" \
    -newkey rsa:2048 -keyout /etc/ssl/certs/privkey.pem \
    -out /etc/ssl/certs/fullchain.pem 