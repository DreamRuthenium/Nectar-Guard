# 使用 Ubuntu 18.04 基础镜像
FROM ubuntu:18.04

# 替换为中国的镜像源
RUN sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list

# 设置环境变量
ENV GLASTOPF_HOME /opt/glastopf
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

# 更新软件包，并安装所需的包
RUN apt-get update && apt-get install -y \
    sudo \
    lsb-release \
    wget \
    apache2 \
    python2.7 \
    python-openssl \
    python-gevent \
    libevent-dev \
    python2.7-dev \
    build-essential \
    make \
    python-chardet \
    python-requests \
    python-sqlalchemy \
    python-lxml \
    python-beautifulsoup \
    mongodb \
    python-pip \
    python-dev \
    python-setuptools \
    g++ \
    git \
    php \
    php-dev \
    liblapack-dev \
    gfortran \
    libmysqlclient-dev \
    libxml2-dev \
    libxslt-dev \
    php-fpm \
    sudo && \
    rm -rf /var/lib/apt/lists/*

# 安装 hpfeeds
RUN pip install -e git+https://github.com/pwnlandia/hpfeeds.git#egg=hpfeeds-dev

# 克隆和安装 BFR
RUN cd /opt && \
    git clone https://github.com/mushorg/BFR.git && \
    cd BFR && \
    phpize && \
    ./configure --enable-bfr && \
    make && make install && \
    BFR_BUILD_OUTPUT=`find /usr/lib/php/ -type f -name "bfr.so" | awk -F"/" '{print $5}'` && \
    echo "zend_extension = /usr/lib/php/$BFR_BUILD_OUTPUT/bfr.so" >> /etc/php/7.2/fpm/php.ini

# 停止并禁用 Apache2
RUN service apache2 stop && \
    update-rc.d -f apache2 remove

# 升级 python-greenlet
RUN pip install --upgrade greenlet

# 安装 glastopf
RUN pip install --upgrade pip && \
    pip install "gevent>=1.0" && \
    pip install "webob>=1.2.0" && \
    pip install "pyopenssl" && \
    pip install "lxml" && \
    pip install "sqlalchemy>=0.8.0" && \
    pip install "jinja2" && \
    pip install "beautifulsoup>=3.2.0" && \
    pip install "requests>=2.20.0" && \
    pip install "cssselect>=0.7.0" && \
    pip install "pymongo>=2.4" && \
    pip install "MySQL-python" && \
    pip install "hpfeeds" && \
    pip install "pylibinjection" && \
    pip install "libtaxii>=1.1" && \
    pip install "python-logstash" && \
    pip install "botocore" && \
    pip install "urllib3==1.26.18" && \
    git clone https://github.com/mushorg/glastopf.git $GLASTOPF_HOME && \
    echo '' > $GLASTOPF_HOME/requirements.txt && \ 
    cd $GLASTOPF_HOME && \
    python setup.py install

# 设置工作目录
WORKDIR $GLASTOPF_HOME
CMD ["/usr/bin/python", "/usr/local/bin/glastopf-runner"]