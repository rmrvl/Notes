FROM kalilinux/kali-rolling

RUN apt update
RUN apt install -y xvfb xrdp xfce4-session xfce4 xfce4-terminal dbus-x11 x11-xserver-utils
RUN apt install -y openssh-server procps
RUN echo "root:toor" | chpasswd

COPY entrypoint /sbin/entrypoint

CMD ["/sbin/entrypoint"]
