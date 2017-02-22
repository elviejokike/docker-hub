FROM ubuntu:16.04
MAINTAINER digilabs@philips.com
ENV DEBIAN_FRONTEND noninteractive
# ——————————
# Installing multiple Python versions with pyenv.
# ——————————
RUN apt-get update && \
    apt-get install -y locales && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8
ENV LANG en_US.UTF-8
RUN apt-get update && \
    apt-get install -y --no-install-recommends git ca-certificates && \
        git clone https://github.com/yyuu/pyenv.git .pyenv && \
        (cd .pyenv && git checkout v1.0.6) && \
    apt-get purge -y --auto-remove git ca-certificates && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
ENV PYENV_ROOT="/.pyenv" \
    PATH="/.pyenv/bin:/.pyenv/shims:$PATH"
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev llvm libncurses5-dev xz-utils \
        wget ca-certificates && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
COPY python-versions.txt ./
RUN xargs -P 4 -n 1 pyenv install < python-versions.txt && \
            pyenv global $(pyenv versions --bare) && \
            find $PYENV_ROOT/versions -type d '(' -name '__pycache__' -o -name 'test' -o -name 'tests' ')' -exec rm -rfv '{}' + && \
            find $PYENV_ROOT/versions -type f '(' -name '*.py[co]' -o -name '*.exe' ')' -exec rm -fv '{}' +
# ——————————
# Install Tox
# ——————————
RUN groupadd -r tox --gid=999 && \
    useradd -m -r -g tox --uid=999 tox
# Install gosu to run tox as the "tox" user instead of as root.
# https://github.com/tianon/gosu#from-debian
ENV GOSU_VERSION 1.10
RUN set -x && \
    apt-get update && apt-get install -y --no-install-recommends ca-certificates wget && rm -rf /var/lib/apt/lists/* && \
    dpkgArch="$(dpkg --print-architecture | awk -F- '{ print $NF }')" && \
    wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch" && \
    wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$dpkgArch.asc" && \
    export GNUPGHOME="$(mktemp -d)" && \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 && \
    gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu && \
    rm -r "$GNUPGHOME" /usr/local/bin/gosu.asc && \
    chmod +x /usr/local/bin/gosu && \
    gosu nobody true && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
RUN pyenv local 2.7.13 && \
    python -m pip install -U pip && \
    python -m pip install tox==2.3.2 && \
    pyenv local --unset && \
    pyenv rehash
WORKDIR /app
VOLUME /src

COPY docker-entrypoint.sh /
ENTRYPOINT ["/docker-entrypoint.sh"]


# Install Java.
RUN \
  apt-get update && \
  apt-get install software-properties-common python-software-properties -y && \
  echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | debconf-set-selections && \
  add-apt-repository -y ppa:webupd8team/java && \
  apt-get update && \
  apt-get install -y git curl wget oracle-java8-installer && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /var/cache/oracle-jdk8-installer
# Define commonly used JAVA_HOME variable
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# Install Docker from Docker Inc. repositories.
# Install Docker from Docker Inc. repositories.
RUN curl -sSL https://get.docker.com/ | sh && \
    apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y docker-engine && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*


# Install the magic wrapper.
ADD ./wrapdocker /usr/local/bin/wrapdocker
RUN chmod +x /usr/local/bin/wrapdocker

# Define additional metadata for our image.
VOLUME /var/lib/docker
CMD ["wrapdocker"]
