**Docker Tutorial**
-

Containers
--
1) What is a container? 
    - A way to package application with all the necessary dependencies and configurations
    - This package is easily shared and moved around (Protable Artifact) 
    - Makes development and deployment easy
    - (Technically) Layers of images usually with a linux base image and an application image on top with optional layers in between with configurations, etc..

2) Where do containers live?
    - Container Repository
        - Private Repo (Used and maintained by organisations)
        - Public Repo (dockerhub)

3) Why do we need containers?
    - The installation process is different for each OS environment and there can be many installation steps increasing the risk of error
    - Containers is an isolated OS therefore getting rid of all the risks as it packages all the needed configurations and allows installation with one command

Image vs Containers
--
1) What is an image?
    - The actual package (contains the application, configuration and dependencies)
    - aka the *artifact* that can be moved around
2) Containers 
    - When the image is pulled and started it creates the *container*
    - Running environment for image


Docker vs Virutal Machine
--
1) Both are vitualisation tools that run on the OS layers (OS Kernel and Applications)
2) Docker virtualises the Applications Layer and uses the kernel of the host
3) VMs Virtualises the both layers meaning that it does not use the host kernel but its own
4) Docker containers are therefore smaller and faster as they do not have to boot up the entire OS kernel. However Vms of any OS can run on any OS host. Docker containers are usually restricted by the kernel. (Linux Application and Windows OS) This can be circumvented by abstracting the kernel with tools such as *Docker toolbox*

Container Port vs Host Port
--
1) Multiple containers can run on the host machine but the host has only a certain amount of ports available. Conflit arises when containers are using the same prots on the host machine. 
2) Need to bind host machine port number to the container's port (e.g. okay for container A and container B both have container port 3000 but are bounded to host port 3000 and 3001 respectively)

Docker Network
--
1) Docker creates an isolated docker netwrok where the containers are running in. Containers in the network can communicate using only the container name.

Commands
- 
- `docker pull {image}` -> Download the image to local
- `docker run {image}` -> To run a new container from an image
    - `-d` -> Runs container in detached mode (runs in the background of the terminal. Does not recieve any input/output. Allows to close the terminal session without stopping the container)
    - `-p{Host Port}:{Container Port}` ->  Refer to *Container Port vs Host Port*
    - `--name {Specify name}` -> run with specific name
    - `-e {Variable Name}={Variable Value}` -> Specify env variables
- `docker images` -> Output existing images
- `docker ps` -> Output list of running containers 
    - `-a` -> shows all containser 
- `docker stop {Container ID}` -> Stops an existing container
- `docker start {Container ID}` -> Starts an existing container
- `docker logs {Container ID/ Container Name}` -> Gets the logs of the container
- `docker exec -it {Container ID/ Container name} /bin/{bash/sh}` -> Enter the container as a root user
- `docker network ls` -> View docker networks
- `docker network create {Network Name}` -> Creates a new network


Demo of local development
- 
1) Develop a simple FE and BE application that utilises MongoDB
2) Utilise a Docker container of MongoDB and MongoExpress
    - Get the MongoDB image from Docker Hub `docker pull mongo`  
    - Get the Mongo-Express image `docker pull mongo-express`
    - Create the Network to run the Mongo related containers `docker network create mongo-network`
    - Run the MongoDb Image `docker run -p 27017:27017 -d -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=password --name mongodb --net mongo-network mongo`
    - Run the Mongo-Express Image `docker run -d -p 8081:8081 -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin -e ME_CONFIG_MONGODB_ADMINPASSWORD=password --net mongo-network --name mongo-express -e ME_CONFIG_MONGODB_SERVER=mongodb mongo-express`
    - verify running via logs `docker logs mongodb` & `docker logs mongo-express`
3) Create a DB using Mongo Express `http://localhost:8081/`
    - Create a Database `user-account`
    - Create a Collection `users`
4) Start the server 
    - Ensure that the server is connecting to the right db
    - run `node server.js`


Docker Compose
-- 
Takes the whole list of docker commands with the configurations and map it to a structured file. Refer to `mongo.yaml`

Takes care of common networks for containers. 

Commands
- 
- `docker-compose -f {filename} up` -> start all containers 
- `docker-compose -f {filename} down` -> stop all containers and kill networks

Dockerfile
--
Blueprint for packaging application to docker image for deployment
- `FROM` -> First line of every docker file is `FROM {image}` as ours is a nodejs application, we can base it on the node image from dockerhub
    - this line installs the base image
- `ENV` -> Set environment variables 
- `RUN` -> execute any linux commands inside the container
    - Create directory inside the container
- `COPY` -> executes the copy command on the host (local)
- `CMD` -> executes an entry point command inside the container (For starting the server)
Refer to the `Dockerfile`

Commands
- 
-   `docker build -t {image name} {directory of docker file}` -> creates the image


Final Words
--
Push Image to a private docker repo where can be deployed by other