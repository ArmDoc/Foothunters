FROM alpine:3.16

LABEL maintainer="Aparg"

EXPOSE 3000
WORKDIR /var/www

COPY /src/resources/js/quasar /var/www


ENV ALPINE_MIRROR "http://dl-cdn.alpinelinux.org/alpine"
RUN echo "${ALPINE_MIRROR}/v3.16/main/" >> /etc/apk/repositories
RUN apk add nodejs npm --repository="${ALPINE_MIRROR}/v3.16/main/"

RUN npm install -g yarn @quasar/cli

#RUN yarn install

CMD yarn install && \
    quasar dev