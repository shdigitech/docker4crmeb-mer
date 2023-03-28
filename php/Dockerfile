FROM chialab/php:7.3-fpm

# Change apt source to Aliyun
# RUN echo \
#     deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib\
#     deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib\
#     deb http://mirrors.aliyun.com/debian-security bullseye/updates main\
#     deb-src http://mirrors.aliyun.com/debian-security bullseye/updates main\
#     deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib\
#     deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib\
#     deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib\
#     deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib\
#     > /etc/apt/sources.list

# Install supervisord
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
    supervisor libboost-all-dev git

# Remove lists
RUN rm -rf /var/lib/apt/lists/*

# Install and Enable required php extensions
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions imagick pcntl swoole uploadprogress
RUN docker-php-ext-enable bcmath imagick pcntl redis swoole

# Copy swoole_loader extension file which is necessary for CRMEB PRO to function
# for PHP 7.3
COPY ./extension/swoole_loader/swoole_loader73.so /usr/local/lib/php/extensions/no-debug-non-zts-20180731/swoole_loader73.so

# for PHP 7.4
# COPY ./extension/swoole_loader/swoole_loader74.so /usr/local/lib/php/extensions/no-debug-non-zts-20190902/swoole_loader74.so

# install yasd (extension for debugging)
RUN cd /tmp && \
    git clone https://github.com/swoole/yasd.git && \
    cd yasd && \
    phpize --clean && \
    phpize && \
    ./configure && \
    make clean && \
    make && \
    make install && \
    cd .. && \
    rm -rf yasd

COPY docker-php-entrypoint.sh /usr/local/bin/
RUN ["chmod", "+x", "/usr/local/bin/docker-php-entrypoint.sh"]
ENTRYPOINT ["/usr/local/bin/docker-php-entrypoint.sh"]
