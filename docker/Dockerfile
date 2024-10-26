# WebMap
# -
# https://github.com/SabyasachiRana/WebMap
# Author: SabyasachiRana
# -
# Usage:
#   $ cd /opt
#   $ git clone https://github.com/SabyasachiRana/WebMap.git
#   $ cd WebMap/docker
#   $ docker build -t webmap:latest .
#   $ docker run -d -v /opt/WebMap/docker/xml:/opt/xml -p 8000:8000 webmap:latest
#
# Nmap Example:
#   $ nmap -sT -A -oX /tmp/myscan.xml 192.168.1.0/24
#   $ mv /tmp/myscan.xml /opt/WebMap/docker/xml
#
# Now you can point your browser to http://localhost:8000

FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

# Install dependencies, including python3-venv
RUN apt-get update && apt-get install -y --allow-downgrades --allow-remove-essential --allow-change-held-packages \
    python3 python3-pip python3-venv curl wget git wkhtmltopdf libssl-dev vim nmap tzdata

# Set up directories and download wkhtmltopdf
RUN mkdir /opt/xml && mkdir /opt/notes && \
    wget -P /opt/ https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz && \
    cd /opt/ && tar -xvf /opt/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz

# Install Python packages using a virtual environment to avoid system restrictions
RUN python3 -m venv /opt/venv && \
    /opt/venv/bin/pip install Django requests xmltodict

# Add the virtual environment to PATH
ENV PATH="/opt/venv/bin:$PATH"

# Set up the Django project and clone WebMap repository
RUN cd /opt/ && django-admin startproject nmapdashboard && cd /opt/nmapdashboard && \
    git clone https://github.com/SabyasachiRana/WebMap.git nmapreport && \
    cd nmapreport && git checkout master

# Copy configuration files
RUN cp /opt/nmapdashboard/nmapreport/docker/settings.py /opt/nmapdashboard/nmapdashboard/
RUN cp /opt/nmapdashboard/nmapreport/docker/urls.py /opt/nmapdashboard/nmapdashboard/
RUN cp /opt/nmapdashboard/nmapreport/docker/tzdata.sh /root/tzdata.sh
RUN cp /opt/nmapdashboard/nmapreport/docker/startup.sh /startup.sh

# Run migrations
RUN cd /opt/nmapdashboard && python3 manage.py migrate

# Clean up unnecessary files
RUN apt-get autoremove -y
RUN ln -s /opt/nmapdashboard/nmapreport/token.py /root/token
RUN chmod +x /root/token

# Expose the application port
EXPOSE 8000

# Set the entrypoint
ENTRYPOINT ["bash", "/startup.sh"]
