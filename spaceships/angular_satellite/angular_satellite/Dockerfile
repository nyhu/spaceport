# Builder
FROM node:8.1.4-alpine as clientBuilder

WORKDIR /app

COPY package.json package-lock.json ./
RUN npm i

COPY . /app
RUN npm run build:prod

# Base image
FROM nginx

WORKDIR /app

COPY --from=clientBuilder /app /usr/share/nginx/html

EXPOSE 80
