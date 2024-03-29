FROM ubuntu:xenial
LABEL maintainer="Tinashe Chikomo <tinashe@chikomo.net>"
LABEL org.opencontainers.image.source="https://github.com/hybridadmin/docker-ansible-ubuntu"

ENV pip_packages "ansible yamllint ansible-lint pyopenssl"

# Fix 'ordinal not in range' error.
ENV LC_CTYPE en_US.UTF-8
ENV LANG en_US.UTF-8

# Install dependencies.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       locales \
       python-pymysql \
       python-software-properties \
       software-properties-common \
       python-setuptools \
       wget rsyslog systemd systemd-cron sudo iproute2 \
    && rm -Rf /var/lib/apt/lists/* \
    && rm -Rf /usr/share/doc && rm -Rf /usr/share/man \
    && apt-get clean \
    && wget https://bootstrap.pypa.io/pip/2.7/get-pip.py \
    && python get-pip.py
RUN sed -i 's/^\($ModLoad imklog\)/#\1/' /etc/rsyslog.conf

# Fix potential UTF-8 errors with ansible-test.
RUN locale-gen en_US.UTF-8

# Install Ansible via Pip.
RUN pip install $pip_packages

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
ADD https://raw.githubusercontent.com/hybridadmin/misc-scripts/master/linux/ansible/hosts /etc/ansible/hosts
ADD https://raw.githubusercontent.com/hybridadmin/misc-scripts/master/linux/ansible/ansible.cfg etc/ansible/ansible.cfg

# Disable getty services, which otherwise will consume 100% CPU
# There are two simple solutions to this:
#
# RUN cp /bin/true /sbin/agetty
#
# or:
RUN ln -s /dev/null /etc/systemd/system/getty@tty1.service \
    && ln -s /dev/null /etc/systemd/system/getty@tty2.service \
    && ln -s /dev/null /etc/systemd/system/getty@tty3.service \
    && ln -s /dev/null /etc/systemd/system/getty@tty4.service \
    && ln -s /dev/null /etc/systemd/system/getty@tty5.service \
    && ln -s /dev/null /etc/systemd/system/getty@tty6.service

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/lib/systemd/systemd"]
