# How to Dockerize a MERN Application 

### Clone this repository

`git clone https://github.com/jocasantos/docker-mern-app-with-and-without-docker-compose.git`

### Install Docker in your machine

https://docs.docker.com/engine/install/ubuntu/ - For instructions on how to install Docker on Ubuntu

### Create the front-end Dockerfile

```sh
cd mern/frontend
vim Dockerfile
```

```Dockerfile
FROM node:latest 

# You should define a version instead of using latest

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 5173

CMD ["npm", "run", "dev"]
```

### Create a network for the docker containers

`docker network create mern`

### Build the client 

```sh
cd mern/frontend
docker build -t mern-frontend .
```

### Run the client

`docker run --name=frontend --network=mern -d -p 5173:5173 mern-frontend`

### Verify the client is running

Open your browser and type `http://localhost:5173`

### Run the mongodb container

`docker run --network=mern --name mongodb -d -p 27017:27017 -v ~/opt/data:/data/db mongo:latest`

> It is best practice to use a specific version of the image instead of latest

### Create the backend Dockerfile

```sh
cd ../backend
vim Dockerfile
```

```Dockerfile
FROM node:latest

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 5050

CMD ["npm", "start"]
```

### Build the server    

```sh
docker build -t mern-backend .
```

### Run the server

`docker run --name=backend --network=mern -d -p 5050:5050 mern-backend`

> Congratulations! You have successfully dockerized a MERN application :tada:

## Using Docker Compose

### Remove the containers you created

```sh
docker stop frontend backend mongodb
docker rm frontend backend mongodb
```

### Create the docker-compose.yml file

```sh
cd ..
vim docker-compose.yml
```

```yml
services:
  backend:
    build: ./mern/backend
    ports:
      - "5050:5050"
    networks:
      - mern
    depends_on:
      - mongodb

  frontend:
    build: ./mern/frontend
    ports:
      - "5173:5173"
    networks:
      - mern
    
  mongodb:
    image: mongo:latest
    ports:
      - "27017:27017"
    networks:
      - mern
    volumes:
      - mongo-data:/data/db 
      
networks:
  mern:
    driver: bridge

volumes:
  mongo-data:
    driver: local  # Persist MongoDB data locally    
```

### Run the application

`docker compose up -d`

> Congratulations! You have successfully dockerized a MERN application using Docker Compose :tada::tada:
