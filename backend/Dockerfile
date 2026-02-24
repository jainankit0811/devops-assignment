FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install --omit=dev

ENV NODE_ENV=production
COPY . .

EXPOSE 8080

CMD ["node", "server.js"]
