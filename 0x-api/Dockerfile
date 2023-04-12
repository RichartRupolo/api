# Stage 1
FROM node:18-alpine as npm-install

WORKDIR /usr/app

# Install app dependencies
COPY package*.json ./
RUN apk add --no-cache linux-headers libusb-dev eudev-dev \
    && apk add --no-cache --virtual build-dependencies bash git openssh python3 make g++ libc6-compat \
    && rm -rf node_modules \
    && npm cache clean --force \
    && npm install --no-cache \
    && npm install --no-cache --dotenv-extended 

# Runtime container with minimal dependencies
FROM node:18-alpine

RUN apk add --no-cache ca-certificates libc6-compat \
    && ln -s /lib/libc.musl-x86_64.so.1 /lib/ld-linux-x86-64.so.2

WORKDIR /usr/app

COPY ./dotenv/config/.env ./
COPY --from=npm-install /usr/app/node_modules ./node_modules
COPY package*.json ./
COPY src ./src
COPY tsconfig*.json ./
RUN npm run build

EXPOSE 3001
