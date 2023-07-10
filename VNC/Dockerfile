FROM ubuntu:20.04

ENV DISPLAY=:1 \
    VNC_PORT=5901 \
    NO_VNC_PORT=6901

EXPOSE $VNC_PORT $NO_VNC_PORT

### Change apt source
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list

### Change timezone
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

### Envrioment config
ENV HOME=/root \
    TERM=xterm \
    STARTUPDIR=/dockerstartup \
    INST_SCRIPTS=/root/install \
    NO_VNC_HOME=/root/noVNC \
    DEBIAN_FRONTEND=noninteractive \
    VNC_COL_DEPTH=24 \
    VNC_RESOLUTION=1920x1080 \
    VNC_PW=SYhr_5000 \
    VNC_VIEW_ONLY=false

WORKDIR $HOME

### Install common tools
RUN apt-get update && \
    apt-get install -y vim wget net-tools locales bzip2 procps apt-utils python3-numpy && \
    apt-get clean -y && \
    echo "zh_CN.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen

ENV LANG='zh_CN.UTF-8' LANGUAGE='zh_CN:en' LC_ALL='zh_CN.UTF-8'

### Install custom fonts
RUN apt-get install -y ttf-wqy-zenhei && \
    apt-get clean -y

### Install xfce
RUN apt-get update && \
    apt-get install -y supervisor xfce4 xfce4-terminal xterm dbus-x11 libdbus-glib-1-2 && \
    apt-get purge -y pm-utils *screensaver* && \
    apt-get clean -y

### Install vnc and noVNC
RUN apt-get install -y tigervnc-standalone-server && \
    apt-get clean -y && \
    printf '\n# iasp-vnc-container:\n$localhost = "no";\n1;\n' >>/etc/tigervnc/vncserver-config-defaults

RUN mkdir -p $NO_VNC_HOME/utils/websockify 

COPY content/noVNC-1.3.0.tar.gz $NO_VNC_HOME

COPY content/websockify-0.10.0.tar.gz $NO_VNC_HOME/utils/websockify

RUN tar xf $NO_VNC_HOME/noVNC-1.3.0.tar.gz --strip-components=1 -C $NO_VNC_HOME && \
    tar xf $NO_VNC_HOME/utils/websockify/websockify-0.10.0.tar.gz --strip-components=1 -C $NO_VNC_HOME/utils/websockify && \
    rm $NO_VNC_HOME/noVNC-1.3.0.tar.gz && \
    rm $NO_VNC_HOME/utils/websockify/websockify-0.10.0.tar.gz && \
    ln -s $NO_VNC_HOME/vnc.html $NO_VNC_HOME/index.html

### Install ssh
RUN apt-get update && \
    apt-get install -y openssh-server && \
    apt-get clean -y && \
    sed -i "35i PermitRootLogin yes" /etc/ssh/sshd_config

### Configure startup
ADD content/xfce/ $HOME/
ADD content/set_user_permission.sh $HOME
ADD content/scripts $STARTUPDIR
RUN $HOME/set_user_permission.sh $STARTUPDIR $HOME

RUN echo 'root:SYhr_5000' | chpasswd

ENTRYPOINT ["/dockerstartup/vnc_startup.sh"]
CMD ["--wait"]
