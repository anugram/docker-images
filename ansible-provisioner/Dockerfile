# Credit to https://github.com/geerlingguy/docker-ubuntu2204-ansible
# Duplicating to install ThalesGroup CipherTrust collection as part of the image

FROM ubuntu:22.04
LABEL maintainer="Anurag Jain"

ARG DEBIAN_FRONTEND=noninteractive

ENV pip_packages "ansible"

# Install dependencies.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
       apt-utils \
       build-essential \
       locales \
       libffi-dev \
       libssl-dev \
       libyaml-dev \
       python3-dev \
       python3-setuptools \
       python3-pip \
       python3-yaml \
       git \
       curl \
       software-properties-common \
       rsyslog systemd systemd-cron sudo iproute2 \
    && apt-get clean \
    && rm -Rf /var/lib/apt/lists/* \
    && rm -Rf /usr/share/doc && rm -Rf /usr/share/man
RUN sed -i 's/^\($ModLoad imklog\)/#\1/' /etc/rsyslog.conf
RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
RUN chmod 700 get_helm.sh
RUN ./get_helm.sh

# Fix potential UTF-8 errors with ansible-test.
RUN locale-gen en_US.UTF-8

# Install Ansible via Pip.
RUN pip3 install $pip_packages
RUN python3 -m pip install requests

# Install ThalesGroup CipherTrust collection
# RUN ansible-galaxy collection install thalesgroup.ciphertrust
RUN ansible-galaxy collection install kubernetes.core
RUN git clone --branch integration-tests-1.0.0 https://github.com/ThalesGroup/CDSP-Orchestration-Ansible.git && cd CDSP-Orchestration-Ansible/ && rm -rf thalesgroup-ciphertrust-1.0.0.tar.gz && ansible-galaxy collection build && ansible-galaxy collection install thalesgroup-ciphertrust-1.0.0.tar.gz

WORKDIR /

RUN rm -rf CDSP-Orchestration-Ansible

# Setup KubeConfig file for Helm
RUN mkdir -p ~/.kube

# Install bundled role on the host
RUN mkdir -p /root/.ansible/roles
ADD thalesdocs-dpg-quickstart /root/.ansible/roles/thalesdocs-dpg-quickstart
COPY dpg_quick_start_demo.yml /root

COPY initctl_faker .
RUN chmod +x initctl_faker && rm -fr /sbin/initctl && ln -s /initctl_faker /sbin/initctl

# Install Ansible inventory file.
RUN mkdir -p /etc/ansible
RUN echo "[local]\nlocalhost ansible_connection=local" > /etc/ansible/hosts

# Remove unnecessary getty and udev targets that result in high CPU usage when using
# multiple containers with Molecule (https://github.com/ansible/molecule/issues/1104)
RUN rm -f /lib/systemd/system/systemd*udev* \
  && rm -f /lib/systemd/system/getty.target

VOLUME ["/sys/fs/cgroup", "/tmp", "/run"]
CMD ["/lib/systemd/systemd"]
