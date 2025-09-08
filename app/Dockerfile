FROM node:20-alpine AS builder
WORKDIR /app

# Upgrade system packages to get security fixes
RUN apk update && apk upgrade && rm -rf /var/cache/apk/*

COPY ./package.json .
COPY ./yarn.lock .
RUN yarn install
COPY . .
ARG TMDB_V3_API_KEY
ENV VITE_APP_API_ENDPOINT_URL="https://api.themoviedb.org/3"
RUN echo "API Key length: ${#TMDB_V3_API_KEY}" && \
    echo "Building with API key..." && \
    VITE_APP_TMDB_V3_API_KEY=${TMDB_V3_API_KEY} yarn build

FROM nginx:stable-alpine
WORKDIR /usr/share/nginx/html

# Upgrade system packages in the nginx image as well
RUN apk update && apk upgrade && rm -rf /var/cache/apk/* && \
    rm -rf ./*
COPY --from=builder /app/dist .
EXPOSE 80
ENTRYPOINT ["nginx", "-g", "daemon off;"]