# Docker play by play

Play by Play: Docker for Java Developers
https://app.pluralsight.com/course-player?clipId=39d14f7b-f5cf-43d1-a078-bc9d62539c5c

Arun Gupta
Michael Hoffman

Basic and advanced concepts for docker from java dev
 - docker compose, docker fundamentals, docker swarm
 - package entire app once to run anywhere
 - docker at high level
 - basic and advanced for Java
Arun Gupta - Docker captain
 - Java champion + Docker expert
What is Docker
 - open source
 - started in France
 - Write once and Run any where WORA - Java - JVM understands (jar, war, ear)
 - PODA - package once deploy anywhere
   - image on linux run on linux
 - Java - environment locally - JVM environment tomcat on windows + deploy to linux
   - docker reduce impedence mismatch for different environments

Docker Engine understands docker image
 - Docker C groups and namespaces -> control to run application
 - app -> docker engine -> operating system (kernel) -> infrastructure
 - Allows higher density and throughput -> starts up faster
 - 100s of docker containers versus VM which eats up memory
3 pieces of docker
 - client
   - docker build
   - docker pull
   - docker run
 - docker host (docker daemon)
   - containers
   - images
 - registry
   - SaaS people push image
   - similar to maven for common libraries -> maven central for all docker repos
   - ubuntu, centos, couchbase, nginx
   - people created multipurpose images -> ubuntu-java - dependency on ubuntu
     - easy from user perspective download all dependencies
client talk to docker host and run image - client is dumb client
Docker host has rest endpoint to list to client - requests via tcp
client is dumb - all state controlled by host itself

What do we mean by image?
https://docs.docker.com/engine/reference/builder/



Simple Dockerfile in hello/Dockerfile - commands:
```bash
sudo docker build -t "hello:Dockerfile" .
[+] Building 6.1s (6/6) FINISHED                                                                                                                                                                            
 => [internal] load .dockerignore                                                                                                                                                                      0.1s
 => => transferring context: 2B                                                                                                                                                                        0.0s
 => [internal] load build definition from Dockerfile                                                                                                                                                   0.1s
 => => transferring dockerfile: 72B                                                                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/ubuntu:latest                                                                                                                                       1.4s
 => [auth] library/ubuntu:pull token for registry-1.docker.io                                                                                                                                          0.0s
 => [1/1] FROM docker.io/library/ubuntu@sha256:2adf22367284330af9f832ffefb717c78239f6251d9d0f58de50b86229ed1427                                                                                        4.6s
 => => resolve docker.io/library/ubuntu@sha256:2adf22367284330af9f832ffefb717c78239f6251d9d0f58de50b86229ed1427                                                                                        0.0s
 => => sha256:74f2314a03de34a0a2d552b805411fc9553a02ea71c1291b815b2f645f565683 2.30kB / 2.30kB                                                                                                         0.0s
 => => sha256:76769433fd8a87dd77a6ce33db12156b1ea8dad3da3a95e7c9c36a47ec17b24c 29.53MB / 29.53MB                                                                                                       3.6s
 => => sha256:2adf22367284330af9f832ffefb717c78239f6251d9d0f58de50b86229ed1427 1.13kB / 1.13kB                                                                                                         0.0s
 => => sha256:b2175cd4cfdd5cdb1740b0e6ec6bbb4ea4892801c0ad5101a81f694152b6c559 424B / 424B                                                                                                             0.0s
 => => extracting sha256:76769433fd8a87dd77a6ce33db12156b1ea8dad3da3a95e7c9c36a47ec17b24c                                                                                                              0.6s
 => exporting to image                                                                                                                                                                                 0.0s
 => => exporting layers                                                                                                                                                                                0.0s
 => => writing image sha256:53000a0ffd678ecc5bf03f882e30aa6980164e3a6512230192c0ee79b1a87c7d                                                                                                           0.0s
 => => naming to docker.io/library/hello:Dockerfile                                                                                                                                                    0.0s
tom@tom-ubuntu:~/Desktop/play-by-play$ sudo docker images
REPOSITORY   TAG          IMAGE ID       CREATED       SIZE
hello        Dockerfile   53000a0ffd67   6 days ago    77.8MB
mysql        latest       57da161f45ac   3 weeks ago   517MB
tom@tom-ubuntu:~/Desktop/play-by-play$ docker run hello
docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post "http://%2Fvar%2Frun%2Fdocker.sock/v1.24/containers/create": dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.
tom@tom-ubuntu:~/Desktop/play-by-play$ sudo docker run hello
Unable to find image 'hello:latest' locally
docker: Error response from daemon: pull access denied for hello, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
tom@tom-ubuntu:~/Desktop/play-by-play$ sudo docker run 5300
Hello world

```

## Docker java file inside hello-java

```bash

tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ java -version
openjdk version "11.0.18" 2023-01-17
OpenJDK Runtime Environment (build 11.0.18+10-post-Ubuntu-0ubuntu122.10)
OpenJDK 64-Bit Server VM (build 11.0.18+10-post-Ubuntu-0ubuntu122.10, mixed mode, sharing)
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ javac Hello.java
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ ls
Hello.class  Hello.java
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ jar cvf hello.jar Hello.class
added manifest
adding: Hello.class(in = 416) (out= 285)(deflated 31%)
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ jar tf hello.jar
META-INF/
META-INF/MANIFEST.MF
Hello.class
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ jar xvf hello.jar
  created: META-INF/
 inflated: META-INF/MANIFEST.MF
 inflated: Hello.class

```

Run java dockerfile:

```bash

tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ sudo docker build -t "hello-java:Dockerfile" .
[+] Building 12.2s (9/9) FINISHED                                                                    
 => [internal] load build definition from Dockerfile                                            0.0s
 => => transferring dockerfile: 193B                                                            0.0s
 => [internal] load .dockerignore                                                               0.0s
 => => transferring context: 2B                                                                 0.0s
 => [internal] load metadata for docker.io/library/alpine:latest                                1.5s
 => [auth] library/alpine:pull token for registry-1.docker.io                                   0.0s
 => [internal] load build context                                                               0.0s
 => => transferring context: 456B                                                               0.0s
 => [1/3] FROM docker.io/library/alpine:latest@sha256:69665d02cb32192e52e07644d76bc6f25abeb541  0.1s
 => => resolve docker.io/library/alpine:latest@sha256:69665d02cb32192e52e07644d76bc6f25abeb541  0.1s
 => => sha256:69665d02cb32192e52e07644d76bc6f25abeb5410edc1c7a81a10ba3f0efb90a 1.64kB / 1.64kB  0.0s
 => => sha256:e2e16842c9b54d985bf1ef9242a313f36b856181f188de21313820e177002501 528B / 528B      0.0s
 => => sha256:b2aa39c304c27b96c1fef0c06bee651ac9241d49c4fe34381cab8453f9a89c7d 1.47kB / 1.47kB  0.0s
 => [2/3] ADD Hello.class Hello.class                                                           0.1s
 => [3/3] RUN apk --update add openjdk11-jre                                                    9.5s
 => exporting to image                                                                          0.9s
 => => exporting layers                                                                         0.9s
 => => writing image sha256:61add0199393d6e12fb3c9bb50dc13098a3acd73c7188b65f91fa56aa1866671    0.0s 
 => => naming to docker.io/library/hello-java:Dockerfile                                        0.0s 
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ sudo docker images
REPOSITORY   TAG          IMAGE ID       CREATED          SIZE                                       
hello-java   Dockerfile   61add0199393   10 seconds ago   184MB
mysql        latest       57da161f45ac   3 weeks ago      517MB
tom@tom-ubuntu:~/Desktop/play-by-play/hello-java$ sudo docker run 61add
Hello world!

```
