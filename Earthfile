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
  COPY package.json /app/build/package.json
  COPY yarn.lock /app/build/yarn.lock
  WORKDIR /app/build
  COPY . /app/build
  RUN npx quasar build -m pwa
  RUN pwd
  RUN ls -al
  SAVE ARTIFACT /app/build/dist/pwa /app

image:
  FROM nginx
  ARG EXPECTED_REF=${repo}/${project}:${version}
  COPY ./nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf
  COPY +build/app /app
  SAVE IMAGE --push ${EXPECTED_REF}

ci:
  BUILD +image
