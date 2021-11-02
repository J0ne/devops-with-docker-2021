
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

