
# Part 3

### 3.1 A deployment pipeline to heroku

https://github.com/J0ne/web-components-dev

Disclaimer: workflow works and container is deployed to heroku. There are some problems... either $PORT issue (even with nginx) 

### 3.2
[todo]


### 3.3

Backend Dockerfile
```sh
FROM golang:1.16
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN useradd -m appuser
USER appuser
RUN chown -R appuser /usr/src/app
ENV REQUEST_ORIGIN=http://localhost:5000
RUN go build
ENTRYPOINT [ "./server" ]
```

Frontend Dockerfile
```sh
FROM node:14
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN useradd -m appuser
USER appuser
RUN chown -R appuser /usr/src/app

ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm install && \
    npm run build

RUN npm i -g serve
```
### 3.4

I managed to reduce the size mostly by changing the base image. But nothing else since the image files were so tiny.
Disclaimer: at this exercise I didn't fix the ``chown``command. I just tried to examine the size of images even they did not work properly after... :)
Edited: this is fixed in ex 3.5

Backend:
At first: 1.06GB 
golang:1.16 -> golang:alpine

 467MB 
->
```sh
FROM golang:alpine
WORKDIR /usr/src/app
COPY . /usr/src/app
ENV REQUEST_ORIGIN=http://localhost:5000
RUN go build
ENTRYPOINT [ "./server" ]
```

Frontend:

At first: 1.17GB

node:14 -> node:14-alpine
-> 344MB
```sh
FROM node:14-alpine
WORKDIR /usr/src/app
COPY . /usr/src/app
ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm install && \
    npm run build
RUN npm i -g serve
```

### 3.5


With Ubuntu

- 34_backend-app  467MB
- 34_front-end    447MB

Alpine:

Backend:
```sh
FROM golang:alpine
WORKDIR /usr/src/app
COPY . /usr/src/app
ENV REQUEST_ORIGIN=http://localhost:5000
RUN adduser -D userapp
RUN chown -R userapp /usr/src/app
USER userapp
RUN go build
ENTRYPOINT [ "./server" ]
```

Frontend

```sh
FROM node:14-alpine
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN adduser -D userapp
RUN chown -R userapp /usr/local/ /usr/src/app
USER userapp
ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm install && \
    npm run build
RUN npm i -g serve
```

[TODO] Sizes after


### 3.6: Multi-stage frontend

Dockerfile:
```sh
FROM node:14-alpine as builder
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN adduser -D userapp
RUN chown -R userapp /usr/local/ /usr/src/app
USER userapp
ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm ci && \
    npm run build

FROM node:14-slim
COPY --from=builder /usr/src/app/build /usr/src/app
RUN npm i -g serve
EXPOSE 5000
CMD [ "serve","-s", "-l", "5000", "/usr/src/app"]
```
