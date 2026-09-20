# ── Stage 1: Build React SPA ──
FROM node:20-alpine AS builder

WORKDIR /app

# Copy dependency definitions
COPY package.json yarn.lock* ./

# Install dependencies using Yarn
RUN yarn install --network-timeout 100000

# Copy frontend source
COPY . .

# Set relative backend URL so requests hit Nginx reverse proxy
ENV REACT_APP_BACKEND_URL="" \
    GENERATE_SOURCEMAP=false \
    CI=false

# Build production bundle
RUN yarn build

# ── Stage 2: Production Nginx Server ──
FROM nginx:alpine AS runner

# Remove default static pages
RUN rm -rf /usr/share/nginx/html/*

# Copy compiled assets from builder
COPY --from=builder /app/build /usr/share/nginx/html

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/conf.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
