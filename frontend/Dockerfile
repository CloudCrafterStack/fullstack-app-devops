# === Build Angular app ===
FROM node:20 AS build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build -- --configuration production

# === Serve with Nginx ===
FROM nginx:alpine

COPY nginx.conf /etc/nginx/conf.d/default.conf

# Angular outputs to dist/crud-angular
COPY --from=build /app/dist/crud-angular /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]     



