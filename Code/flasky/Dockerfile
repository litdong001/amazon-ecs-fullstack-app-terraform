FROM httpd:latest
MAINTAINER Jason Tipton

# Install apache and supplimentary programs. openssh-server, curl, and lynx-cur are for debugging the container.
RUN apt-get update && apt-get -y upgrade && DEBIAN_FRONTEND=noninteractive apt-get -y install \
    apache2 libapache2-mod-wsgi-py3 python-dev build-essential python3-pip

# Enable apache mods.
RUN a2enmod wsgi
#RUN pip install Flask
WORKDIR /flasky
COPY requirements requirements
COPY app app
COPY migrations migrations
COPY data-dev.sqlite data-dev.sqlite
COPY flasky.py config.py flasky.wsgi  boot.sh ./
RUN pip install -r requirements/dev.txt
RUN pip install gunicorn


# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid
ENV FLASK_APP flasky.py
ENV FLASK_CONFIG default


# Expose apache.
#EXPOSE 5000
EXPOSE 80

# Copy these repo into place.
ADD app /var/www/app
ADD migrations /var/www/migrations
ADD flasky.py config.py flasky.wsgi  boot.sh data-dev.sqlite /var/www/


# Update the default apache site with the config we created.
ADD apache-config.conf /etc/apache2/sites-enabled/000-default.conf

# By default start up apache in the foreground, override with /bin/bash for interative.
CMD /usr/sbin/apache2ctl -D FOREGROUND
