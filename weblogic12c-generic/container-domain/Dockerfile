# LICENSE CDDL 1.0 + GPL 2.0
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This Dockerfile extends the oracle/weblogic:12.1.3 (from the weblogic12c-generic Dockerfile) by creating a sample domain.
# The 'base-domain' created here has java EE 7 APIs enabled by default (JAX-RS 2.0 shared lib deployed, JPA 2.1, WebSockets and JSON-P 1.0).
# Util scripts are also copied into the image enabling users to use the dockWeblogic and dockNodeManager scripts.
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run: 
#      $ sudo docker build -t myweblogic .
#

# Pull base image
# ---------------
FROM oracle/weblogic:12.1.3

# Maintainer
# ----------
MAINTAINER Bruno Borges <bruno.borges@oracle.com>

# WLS Admin Password (you may change)
# This password is used for:
#  (a) 'weblogic' admin user of WebLogic 
# -----------------------------------
ENV ADMIN_PASSWORD welcome1

# Add files required to build this image
COPY container-scripts/* /u01/

# Root commands
USER root
RUN chown oracle:oracle /u01/* && \
    mv /u01/add-machine.py /u01/createMachine.sh /u01/oracle/ && \
    echo ". /u01/oracle/weblogic/user_projects/domains/base_domain/bin/setDomainEnv.sh" >> /root/.bashrc && \
    echo "export PATH=$PATH:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin" >> /root/.bashrc

# Configuration of WLS Domain
USER oracle
WORKDIR /u01/oracle/weblogic
RUN /u01/oracle/weblogic/wlserver/common/bin/wlst.sh -skipWLSModuleScanning /u01/create-wls-domain.py && \
    mkdir -p /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security && \
    mv /u01/commEnv.sh /u01/oracle/weblogic/wlserver/common/bin/commEnv.sh && \
    echo "username=weblogic" > /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security/boot.properties && \ 
    echo "password=$ADMIN_PASSWORD" >> /u01/oracle/weblogic/user_projects/domains/base_domain/servers/AdminServer/security/boot.properties && \
    echo ". /u01/oracle/weblogic/user_projects/domains/base_domain/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc && \ 
    echo "export PATH=$PATH:/u01/oracle/weblogic/wlserver/common/bin:/u01/oracle/weblogic/user_projects/domains/base_domain/bin" >> /u01/oracle/.bashrc && \
    rm /u01/create-wls-domain.py /u01/jaxrs2-template.jar 

# Expose Node Manager default port, and also default http/https ports for admin console
EXPOSE 5556 7001 7002

# Final setup
WORKDIR /u01/oracle

# Define default command to start bash. 
CMD ["/u01/oracle/weblogic/user_projects/domains/base_domain/bin/startWebLogic.sh"]
