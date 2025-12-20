# ---------- Build stage ----------
FROM node:18-alpine AS builder
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci --silent

COPY . .

# Build-time API URL (React reads REACT_APP_* at build time)
ARG REACT_APP_API_URL
ENV REACT_APP_API_URL=$REACT_APP_API_URL

RUN npm run build

# ---------- Runtime stage ----------
FROM nginxinc/nginx-unprivileged:alpine

# Copy nginx config
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Copy built static files
COPY --from=builder /app/build /usr/share/nginx/html

# nginx-unprivileged listens on 8080
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]