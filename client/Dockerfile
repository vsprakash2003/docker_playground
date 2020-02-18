FROM node:alpine as builder

WORKDIR "/app"

COPY ./package.json ./

RUN yarn install

COPY ./ ./

RUN yarn build

FROM nginx

EXPOSE 3000

COPY ./nginx/default.conf /etc/nginx/conf.d/defaut.conf

COPY --from=builder /usr/app/build /usr/share/nginx/html