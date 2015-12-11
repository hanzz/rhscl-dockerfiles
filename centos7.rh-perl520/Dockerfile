FROM rhscl/s2i-httpd-24-rhel7

# This image provides a Perl 5.20 environment you can use to run your Perl applications.
MAINTAINER SoftwareCollections.org <sclorg@redhat.com>

EXPOSE 8080

# Image metadata
ENV PERL_VERSION=5.20

LABEL io.k8s.description="Platform for building and running Perl 5.20 applications" \
      io.k8s.display-name="Apache 2.4 with mod_perl/5.20" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,perl,perl520"

USER 0

# TODO: Cleanup cpanp cache after cpanminus is installed?
RUN yum install -y \
    https://www.softwarecollections.org/en/scls/rhscl/rh-perl520/epel-7-x86_64/download/rhscl-rh-perl520-epel-7-x86_64.noarch.rpm && \
    yum install -y --setopt=tsflags=nodocs make rh-perl520 rh-perl520-devel rh-perl520-mod_perl rh-perl520-perl-CPAN && \
    yum clean all && \
    cp -f $STI_SCRIPTS_PATH/assemble $STI_SCRIPTS_PATH/assemble.httpd && \
    cp -f $STI_SCRIPTS_PATH/run $STI_SCRIPTS_PATH/run.httpd

# Copy the S2I scripts from the specific language image to $STI_SCRIPTS_PATH
COPY ./s2i/bin/ $STI_SCRIPTS_PATH

# Each language image can have 'contrib' a directory with extra files needed to
# run and build the applications.
COPY ./contrib/ /opt/app-root

USER 1001

# Enable the SCL for all bash scripts.
ENV BASH_ENV=/opt/app-root/etc/scl_enable \
    ENV=/opt/app-root/etc/scl_enable \
    PROMPT_COMMAND=". /opt/app-root/etc/scl_enable"

# Set the default CMD to print the usage of the language image
CMD $STI_SCRIPTS_PATH/usage
