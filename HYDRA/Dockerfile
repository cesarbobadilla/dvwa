FROM kalilinux/kali-rolling
# Install common dependencies
RUN apt-get update && apt-get install -y \
 nmap \
 net-tools \
 grep \
 curl \
 tar \
 make \
 git \
 jq \
 sshpass
# Install hydra dependencies
RUN apt-get install -y libssl-dev libssh-dev libidn11-dev libpcre3-dev \
                 libgtk2.0-dev libpq-dev libsvn-dev \
                 firebird-dev libmemcached-dev libgpg-error-dev
RUN git clone https://github.com/vanhauser-thc/thc-hydra.git
RUN cd thc-hydra && ./configure && make && make install
