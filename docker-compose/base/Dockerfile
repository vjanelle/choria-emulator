FROM centos:7

ARG role=base
ARG puppet_psk=securepsk
ARG region=docker

RUN rm -rf /var/yum/cache && yum -y install nc sudo bind-utils telnet gaw vi net-tools cronie initscripts openssl which

# We don't want 90% of systemd here
RUN rm -rf /var/yum/cache \
    && rm -f /etc/fstab \
    && rm -f /etc/generated-facts.yaml \
    && (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == systemd-tmpfiles-setup.service ] || rm -f $i; done); \
    rm -f /lib/systemd/system/multi-user.target.wants/*;\
    rm -f /etc/systemd/system/*.wants/*;\
    rm -f /lib/systemd/system/local-fs.target.wants/*; \
    rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
    rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
    rm -f /lib/systemd/system/basic.target.wants/*;\
    rm -f /lib/systemd/system/anaconda.target.wants/*;

# stop these from loading on container start
RUN cd /etc/sysctl.d/ \
      && touch /etc/sysctl.d/00-system.conf \
      10-default-yama-scope.conf \
      50-default.conf
RUN rpm -ivh http://yum.puppetlabs.com/puppet6/puppet-release-el-7.noarch.rpm
RUN yum -y install puppet-agent git
RUN echo "extension_requests:" > /etc/puppetlabs/puppet/csr_attributes.yaml
RUN echo "  pp_preshared_key: ${puppet_psk}" >> /etc/puppetlabs/puppet/csr_attributes.yaml
RUN ln -s /opt/puppetlabs/bin/puppet /opt/puppetlabs/bin/facter /opt/puppetlabs/bin/mco /usr/bin/
RUN echo "role=${role}" > /opt/puppetlabs/facter/facts.d/role.txt
RUN echo "region=${region}" > /opt/puppetlabs/facter/facts.d/region.txt
RUN /opt/puppetlabs/bin/puppet config set --section main waitforcert 10
RUN /usr/bin/systemctl enable puppet.service

ENTRYPOINT /sbin/init
