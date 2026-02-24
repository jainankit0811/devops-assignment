# Stage 1: Build
FROM node:18-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm install --legacy-peer-deps

COPY . .
RUN export NODE_OPTIONS=--max-old-space-size=4096 && npm run build -- --configuration production && ls -R dist/angular-15-crud

# Stage 2: Serve
FROM nginx:alpine

COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
