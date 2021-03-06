### 2.1

docker-compose.yml

```sh
version: "3"

services:
  web_service:
    build: .
    volumes: 
      - ./text.log:/usr/src/app/text.log 
```

I had some difficult times with this one. I got error:
``Error response from daemon: OCI runtime create failed: container_linux.go:367: starting container process caused: exec: ".": executable file not found in $PATH: unknown``

Finally, I made the Dockerfile like this:
```sh
FROM devopsdockeruh/simple-web-service:alpine
CMD []
```
And it worked! I guess this is ok...

### 2.2

```sh
version: "3.5"

services:
  web_service:
    build: .
    ports:
      - 8000:8080
    command: ["server"] 
```

And the browser:
``{
message: "You connected to the following path: /",
path: "/"
}``

### 2.3

```sh
version: "3.5"

services:
  front_end:
    build: ./frontend
    ports:
      - "5000:5000"
    environment: 
      - REACT_APP_BACKEND_URL=http://localhost:8080
    command: ["serve", "-s", "-l", "5000", "build"]
    
  back_end:
    build: ./backend
    ports: 
      - "8080:8080"
    environment: 
      - REQUEST_ORIGIN=http://localhost:5000
 ```

### 2.4

```sh
version: "3.5"

services:
  front-end:
    build: ./frontend
    ports:
      - 5000:5000

    command: ["serve", "-s", "-l", "5000", "build"]
    
  backend-app:
    build: ./backend
    environment: 
      - REDIS_HOST=redis
    ports:
      - 8080:8080

  redis:
    image: redis
    
```

### 2.5

```sh
docker compose up -d --scale compute=10
```

### 2.6

```sh
version: "3.5"

services:
  front-end:
    build: ./frontend
    ports:
      - 5000:5000

    command: ["serve", "-s", "-l", "5000", "build"]
    
  backend-app:
    build: ./backend
    environment: 
      - REDIS_HOST=redis
      - POSTGRES_PASSWORD=postgres1
      - POSTGRES_HOST=db
    ports:
      - 8080:8080

  redis:
    image: redis

  db:
    image: postgres:13.2-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=postgres1
    container_name: db

```

### 2.7.

```sh
version: "3.5"

services:
  backend:
    build: ./backend
    volumes:
      - model:/src/model
    depends_on: 
      - training
  front-end:
    build: ./frontend
    ports:
      - 3000:3000
  training:
    build: ./training
    volumes:
      - model:/src/model
      - ./images:/src/imgs
volumes: 
  model:
```
I had some hard times with this one. It just didn't work on my machine - either did the model solution (the difference was to point the build commands to git repos). Anyway this was my solution.

### 2.8

```sh
version: "3.5"

services:
  nginx:
    image: nginx:latest
    container_name: nginx_proxy
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - 80:80

  front-end:
    build: ./frontend
    ports:
      - 5000:5000
    command: ["serve", "-s", "-l", "5000", "build"]
    
  backend-app:
    build: ./backend
    environment: 
      - REDIS_HOST=redis
      - POSTGRES_PASSWORD=postgres1
      - POSTGRES_HOST=db
    ports:
      - 8080:8080

  redis:
    image: redis

  db:
    image: postgres:13.2-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=postgres1
    container_name: db
  ```
  
nginx.conf:
```sh
events { worker_connections 1024; }

  http {
    server {
      listen 80;

      location / {
        proxy_pass http://front-end:5000;
      }

      location /api/ {
        proxy_set_header Host $host;
        proxy_pass http://backend-app:8080/;
      }
    }
  }
```

### 2.9

```sh
version: "3.5"

services:
  nginx:
    image: nginx:latest
    container_name: nginx_proxy
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - 80:80

  front-end:
    build: ./frontend
    ports:
      - 5000:5000
    command: ["serve", "-s", "-l", "5000", "build"]
    
  backend-app:
    build: ./backend
    environment: 
      - REDIS_HOST=redis
      - POSTGRES_PASSWORD=postgres1
      - POSTGRES_HOST=db
      - REQUEST_ORIGIN=http://localhost
    ports:
      - 8080:8080

  redis:
    image: redis

  db:
    image: postgres:13.2-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=postgres1
    volumes:
      - ./database:/var/lib/postgresql/data
    container_name: db
 ```
 
 ### 2.10
 
 All buttons work after I just fixed the cors error  after adding nginx (REQUEST_ORIGIN=http://localhost).

#### nginx.confg:
```sh
events { worker_connections 1024; }

  http {
    server {
      listen 80;

      location / {
        proxy_pass http://front-end:5000;
      }

      location /api/ {
        proxy_set_header Host $host;
        proxy_pass http://backend-app:8080/;
      }
    }
  }
 ```
  
#### docker-compose.yml:

```sh
version: "3.5"

services:
  nginx:
    image: nginx:latest
    container_name: nginx_proxy
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - 80:80

  front-end:
    build: ./frontend
    ports:
      - 5000:5000
    command: ["serve", "-s", "-l", "5000", "build"]
    
  backend-app:
    build: ./backend
    environment: 
      - REDIS_HOST=redis
      - POSTGRES_PASSWORD=postgres1
      - POSTGRES_HOST=db
      - REQUEST_ORIGIN=http://localhost
    ports:
      - 8080:8080

  redis:
    image: redis

  db:
    image: postgres:13.2-alpine
    restart: unless-stopped
    environment:
      - POSTGRES_PASSWORD=postgres1
    volumes:
      - ./database:/var/lib/postgresql/data
    container_name: db
```

### 2.11

I created a simple project for developing web components ( by using [open-wc community's](https://open-wc.org/guides/) scaffolding tool). 
The repo with Dockerfile and docker-compose file is [here](https://github.com/J0ne/web-components-dev).
The docker-compose.yml runs ```npm run storybook``` by default. 

docker-compose.yml:

```sh
version: '3.7'

services:
  awesome-dev:
    build: . # Build with the Dockerfile here
    command: npm run storybook # Run npm storybook as the command
    ports: 
      - 8001:8000 # storybook runs at 8000, publish that one
    volumes:
      - ./:/usr/src/app # Let us modify the contents of the container locally
      - node_modules:/usr/src/app/node_modules # A bit of node magic, this ensures the dependencies built for the image are not available locally.
    container_name: awesome-dev # Container name for convenience

volumes: # This is required for the node_modules named volume
  node_modules:
```
