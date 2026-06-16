# Stage 1: Build the CLI and compile native dependencies
FROM node:24-alpine AS builder

ARG LIVESYNC_VERSION=0.25.76

RUN apk add --no-cache git python3 make g++

WORKDIR /build

RUN git clone --depth 1 --branch ${LIVESYNC_VERSION} \
    --recurse-submodules --shallow-submodules \
    https://github.com/vrtmrz/obsidian-livesync.git .

RUN npm install

WORKDIR /build/src/apps/cli

RUN npm run build

# Install only runtime dependencies (native modules: leveldown, chokidar, etc.)
WORKDIR /runtime-deps
RUN cp /build/src/apps/cli/runtime-package.json ./package.json && \
    npm install --omit=dev

# Stage 2: Runtime
FROM ghcr.io/home-assistant/base:latest

RUN apk add --no-cache nodejs

COPY --from=builder /build/src/apps/cli/dist /opt/livesync-cli/dist
COPY --from=builder /runtime-deps/node_modules /opt/livesync-cli/node_modules

COPY rootfs /

LABEL \
  org.opencontainers.image.title="Home Assistant App: Obsidian LiveSync" \
  org.opencontainers.image.description="Self-hosted Obsidian LiveSync server. Syncs multiple Obsidian vaults with CouchDB in real-time without running Obsidian." \
  org.opencontainers.image.source="https://github.com/grunjol/addon-obsidian-livesync" \
  org.opencontainers.image.licenses="MIT"
