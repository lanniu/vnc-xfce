FROM ubuntu:20.04

WORKDIR /root

COPY qt-everywhere-src-5.12.4.tar.xz qt-everywhere-src-5.12.4.tar.xz

RUN tar xf qt-everywhere-src-5.12.4.tar.xz && \
    apt-get update && apt-get install -y xz-utils libgles2-mesa-dev build-essential vim python3 && \
    ln -s /usr/bin/python3 /usr/bin/python && \
    sed -i "48i #include <linux/sockios.h>" /root/qt/qtserialbus/src/plugins/canbus/socketcan/socketcanbackend.cpp && \
    tar xf qt-everywhere-src-5.12.4.tar.xz && \
    cd ./qt-everywhere-src-5.12.4 && \
    ./configure -prefix /root/qt -skip qtvirtualkeyboard -skip qtwebengine -opengl es2 && \
    make -j4 && \
    make install && \
    rm -rf /root/qt-everywhere-src-5.12.4