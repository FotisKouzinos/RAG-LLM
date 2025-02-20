# Stage 1: Build stage with Node.js v20.18.3 and Flowise v2.1.1
FROM alpine:3.20 AS build

USER root

# Install necessary packages
RUN apk add --no-cache curl tar python3 py3-pip make g++ git chromium cairo-dev pango-dev

# Download and install Node.js v20.18.3
RUN curl -fsSLO https://nodejs.org/dist/v20.18.3/node-v20.18.3-linux-x64.tar.xz \
    && tar -xJf node-v20.18.3-linux-x64.tar.xz \
    && cp -R node-v20.18.3-linux-x64/{bin,include,lib,share} /usr/

# Skip downloading Chrome for Puppeteer
ENV PUPPETEER_SKIP_DOWNLOAD=true

# Install Flowise v2.1.1 globally
RUN npm install -g flowise@2.1.1

# Enable Yarn and pnpm
RUN corepack enable yarn
RUN corepack enable pnpm

# Stage 2: Runtime stage
FROM alpine:3.20

# Install runtime dependencies
RUN apk add --no-cache chromium git python3 py3-pip make g++ build-base cairo-dev pango-dev

# Set Puppeteer Chromium path
ENV PUPPETEER_EXECUTABLE_PATH=/usr/bin/chromium-browser

# Copy installed Node.js and Flowise from the build stage
COPY --from=build /usr /usr
COPY --from=build /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=build /usr/local/bin /usr/local/bin

# Confirm Node.js version and Flowise version at runtime
RUN node -v && flowise --version

# Start Flowise
ENTRYPOINT ["flowise", "start"]
