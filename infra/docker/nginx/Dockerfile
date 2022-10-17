FROM node:16-alpine as node
FROM nginx:1.20-alpine
LABEL maintainer="ucan-lab <yes@u-can.pro>"
SHELL ["/bin/ash", "-oeux", "pipefail", "-c"]

ENV TZ=UTC

RUN apk update && apk add --update --no-cache --virtual=.build-dependencies \
&& apk add --no-cache --virtual g++ \
&& apk add --no-cache libstdc++ && apk add --no-cache libgcc \
&& apk -UvX http://dl-4.alpinelinux.org/alpine/edge/main add -u nodejs npm

# npm commands
# RUN npm install webpack
# RUN npm -v && node -v
# RUN npm run develop
# RUN npm run watch

# node command
COPY --from=node /usr/local/bin /usr/local/bin
# npm command
COPY --from=node /usr/local/lib /usr/local/lib

# nginx config file
COPY ./infra/docker/nginx/*.conf /etc/nginx/conf.d/

WORKDIR /work/home
