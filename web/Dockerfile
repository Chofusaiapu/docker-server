FROM centos:7
RUN yum update -y
RUN yum -y install httpd httpd.x86_64 httpd-tools.x86_64

# install leatest apache2.4.*
RUN yum install -y epel-release && \
	yum -y install https://repo.ius.io/ius-release-el7.rpm && \
	sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/ius.repo && \
	sed -i -e "s/enabled *= *1/enabled=0/g" /etc/yum.repos.d/epel.repo

RUN yum remove -y httpd-tools-2.4.6-99.el7.centos.1.x86_64 && \
	yum remove -y httpd-2.4.6-99.el7.centos.1.x86_64
RUN yum --enablerepo=epel -y install nghttp2 && \
	yum -y install epel-release && \
	yum -y install mailcap system-logos && \
	yum -y install openssl wget && \
# Installing packages required for httpd 2.4.41
	wget https://dl.fedoraproject.org/pub/archive/epel/7/x86_64/Packages/b//brotli-1.0.7-5.el7.x86_64.rpm && \
	rpm -Uvh brotli-1.0.7-5.el7.x86_64.rpm && \
	yum -y install brotli && \
	yum -y --disablerepo=base,extras,updates --enablerepo=ius install httpd mod_ssl && \
	yum clean all
RUN sed -i -e "s|LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|#LoadModule mpm_prefork_module modules/mod_mpm_prefork.so|" /etc/httpd/conf.modules.d/00-mpm.conf && \
	sed -i -e "s|#LoadModule mpm_event_module modules/mod_mpm_event.so|LoadModule mpm_event_module modules/mod_mpm_event.so|" /etc/httpd/conf.modules.d/00-mpm.conf
# make SSL server certificate
RUN mkdir -p /etc/ssl/private && \
	openssl req -new -newkey rsa:2048 -nodes -out /etc/ssl/private/server.csr -keyout /etc/ssl/private/server.key -subj "/C=/ST=/L=/O=/OU=/CN=*.lvh.me" && \
	openssl x509 -days 3650 -req -signkey /etc/ssl/private/server.key -in /etc/ssl/private/server.csr -out /etc/ssl/private/server.crt

RUN mkdir /virtual virtual/chofusai virtual/chofusai/public_html  virtual/chofusai/public_html/cgi-bin
	
COPY docker.conf /etc/httpd/conf.d/docker.conf

RUN	chown apache:apache /virtual/chofusai/public_html/*  

# dockre can not use systemd without permission, apahce is started with httpd
CMD ["/usr/sbin/httpd", "-DFOREGROUND"]
