# 4.可能的问题

1. samtools用conda刚安好是可用的，但是装了具他一些软件后就有可能报错

先用samtools --help #测试是否安装成功，如果使用时报错，需要建立一个软连接，如下所示

(1) 找到libcrypto.so.1.1.1，一般在：

    ```sh
    ~/anaconda/lib/libcrypto.so.1.1
    ```

(2) 建立软连接

    ```sh
    #进到这个文件夹
    cd ~/anaconda/lib/
    #建立软连接（类似Hn的快挂方式）
    ln -s libcrypto.so.1.1 libcrypto.so.1.0.0
    ```

