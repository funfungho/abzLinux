<!-- Continues at -->

- Graphical user interfaces make easy tasks easy, while command line interfaces make difficult tasks possible
# Playground setup
- Ubuntu 16.04

    ```bash
    docker pull ubuntu:16.04
    docker run --name learn-cli -d -it ubuntu:16.04
    docker exec -it learn-cli bash

    cat /etc/lsb-release
    bash --version
    ```

    - Docker registry
        - Check https://www.daocloud.io/mirror
        - `docker info | grep -A 1 "Registry Mirrors"`
<!-- - Acrobat 屏幕取词：uncheck "Enable Enhanced Security" -->