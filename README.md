## Installation guide

Steps mostly taken from here:

https://www.jenkins.io/doc/book/installing/docker/

The idea is to have jenkins run inside a DinD container (Docker in Docker)

Step 1:

Create a jenkins bridge

`docker network create jenkins`

Step II:

Run the DIND container via docker run:

```
docker run \
  --name jenkins-docker \
  --rm \
  --detach \
  --privileged \
  --network jenkins \
  --network-alias docker \
  --env DOCKER_TLS_CERTDIR=/certs \
  --volume jenkins-docker-certs:/certs/client \
  --volume jenkins-data:/var/jenkins_home \
  --publish 2376:2376 \
  docker:dind \
  --storage-driver overlay
```

Step III:

Run docker build:

`docker build -t jenkins-test:latest .`

Step IV:

Use previous image to run Jenkins:

```
docker run \
  --name jenkins-test \
  --rm \
  --detach \
  --network jenkins \
  --env DOCKER_HOST=tcp://docker:2376 \
  --env DOCKER_CERT_PATH=/certs/client \
  --env DOCKER_TLS_VERIFY=1 \
  --publish 8080:8080 \
  --publish 50000:50000 \
  --volume jenkins-data:/var/jenkins_home \
  --volume jenkins-docker-certs:/certs/client:ro \
  jenkins-test:latest
```