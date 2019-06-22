# https://docs.docker.com/engine/reference/builder/
# https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
    # https://stackoverflow.cocom/questions/25019183/docker-java7-install-fail/25020555#25020555

FROM ubuntu:16.04

RUN apt-get update -y -o Dpkg::Progress-Fancy="1" && \
    apt-get install -y -o Dpkg::Progress-Fancy="1" less vim

# docker build -t linux-cmd:16.04 .

# order matters; -it required
# docker run --name fun-cmd -it -d linux-cmd:16.04
# docker exec -it fun-cmd bash