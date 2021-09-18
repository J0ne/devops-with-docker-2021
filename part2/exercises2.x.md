### 2.1

docker-compose.yml

```
version: "3"

services:
  web_service:
    build: .
    volumes: 
      - ./text.log:/usr/src/app/text.log 
```
