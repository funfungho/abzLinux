- error while loading shared libraries: libevent-2.1.so.6: cannot open shared object file: No such file or directory
    - http://www.voidcn.com/article/p-ghgzqvrp-bqo.html
    - https://blog.csdn.net/yjk13703623757/article/details/53217377
- des.h

    ```bash
    gcc -xc -E -v -     # C
    gcc -xc++ -E -v -   # C++
    # https://stackoverflow.com/questions/4980819/what-are-the-gcc-default-include-directories


    cd /usr/local/include 
    ln -s ../opt/openssl/include/openssl .
    # https://stackoverflow.com/questions/42044221/openssl-not-found-on-macos-sierra/45620659#45620659
    ```