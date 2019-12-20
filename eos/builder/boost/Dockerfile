FROM weigun/baseEnv:last

WORKDIR ${TOOLS}
    
# build boost
RUN curl -LO https://dl.bintray.com/boostorg/release/1.70.0/source/boost_1_70_0.tar.bz2 && \
    tar -xjf boost_1_70_0.tar.bz2 && \
    cd boost_1_70_0 && \
    mkdir -p ${TOOLS}/boost && \
    ./bootstrap.sh --with-toolset=clang --prefix="${TOOLS}/boost" && \
    ./b2 toolset=clang cxxflags='-stdlib=libc++ -D__STRICT_ANSI__ -nostdinc++ -I/usr/local/include/c++/v1' linkflags='-stdlib=libc++' link=static threading=multi --with-iostreams --with-date_time --with-filesystem --with-system --with-program_options --with-chrono --with-test -q -j$(nproc) install && \
    export PATH=${TOOLS}/boost:$PATH && \
    cd ${TOOLS} && \
    rm -rf boost_1_70_0.tar.bz2 /boost_1_70_0 