![alt docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)

# Docker Notes

Owner: Khant Naing Set
Tags: CloudNative, Container

# Installation of Docker

## Window

- In window installation is pretty straight forward. **Just download the installer from official site and installed.**

## Linux

- Although there are too many Linux distribution and docker support almost all of them, it’s not ideal to work with some Linux distros. **Docker work best with Ubuntu or Debian**
- Installing docker in **Ubuntu or Debian:**

```yaml
sudo apt update

sudo apt install apt-transport-https curl gnupg-agent ca-certificates software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

sudo apt install docker-ce docker-ce-cli containerd.io -y

```

# CLI References

- **Build an image:**

```yaml
# Sample cmd
docker build -t <image-name>:<tags> <Dockerfile>

# Sample usage

# Default build
docker build -t web-app:lastest .

# Build with specific dockerfile
docker build -t web-app:latest -f <specific-Dockefile> .
```

- **List an available images:**

```yaml
docker images

#or

docker image ls
```

- **Delete an image:**

```yaml
docker image rm <image-name>

# force delete
docker image rm <image-name> --force

#untagged delete
docker image rm <image-name> --no-prune

```

- **Run docker image:**

```yaml
docker run -p <host-port>:<image-port> -d <imagename:tags>

# Run with enviroment variables

docker run -p <host-port>:<image-port> -d \
-e PORT=3000 \
<imagename:tags>

# Run with enviroment variables file
docker run -p <host-port>:<image-port> -d  \ 
--env-file <file-location> \
<imagename:tags>
```

- **List running docker images:**

```yaml
docker ps

# full stats view
docker stats
```

- **Stop a running image:**

```yaml
docker stop <container-id>
```

- **Output logs of image:**

```yaml
docker logs <container-id>
```

- **Show modified file inside container:**

```yaml
docker diff <container-id>
```

- **login and logout into repo:**

```yaml
docker login

docker logout
```

- **Pull or Push images:**

```yaml
docker pull <image-name>:<tag>

docker push <image-name>:<tag>
```

# **Dockerfile References**

| Command | Description |
| --- | --- |
| FROM <image>| scratch | The base image for the build |
| MAINTAINER <email> | Metadata of maintainer of the image: in this case Email |
| COPY <path> <dst> | Copy the file to inside container location |
| RUN <args> | run an arbitrary command inside container. |
| WORKDIR <path> | Set the work directory inside container |
| CMD <args> | Set the default command while running image, default entry point |
| ENV <name>=<value> | Set the environmental values inside container |
| ENTRYPOINT <entry-point-location> | Set the custom entry point for image |

# Docker Best Practices

- Use minimal image like `alpine` in production for less space and footprint
- Do not expose secrets inside `Dockerfike`, better to use with external service like **Vault**
- Use ENV to define environment variables, but better to define at runtime rather than in `Dockerfile`
- Use multi-stage build if possible
    - **Sample multi-stage docker image:**
    
    ```docker
    # 1. Build the app using build env
    FROM node:latest AS build-env
    WORKDIR /usr/src/app
    COPY . /usr/src/app/
    RUN npm install --only=production
    RUN npm run build
    
    # 1. Build the app using minimal alpine env
    FROM node:lts-alpine
    ENV NODE_ENV production
    
    WORKDIR /usr/src/app
    RUN  npm install pm2 -g
    
    USER node
    COPY --chown=node:node --from=build-env /usr/src/app/ /usr/src/app/
    COPY --chown=node:node --from=build-env /usr/src/app/.env /usr/src/app/build/
    EXPOSE 3333
    CMD ["pm2-runtime", "start", "ecosystem.config.js"]
    ```
    
- Use `entrypoint` rather than `CMD` as `entrypoint`
- Use tags while building an image
- Separate the build script if possible
- Keep `Dockefile` clean and commented
- Use `.dockerignore` for unnecessary files
- **Use the Least Privileged User, avoid run as root:**

```docker
USER node
COPY --chown=node:node --from=build-env /usr/src/app/ /usr/src/app/
COPY --chown=node:node --from=build-env /usr/src/app/.env /usr/src/app/build/
EXPOSE 3333
CMD ["pm2-runtime", "start", "ecosystem.config.js"]
```

- Scan your Images for Security Vulnerabilities by using `docker scan`
- **Use caching for more speed**
