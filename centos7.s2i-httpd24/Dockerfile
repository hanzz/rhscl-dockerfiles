#FROM centos:centos7
FROM openshift/base-centos7

# RHSCL httpd24 image.
#
# Volumes:
#  * /var/log/httpd24 - Storage for logs when $HTTPD_LOG_TO_VOLUME is set
# Environment:
#  * $HTTPD_LOG_TO_VOLUME (optional) - When set, httpd will log into /var/log/httpd24

EXPOSE 8080
EXPOSE 8443

LABEL io.k8s.description="Platform for running httpd or building httpd-based application" \
      io.k8s.display-name="Apache httpd 2.4" \
      io.openshift.expose-services="8080:http" \
      io.openshift.expose-services="8443:https" \
      io.openshift.tags="builder,httpd,httpd24"

ENV HTTPD_CONFIGURATION_PATH=/opt/app-root/etc/httpd.d

RUN rpmkeys --import file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/httpd24/epel-7-x86_64/download/rhscl-httpd24-epel-7-x86_64.noarch.rpm && \
    yum install -y --setopt=tsflags=nodocs gettext hostname bind-utils httpd24 httpd24-mod_ssl && \
    yum clean all

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f /opt/app-root/httpdconf.sed /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf && \
    sed -i -f /opt/app-root/sslconf.sed /opt/rh/httpd24/root/etc/httpd/conf.d/ssl.conf && \
    mkdir -p /opt/app-root/etc/httpd.d/ && \
    echo 'IncludeOptional /opt/app-root/etc/httpd.d/*.conf' >> /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf && \
    head -n151 /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf | tail -n1 | grep "AllowOverride All" || exit && \
    chmod -R a+rwx /opt/rh/httpd24/root/var/run/httpd && \
    chmod -R a+rwx /opt/rh/httpd24/root/etc/httpd/conf && \
    chmod -R a+rwx /opt/rh/httpd24/root/etc/httpd/conf.d && \
    chmod -R a+r   /etc/pki/tls/certs/localhost.crt && \
    chmod -R a+r   /etc/pki/tls/private/localhost.key && \
    chmod -R a+rwx /opt/app-root/etc/httpd.d && \
    chown -R 1001:0 /opt/app-root

USER 1001

VOLUME ["/var/log/httpd24"]

CMD $STI_SCRIPTS_PATH/usage
