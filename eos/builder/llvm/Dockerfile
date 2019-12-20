FROM weigun/clang8:last

WORKDIR ${TOOLS}

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
