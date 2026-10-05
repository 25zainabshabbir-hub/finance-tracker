# Stage 1: compile TypeScript (needs dev dependencies)
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: production dependencies only (node-pg-migrate, pg)
FROM node:20-alpine AS deps
WORKDIR /app
COPY package*.json ./
RUN npm ci --omit=dev

# Stage 3: final image. No npm, so the command below calls node directly.
FROM node:20-alpine
WORKDIR /app

RUN rm -rf /usr/local/lib/node_modules \
           /usr/local/bin/npm /usr/local/bin/npx /usr/local/bin/corepack \
           /usr/local/bin/yarn /usr/local/bin/yarnpkg /opt/yarn-*

COPY --from=deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist
# runner.ts reads ../migrations and ../seeds relative to dist/
COPY migrations ./migrations
COPY seeds ./seeds
COPY package.json ./

USER node
# Same thing "npm run migrate:up" does, without needing npm
CMD ["node", "dist/runMigrations.js", "up"]
