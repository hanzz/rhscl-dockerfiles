FROM openshift3/sti-base

# This image provides an Apache+PHP environment for running PHP
# applications.

EXPOSE 8080

ENV PHP_VERSION=5.6 \
    PATH=$PATH:/opt/rh/rh-php56/root/usr/bin

LABEL io.k8s.description="Platform for building and running PHP 5.6 applications" \
      io.k8s.display-name="Apache 2.4 with PHP 5.6" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,php,php56,rh-php56"

LABEL k8s.io/description="Platform for building and running PHP 5.6 applications" \
      k8s.io/display-name="Apache 2.4 with PHP 5.6" \
      openshift.io/expose-services="8080:http" \
      openshift.io/tags="builder,php,php56,rh-php56"

# Labels consumed by Red Hat build service
LABEL Component="rh-php56-docker" \
      BZComponent="rh-php56-docker" \
      Architecture="x86_64" \
      Name="rhscl/php-56-rhel7" \
      Version="5.6" \
      Release="8"

# Install Apache httpd and PHP
RUN yum-config-manager --enable rhel-server-rhscl-7-rpms && \
    yum-config-manager --enable rhel-7-server-optional-rpms && \
    yum install -y --setopt=tsflags=nodocs httpd24 \
    rh-php56 rh-php56-php rh-php56-php-mysqlnd rh-php56-php-pgsql rh-php56-php-bcmath \
    rh-php56-php-gd rh-php56-php-intl rh-php56-php-ldap rh-php56-php-mbstring rh-php56-php-pdo \
    rh-php56-php-pecl-memcache rh-php56-php-process rh-php56-php-soap rh-php56-php-opcache rh-php56-php-xml \
    rh-php56-php-pecl-xdebug && \
    yum clean all -y

# Copy the STI scripts from the specific language image to /usr/local/sti
COPY ./.sti/bin/ /usr/local/sti

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
RUN sed -i -f /opt/app-root/etc/httpdconf.sed /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf && \
    head -n151 /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf | tail -n1 | grep "AllowOverride All" || exit && \
    mkdir /tmp/sessions && \
    chmod -R og+rwx /etc/opt/rh/rh-php56 && \
    chmod -R og+rwx /opt/rh/httpd24/root/var/run/httpd && \
    chown -R 1001:1001 /opt/app-root /tmp/sessions

USER 1001

# Set the default CMD to print the usage of the language image
CMD ["usage"]
