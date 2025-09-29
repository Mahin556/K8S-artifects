# Day 2/40 - How To Dockerize a Project - CKA Full Course 2024 ☸️


## Check out the video below for Day2 👇

[![Day 2/40 - How To Dockerize a Project - CKA Full Course 2024](https://img.youtube.com/vi/nfRsPiRGx74/sddefault.jpg)](https://youtu.be/nfRsPiRGx74)

## If you would like to use docker and Kubernetes sandbox environment , you can use below:
```
https://labs.play-with-docker.com/
https://labs.play-with-k8s.com/
```

## Download Docker desktop client
```
https://www.docker.com/products/docker-desktop/
```
Image section --> Local, Hub, Artifectory(jfrog, image, tar, npm etc)

## Get started
- Clone a sample git repository using the below command or use your project for the demo:

```
git clone https://github.com/docker/getting-started-app.git
```

- cd into the directory
```
cd getting-started-app/
```
- Create an empty file with the name Dockerfile(default)
```
touch Dockerfile
```
- Using the text editor of your choice, paste the below content ( Details about each of these have already shared in the video)
```
FROM node:18-alpine
WORKDIR /app
COPY . .
RUN yarn install --production
EXPOSE 3000
CMD ["node", "src/index.js"]
```

- Build the docker image using the application code and Dockerfile

```
docker build -t day02-todo . #can use -f if file have different name then Dockerfile
```
- Verify the image has been created and stored locally using the below command:
```
docker images
```

- Create a public repository on hub.docker.com and push the image to remote repo
```
docker login
docker tag day02-todo:latest username/new-reponame:tagname
docker images
docker push username/new-reponame:tagname
```
  Dockerhub store image in compressed form

- To pull the image to another environment , you can use below command
```
docker pull username/new-reponame:tagname
```

- To start the docker container, use below command

```
docker run -dp 3000:3000 username/new-reponame:tagname
```

- Verify your app. If you have followed the above steps correctly, your app should be listening on localhost:3000
- To enter(exec) into the container, use the below command

```
docker exec -it containername sh
or
docker exec -it containerid sh
```
- To view docker logs

```
docker logs containername
or
docker logs containerid
```

```bash
1. **Use a Lightweight Base Image**: 
   - Example: `FROM alpine:latest`

2. **Multi-Stage Builds**: 
   - Build in one stage, copy artifacts to a clean stage.

3. **Minimize Layers**: 
   - Combine commands into a single `RUN`.
   - Example: 
     ```Dockerfile
     RUN apt-get update && apt-get install -y package1 package2 && apt-get clean && rm -rf /var/lib/apt/lists/*
     ```

4. **Remove Unnecessary Files**: 
   - Delete temporary files and caches.
   
5. **Use `.dockerignore` File**: 
   - Exclude unnecessary files.
   - Example: 
     ```
     node_modules
     *.log
     ```

6. **Optimize Dependencies**: 
   - Install only necessary dependencies.

7. **Use `COPY` instead of `ADD`**: 
   - `COPY` is more predictable.
```
