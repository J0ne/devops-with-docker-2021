### 1.12: Hello, frontend!

Dockerfile:
```sh
FROM node:14
WORKDIR /usr/src/app
COPY /example-frontend /usr/src/app
RUN npm install && \
npm run build
RUN npm i -g serve
CMD ["serve", "-s", "-l", "5000", "build"]
```

Command: 
```sh
docker build . -t front && docker run -p 5000:5000 front
```

And the message: Exercise 1.12: Congratulations! You configured your ports correctly!

### 1.13: Hello, backend!

Dockerfile:
```sh
FROM golang:1.16
WORKDIR /usr/src/app
COPY /example-backend /usr/src/app
RUN go build
ENV REQUEST_ORIGIN=http://localhost:5000
ENV PORT=8080
ENTRYPOINT [ "./server" ]
```
Command: 
```sh
docker build . -t back && docker run -p 8080:8080 back
```

### 1.14: Environment

Frontend

Dockerfile:
```sh
FROM node:14
WORKDIR /usr/src/app
COPY /example-frontend /usr/src/app
ENV REACT_APP_BACKEND_URL=http://localhost:8080
RUN npm install && \
npm run build
RUN npm i -g serve
CMD ["serve", "-s", "-l", "5000", "build"]
```

Commands: 
```sh
docker build . -t front && docker run -p 5000:5000 front
```

Backend:

Dockerfile:
```sh
FROM golang:1.16
WORKDIR /usr/src/app
COPY /example-backend /usr/src/app
RUN go build
ENV REQUEST_ORIGIN=http://localhost:5000
ENV PORT=8080
ENTRYPOINT [ "./server" ]
```
Commands: 
```sh
docker build . -t back && docker run -p 8080:8080 back
```

### 1.5: Homework

https://hub.docker.com/repository/docker/j0ne/devops_docker

Here is the repo: https://github.com/J0ne/Hsl_demo_backend

Disclaimer: this back end app sends a lot of messages via socket connection. 

### 1.6: Heroku

https://jonedocker.herokuapp.com/
