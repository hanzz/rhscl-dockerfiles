FROM rhscl/s2i-httpd-24-rhel7

# This image provides a Passenger 4.0 environment you can use to run your Passenger
# applications.

MAINTAINER SoftwareCollections.org <sclorg@redhat.com>

EXPOSE 8080

ENV PASSENGER_VERSION 4.0

USER 0

RUN rpmkeys --import file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7 && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/rh-passenger40/epel-7-x86_64/download/rhscl-rh-passenger40-epel-7-x86_64.noarch.rpm && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/rh-ruby22/epel-7-x86_64/download/rhscl-rh-ruby22-epel-7-x86_64.noarch.rpm && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/v8314/epel-7-x86_64/download/rhscl-v8314-epel-7-x86_64.noarch.rpm && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/rh-ror41/epel-7-x86_64/download/rhscl-rh-ror41-epel-7-x86_64.noarch.rpm && \
    yum -y --setopt=tsflags=nodocs install https://www.softwarecollections.org/en/scls/rhscl/nodejs010/epel-7-x86_64/download/rhscl-nodejs010-epel-7-x86_64.noarch.rpm && \
    yum install -y --setopt=tsflags=nodocs install nss_wrapper && \
    yum install -y --setopt=tsflags=nodocs rh-ruby22 rh-ruby22-ruby-devel rh-ruby22-rubygem-rake v8314 rh-ruby22-rubygem-bundler rh-ror41-rubygem-rack nodejs010 rh-passenger40-mod_passenger rh-passenger40-ruby22 && \
    yum clean all -y && \
    cp -f $STI_SCRIPTS_PATH/assemble $STI_SCRIPTS_PATH/assemble.httpd && \
    cp -f $STI_SCRIPTS_PATH/run $STI_SCRIPTS_PATH/run.httpd

# Copy the STI scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root
COPY ./contrib/ /opt/openshift

RUN chown -R 1001:0 /opt/app-root

# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default "security" model is to run the container under
# random UID.
#    sed -ri ' s!^(\s*CustomLog)\s+\S+!\1 /proc/self/fd/1!g; s!^(\s*ErrorLog)\s+\S+!\1 /proc/self/fd/2!g;' /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf &&
RUN mkdir /tmp/sessions && \
    mkdir -p /opt/app-root/src/public && \
    chmod -R og+rwx /var/run/rh-passenger40 && \
    chown -R 1001:0 /tmp/sessions /opt/app-root/src/public && \
    mv /opt/app-root/etc/passenger.conf $HTTPD_CONFIGURATION_PATH

USER 1001

# Set the default CMD to print the usage of the language image
CMD "$STI_SCRIPTS_PATH/usage"
