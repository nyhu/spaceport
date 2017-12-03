FROM node:latest

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm install --quiet
RUN npm install -g nodemon

COPY . .

EXPOSE 3000
EXPOSE 27017

CMD ["node", "app.js"]
