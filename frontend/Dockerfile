# Build stage
FROM node:18-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm ci

COPY . .

RUN npm run build

# Serve stage
FROM node:18-alpine

RUN npm install -g serve

COPY --from=builder /app/build /build

RUN adduser -S appuser

USER appuser

EXPOSE 3000

CMD ["serve", "-s", "/build"]
