FROM node:20.10.0-slim

WORKDIR /app

COPY . .

RUN yarn install

CMD [ "yarn","dev" ,"--host"]