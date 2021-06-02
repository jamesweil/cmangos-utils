############################################################
# Dockerfile to build a Classic CMaNGOS Server
############################################################
FROM ubuntu:latest

ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

ENV MYSQL_ROOT_PASSWORD root

RUN apt-get update
RUN apt-get install -y build-essential gcc g++ automake git-core \
autoconf make patch libmysql++-dev libtool mysql-server supervisor \
libtool libssl-dev grep binutils zlibc libc6 libbz2-dev cmake subversion libboost-all-dev curl dnsutils

# Adding mangos user and group
RUN groupadd mangos
RUN useradd -m -d /home/mangos -c "MANGoS" -g mangos mangos

WORKDIR /home/mangos/

# Cloning repos
RUN git clone git://github.com/cmangos/mangos-classic.git mangos
RUN git clone git://github.com/ACID-Scripts/Classic.git acid
RUN git clone https://github.com/cmangos/classic-db.git classicdb

# "Creating build and run folders"
RUN mkdir build
RUN mkdir run

# Compiling
WORKDIR /home/mangos/build

RUN cmake cmake ../mangos -DCMAKE_INSTALL_PREFIX=\../mangos/run -DBUILD_EXTRACTORS=ON -DPCH=1 -DDEBUG=0 -DBUILD_PLAYERBOT=ON

RUN make -j $(cat /proc/cpuinfo | awk '/^processor/{print $3}' | wc -l)
RUN make install

WORKDIR /home/mangos/run

# Adding server data
#ADD dbc /home/mangos/run/dbc/
#ADD maps /home/mangos/run/maps/
#ADD mmaps /home/mangos/run/mmaps/
#ADD vmaps /home/mangos/run/vmaps/
RUN mkdir /home/mangos/run/dbc/
RUN mkdir /home/mangos/run/maps/
RUN mkdir /home/mangos/run/mmaps/
RUN mkdir /home/mangos/run/vmaps/

# Adding configs and some scripts
ADD set_realmlist_public.sql /home/mangos/mangos/sql/base/set_realmlist_public.sql
ADD etc/supervisor/supervisord.conf /etc/supervisor/supervisord.conf
ADD etc/supervisor/conf.d/mangosd.conf /etc/supervisor/conf.d/mangosd.conf
ADD etc/supervisor/conf.d/realmd.conf /etc/supervisor/conf.d/realmd.conf
ADD create_mangos_db.sql /home/mangos/mangos/sql/create/create_mangos_db.sql
ADD create_char_realmd_db.sql /home/mangos/mangos/sql/create/create_char_realmd_db.sql
ADD create_gm_account.sql /home/mangos/mangos/sql/
ADD start.sh /start.sh

# Setting permission
RUN chmod +x /start.sh
RUN chown -R mangos:mangos /home/mangos/

EXPOSE 8085
EXPOSE 3724

VOLUME ["/home/mangos/run/etc/"]

CMD ["/start.sh"]
