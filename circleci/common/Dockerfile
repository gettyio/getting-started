FROM node:10

USER node

WORKDIR /home/node/repo

COPY --chown=node:node package.json ./
COPY --chown=node:node node_modules/ ./node_modules
COPY --chown=node:node build/ ./build

CMD yarn start