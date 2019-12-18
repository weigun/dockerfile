FROM eosio/ci:eos-ubuntu-18.04-pinned-4bad53eca23c04cae89231987744f6d6aab8c399
ENV VERSION 1

# 删除无用的文件和目录
RUN cd /root/eosio/build && \
    make clean && \
    make uninstall && \
    sudo apt-get remove -y nodejs && \
    sudo apt-get autoclean && \
    sudo apt-get autoremove
