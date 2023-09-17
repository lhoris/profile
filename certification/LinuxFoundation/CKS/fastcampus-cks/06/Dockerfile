FROM node:latest
USER root
WORKDIR /app
ENV PATH /app/node_modules/.bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
COPY package.json ./
RUN npm install --silent
COPY . ./
USER root
CMD ["npm", "start"]