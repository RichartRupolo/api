# Stage 1
FROM node:18-alpine as npm-install
WORKDIR /usr/app
# Install app dependencies
COPY package.json ./
RUN rm -rf node_modules
RUN npm cache clean --force
RUN apk add linux-headers
RUN apk update && apk add libusb-dev
RUN apk update && apk upgrade && apk add eudev-dev
RUN apk update && \
    apk upgrade && \
    apk add --no-cache --virtual build-dependencies bash git openssh python3 make g++ libc6-compat && \
    npm install --no-cache && \
    npm install --dotenv-extended && \   
    npm cache clean --force

# Runtime container with minimal dependencies
FROM node:18-alpine

RUN apk update && \
    apk upgrade && \
    apk add ca-certificates libc6-compat && \
    ln -s /lib/libc.musl-x86_64.so.1 /lib/ld-linux-x86-64.so.2

WORKDIR /usr/app
COPY --from=npm-install /usr/app/node_modules /usr/app/node_modules
# ENV NODE_PATH=/usr/local/lib/node_modules
# Bundle app source
COPY . .

RUN npm run build
EXPOSE 3000
# ENTRYPOINT ["node", "-r", "dotenv/config", "src/index.ts"]
CMD echo "use docker CLI to specify a startup command"
