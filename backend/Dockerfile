FROM node:24-alpine AS builder

WORKDIR /app

RUN npm install -g pnpm@latest

RUN pnpm config set strict-dep-builds false

COPY package.json pnpm-lock.yaml* ./

RUN pnpm install --frozen-lockfile

COPY tsconfig.json ./
COPY src ./src

RUN pnpm run build

RUN pnpm prune --prod

FROM node:24-alpine AS runner

WORKDIR /app

ENV NODE_ENV=production
ENV PORT=3000

RUN addgroup --system --gid 1001 nodejs && \
    adduser --system --uid 1001 nodejs

COPY --from=builder /app/package.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

USER nodejs

EXPOSE 3000

CMD ["node", "dist/server.js"]