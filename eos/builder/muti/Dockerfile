FROM weigun/bitbuilder:clang8 AS clang8

RUN echo $PATH


# Second stage: minimal runtime environment
FROM weigun/bitbuilder:llvm AS llvm

RUN echo $PATH

FROM weigun/bitbuilder:boost AS boost

RUN echo $PATH

FROM weigun/bitbuilder:driver AS driver

RUN echo $PATH

FROM weigun/bitbuilder:baseEnv as result
COPY --from=clang8 ${TOOLS}/clang ${TOOLS}/clang
COPY --from=llvm ${TOOLS}/llvm4 ${TOOLS}/llvm4
COPY --from=boost ${TOOLS}/boost ${TOOLS}/boost
COPY --from=driver ${TOOLS}/mongodb-linux-x86_64-ubuntu1804-4.1.1 ${TOOLS}/mongodb-linux-x86_64-ubuntu1804-4.1.1
COPY --from=driver ${TOOLS}/mongo-cdriver ${TOOLS}/mongo-cdriver
COPY --from=driver ${TOOLS}/mongo-cxxdriver ${TOOLS}/mongo-cxxdriver

RUN echo $PATH