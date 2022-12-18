# Earthfile
VERSION 0.6
ARG repo=duck1123
ARG project=astral
ARG version=latest

builder:
  FROM node:16.16.0-alpine3.16
  RUN apk --no-cache --virtual build-dependencies add \
      python3 \
      make \
      g++ \
    && yarn \
    && apk del build-dependencies

build:
  FROM +builder
  COPY package.json yarn.lock /app/build/
  WORKDIR /app/build
  COPY --dir +node-deps/node_modules node_modules
  COPY --dir \
    babel.config.js \
    .eslintignore \
    .eslintrc.js \
    jsconfig.json \
    nginx \
    .postcssrc.js \
    quasar.config.js \
    /app/build
  COPY --dir src src-pwa /app/build
  RUN npx quasar build -m pwa
  SAVE ARTIFACT /app/build/dist/pwa /app

image:
  FROM nginx
  ARG EXPECTED_REF=${repo}/${project}:${version}
  COPY ./nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf
  COPY +build/app /app
  SAVE IMAGE --push ${EXPECTED_REF}

node-deps:
  FROM +builder
  COPY package.json yarn.lock .
  RUN npm install -g npm@9.2.0
  RUN npx yarn install --frozen-lockfile
  SAVE ARTIFACT node_modules

ci:
  BUILD +image
