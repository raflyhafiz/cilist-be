FROM node:16.19.0 AS builder

WORKDIR /app

RUN apt update && apt install nano -y

COPY .env.example .env

COPY . /app

RUN npm install



FROM node:16.19.0-slim

COPY --from=builder /app /app

WORKDIR /app

EXPOSE 5000

CMD ["npm", "start"]