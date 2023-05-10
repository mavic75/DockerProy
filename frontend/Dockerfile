# STEP 1 BUILD dist
FROM node:16.20.0 AS build
RUN mkdir -p /app
WORKDIR /app
COPY package.json /app
RUN npm install
COPY . /app
RUN npm run build
#STEP 2 build docker image
FROM nginx:1.20.1
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 8085:80
