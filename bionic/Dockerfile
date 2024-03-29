FROM ubuntu:bionic
LABEL maintainer="Tinashe Chikomo <tinashe@chikomo.net>"
LABEL org.opencontainers.image.source="https://github.com/hybridadmin/docker-ansible-ubuntu"

ENV container docker
#ENV LC_ALL C
ENV DEBIAN_FRONTEND noninteractive

# Fix 'ordinal not in range' error.
ENV LC_CTYPE en_US.UTF-8
ENV LANG en_US.UTF-8

ENV pip_packages "ansible yamllint ansible-lint pymysql"

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       apt-utils \
       locales \
       language-pack-en \
       libffi-dev \
       libssl-dev \
       libyaml-dev \
       python3-dev \
       python3-setuptools \
       python3-pip \
       python3-yaml \
       software-properties-common \
       rsyslog systemd systemd-sysv systemd-cron sudo iproute2 \
    && apt-get clean \
    && rm -Rf /usr/share/doc && rm -Rf /usr/share/man \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN sed -i 's/^\($ModLoad imklog\)/#\1/' /etc/rsyslog.conf

# Fix potential UTF-8 errors with ansible-test.
RUN locale-gen en_US.UTF-8

RUN python3 -m pip install --upgrade pip
RUN python3 -m pip install wheel

# Install Ansible via Pip.
RUN python3 -m pip install $pip_packages

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
ADD https://raw.githubusercontent.com/hybridadmin/misc-scripts/master/linux/ansible/hosts /etc/ansible/hosts
ADD https://raw.githubusercontent.com/hybridadmin/misc-scripts/master/linux/ansible/ansible.cfg etc/ansible/ansible.cfg

RUN cd /lib/systemd/system/sysinit.target.wants/ \
    && ls | grep -v systemd-tmpfiles-setup | xargs rm -f $1

RUN rm -f /lib/systemd/system/multi-user.target.wants/* \
    /etc/systemd/system/*.wants/* \
    /lib/systemd/system/local-fs.target.wants/* \
    /lib/systemd/system/sockets.target.wants/*udev* \
    /lib/systemd/system/sockets.target.wants/*initctl* \
    /lib/systemd/system/basic.target.wants/* \
    /lib/systemd/system/anaconda.target.wants/* \
    /lib/systemd/system/plymouth* \
    /lib/systemd/system/systemd-update-utmp*

VOLUME [ "/sys/fs/cgroup" ]

CMD ["/lib/systemd/systemd"]
