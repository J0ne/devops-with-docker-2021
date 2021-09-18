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
