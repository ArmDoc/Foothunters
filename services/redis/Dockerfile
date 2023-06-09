FROM redis:6.2-alpine@sha256:4bed291aa5efb9f0d77b76ff7d4ab71eee410962965d052552db1fb80576431d

LABEL maintainer="Aparg"

EXPOSE 6379

CMD redis-server --requirepass ${REDIS_PASSWORD}