FROM node:18-alpine3.17

WORKDIR /usr/app

COPY package*.json .

RUN npm install
RUN npm install prom-client

COPY . .

ENV MONGO_USERNAME="superuser"
ENV MONGO_PASSWORD="SuperPassword"
ENV MONGO_URI="mongodb+srv://supercluster.d83jj.mongodb.net/superData"

EXPOSE 3000

CMD ["npm", "start"]