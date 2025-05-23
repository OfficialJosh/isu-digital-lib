# Base Node.js image
FROM node:20-alpine AS base

# Install dependencies
FROM base AS deps
WORKDIR /app

# Copy package files
COPY package*.json ./
RUN npm ci

# Build the application
FROM base AS builder
WORKDIR /app

# Copy dependencies and source code
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Set environment variables for BUILD TIME ONLY
# These are placeholder values just to make the build succeed
ENV NODE_ENV=production
ENV NEXT_PUBLIC_BASE_PATH=""
ENV MY_OPENAI_API_KEY="placeholder-during-build"
ENV QDRANT_URL="placeholder-during-build"
ENV QDRANT_API_KEY="placeholder-during-build"

# Build the application
RUN npm run build

# Production image
FROM base AS runner
WORKDIR /app

ENV NODE_ENV=production
ENV PORT=3000
# Note: Real environment variables will be injected at runtime by Cloud Run

# Copy necessary files
COPY --from=builder /app/public ./public
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static

# Expose port and start application
EXPOSE 3000
CMD ["node", "server.js"]