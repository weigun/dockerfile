FROM ubuntu:18.04

ENV TOOLS=/root/deps
RUN mkdir -p ${TOOLS}
WORKDIR ${TOOLS}

# install dependencies.
RUN apt-get update && \
    apt-get upgrade -y && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y git make \
    bzip2 automake libbz2-dev libssl-dev doxygen graphviz libgmp3-dev \
    autotools-dev libicu-dev python2.7 python2.7-dev python3 python3-dev \
    autoconf libtool g++ gcc curl zlib1g-dev sudo ruby libusb-1.0-0-dev \
    libcurl4-gnutls-dev pkg-config patch ccache vim-common jq

# build cmake.
RUN curl -LO https://cmake.org/files/v3.13/cmake-3.13.2-Linux-x86_64.tar.gz && \
    tar -xzf cmake-3.13.2-Linux-x86_64.tar.gz && \ 
    ln -sf ${TOOLS}/cmake-3.13.2-Linux-x86_64/bin/* /usr/bin/ && \
    export PATH=${TOOLS}:$PATH && \
    rm -f cmake-3.13.2-Linux-x86_64.tar.gz

# RUN curl -LO https://cmake.org/files/v3.13/cmake-3.13.2.tar.gz && \
#     tar -xzf cmake-3.13.2.tar.gz && \
#     cd cmake-3.13.2 && \
#     ./bootstrap --prefix=/usr/local && \
#     make -j$(nproc) && \
#     make install && \
#     cd / && \
#     rm -rf cmake-3.13.2.tar.gz /cmake-3.13.2

# build clang8
RUN git clone --single-branch --branch release_80 https://git.llvm.org/git/llvm.git clang8 && cd clang8 && git checkout 18e41dc && \
    cd tools && git clone --single-branch --branch release_80 https://git.llvm.org/git/lld.git && cd lld && git checkout d60a035 && \
    cd ../ && git clone --single-branch --branch release_80 https://git.llvm.org/git/polly.git && cd polly && git checkout 1bc06e5 && \
    cd ../ && git clone --single-branch --branch release_80 https://git.llvm.org/git/clang.git clang && cd clang && git checkout a03da8b && \
    cd tools && mkdir extra && cd extra && git clone --single-branch --branch release_80 https://git.llvm.org/git/clang-tools-extra.git && cd clang-tools-extra && git checkout 6b34834 && \
    cd ${TOOLS}/clang8/projects && git clone --single-branch --branch release_80 https://git.llvm.org/git/libcxx.git && cd libcxx && git checkout 1853712 && \
    cd ../ && git clone --single-branch --branch release_80 https://git.llvm.org/git/libcxxabi.git && cd libcxxabi && git checkout d7338a4 && \
    cd ../ && git clone --single-branch --branch release_80 https://git.llvm.org/git/libunwind.git && cd libunwind && git checkout 57f6739 && \
    cd ../ && git clone --single-branch --branch release_80 https://git.llvm.org/git/compiler-rt.git && cd compiler-rt && git checkout 5bc7979 && \
    mkdir ${TOOLS}/clang && mkdir ${TOOLS}/clang8/build && cd ${TOOLS}/clang8/build && \
    cmake -G 'Unix Makefiles' -DCMAKE_INSTALL_PREFIX="${TOOLS}/clang" -DLLVM_BUILD_EXTERNAL_COMPILER_RT=ON -DLLVM_BUILD_LLVM_DYLIB=ON -DLLVM_ENABLE_LIBCXX=ON -DLLVM_ENABLE_RTTI=ON -DLLVM_INCLUDE_DOCS=OFF -DLLVM_OPTIMIZED_TABLEGEN=ON -DLLVM_TARGETS_TO_BUILD=X86 -DCMAKE_BUILD_TYPE=Release .. && \
    make -j $(nproc) && \
    make install && \
    export PATH=${TOOLS}/clang:$PATH && \
    cd ${TOOLS} && \
    rm -rf clang8
#COPY ./.cicd/helpers/clang.make /tmp/clang.cmake

ENV CC ${TOOLS}/clang/bin/clang
ENV CXX ${TOOLS}/clang/bin/clang++

# build llvm
RUN git clone --depth 1 --single-branch --branch release_40 https://github.com/llvm-mirror/llvm.git llvm && \
    cd llvm && \
    mkdir build && \
    cd build && \
    mkdir -p ${TOOLS}/llvm4 && \
    cmake -G 'Unix Makefiles' -DLLVM_TARGETS_TO_BUILD=host -DLLVM_BUILD_TOOLS=false -DLLVM_ENABLE_RTTI=1 -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="${TOOLS}/llvm4" .. && \
    make -j$(nproc) && \
    make install && \
    export PATH=${TOOLS}/llvm4:$PATH && \
    cd ${TOOLS} && \
    rm -rf llvm
    # ln -s /usr/lib/llvm-4.0/lib/LLVMgold.so /root/install/lib/LLVMgold.so
    
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
# build mongodb
RUN curl -LO http://downloads.mongodb.org/linux/mongodb-linux-x86_64-ubuntu1804-4.1.1.tgz && \
    tar -xzf mongodb-linux-x86_64-ubuntu1804-4.1.1.tgz && \
    rm -f mongodb-linux-x86_64-ubuntu1804-4.1.1.tgz && \
    export PATH=${PATH}:${TOOLS}/mongodb-linux-x86_64-ubuntu1804-4.1.1/bin
# build mongodb c driver
RUN curl -LO https://github.com/mongodb/mongo-c-driver/releases/download/1.13.0/mongo-c-driver-1.13.0.tar.gz && \
    tar -xzf mongo-c-driver-1.13.0.tar.gz && \
    cd mongo-c-driver-1.13.0 && \
    mkdir -p ${TOOLS}/mongo-cdriver && \
    mkdir -p build && \
    cd build && \
    cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="${TOOLS}/mongo-cdriver" -DENABLE_BSON=ON -DENABLE_SSL=OPENSSL -DENABLE_AUTOMATIC_INIT_AND_CLEANUP=OFF -DENABLE_STATIC=ON .. && \
    make -j$(nproc) && \
    make install && \
    export PATH=${TOOLS}/mongo-cdriver:$PATH && \
    cd ${TOOLS} && \
    rm -rf mongo-c-driver-1.13.0.tar.gz mongo-c-driver-1.13.0

# build mongodb cxx driver
RUN curl -L https://github.com/mongodb/mongo-cxx-driver/archive/r3.4.0.tar.gz -o mongo-cxx-driver-r3.4.0.tar.gz && \
    tar -xzf mongo-cxx-driver-r3.4.0.tar.gz && \
    cd mongo-cxx-driver-r3.4.0 && \
    sed -i 's/\"maxAwaitTimeMS\", count/\"maxAwaitTimeMS\", static_cast<int64_t>(count)/' src/mongocxx/options/change_stream.cpp && \
    sed -i 's/add_subdirectory(test)//' src/mongocxx/CMakeLists.txt src/bsoncxx/CMakeLists.txt && \
    mkdir -p build && \
    cd build && \
    mkdir -p ${TOOLS}/mongo-cxxdriver && \
    cmake -DBUILD_SHARED_LIBS=OFF -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX="${TOOLS}/mongo-cxxdriver" .. && \
    make -j$(nproc) && \
    make install && \
    export PATH=${TOOLS}/mongo-cxxdriver:$PATH && \
    cd ${TOOLS} && \
    rm -rf mongo-cxx-driver-r3.4.0.tar.gz mongo-cxx-driver-r3.4.0
