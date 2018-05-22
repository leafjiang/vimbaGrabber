FROM ubuntu:16.04

###############################################################################
# Install packages
RUN apt-get update && apt-get install -y \
    build-essential \
    ffmpeg \
    iputils-ping \
    less \
    libavcodec-dev \
    libavdevice-dev \
    libavfilter-dev \
    libavformat-dev \
    libopencv-dev \
    libswscale-dev \
    libtinyxml-dev \
    net-tools \
    openssh-server \
    sudo \
    vim-gnome \
    wget

###############################################################################
# Install QT 4.8.4
# http://doc.qt.io/archives/qt-4.8/install-x11.html
RUN wget -qO- \
      https://download.qt.io/archive/qt/4.8/4.8.4/qt-everywhere-opensource-src-4.8.4.tar.gz | tar xvz
WORKDIR qt-everywhere-opensource-src-4.8.4
RUN ./configure -platform linux-g++ -v \
        -release \
        -static \
        -fontconfig \
        -opensource \
        -confirm-license \
        -gtkstyle \
        -no-webkit \
        -nomake demos \
        -nomake examples \
        -no-cups \
        -no-openvg \
        -no-libmng \
        -qt-libpng \
        -qt-libtiff \
        -qt-libjpeg \
        -qt-libpng \
        -no-accessibility \
        -no-phonon \
        -no-script \
        -no-declarative \
        -no-declarative-debug \
        -no-openssl \
        -no-qt3support \
        -no-scripttools \
        -no-exceptions
RUN make -j4
RUN make install
ENV PATH="/usr/local/Trolltech/Qt-4.8.4/bin:${PATH}"
ENV PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/Trolltech/Qt-4.8.4/lib/pkgconfig 

###############################################################################
# Install Vimba 2.1.3
WORKDIR /
RUN wget -qO- \
    https://www.alliedvision.com/fileadmin/content/software/software/Vimba/Vimba_v2.1.3_Linux.tgz | tar xvz
WORKDIR /Vimba_2_1/VimbaGigETL
RUN ./Install.sh
ENV GENICAM_GENTL64_PATH=:/Vimba_2_1/VimbaGigETL/CTI/x86_64bit

###############################################################################
# Install sshd
RUN mkdir /var/run/sshd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd

ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile

EXPOSE 22
#CMD ["/usr/sbin/sshd", "-D"]

###############################################################################
# Set root password
RUN echo "root:lidar" | chpasswd

# Add user so that container does not run as root 
RUN useradd -m nodar
RUN echo "nodar:lidar" | chpasswd 
RUN usermod -s /bin/bash nodar 
RUN usermod -aG sudo nodar 
ENV HOME /home/nodar

USER nodar
WORKDIR /home/nodar

###############################################################################
# Installation notes
# 1. Change directory into folder with Dockerfile, then build an image
#    bash$ docker build -t nodarvimba .
#    bash$ docker ps
#    bash$ docker images
# 2. Run docker container
#    bash$ docker run --rm  -v /Users/leaf/Documents/misc/Vimba_2_1_nodar:/home/nodar -it nodarvimba /bin/bash
#
#    For GUI support, when running Docker with a linux host:
#    bash$ 
#    docker run --network host --rm -v /home/boogie:/home/nodar -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY -it nodarvimba /bin/bash
#
#    For GUI support, when running Docker with a Mac host (TODO: still doesn't work):
#    bash$ docker run -d -p 127.0.0.1:5000:22 -v /Users/leaf/Documents/misc/Vimba_2_1_nodar:/home/nodar nodarvimba /usr/sbin/sshd -D
#    bash$ docker run -d -p 127.0.0.1:5000:22 -h nodarvimba -v /Users/leaf/Documents/misc/Vimba_2_1_nodar:/home/nodar -P --name="rdev" nodarvimba /usr/sbin/sshd -D
#    ssh -X nodar@localhost -p 5000
# 3. Test X11 connection
#    bash$ xmessage -center hello!
# 4. Run executable from Vimba_2_1_nodar directory
#    bash$ ./path/executable...

# Running GUI in docker
# https://stackoverflow.com/questions/24095968/docker-for-gui-based-environments


