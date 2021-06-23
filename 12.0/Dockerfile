FROM debian:buster-slim
MAINTAINER Odoo S.A. <info@odoo.com>

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Use backports to avoid install some libs with pip
RUN echo 'deb http://deb.debian.org/debian buster-backports main' > /etc/apt/sources.list.d/backports.list

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            dirmngr \
            fonts-noto-cjk \
            gnupg \
            libssl-dev \
            node-less \
            npm \
            python3-num2words \
            python3-pip \
            python3-phonenumbers \
            python3-pyldap \
            python3-qrcode \
            python3-renderpm \
            python3-setuptools \
            python3-slugify \
            python3-vobject \
            python3-watchdog \
            python3-xlrd \
            python3-xlwt \
            xz-utils \
        && curl -o wkhtmltox.deb -sSL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.buster_amd64.deb \
        && echo 'ea8277df4297afc507c61122f3c349af142f31e5 wkhtmltox.deb' | sha1sum -c - \
        && apt-get install -y --no-install-recommends ./wkhtmltox.deb \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# install latest postgresql-client
RUN echo 'deb http://apt.postgresql.org/pub/repos/apt/ buster-pgdg main' > /etc/apt/sources.list.d/pgdg.list \
        && GNUPGHOME="$(mktemp -d)" \
        && export GNUPGHOME \
        && repokey='B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8' \
        && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
        && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/pgdg.gpg.asc \
        && gpgconf --kill all \
        && rm -rf "$GNUPGHOME" \
        && apt-get update  \
        && apt-get install --no-install-recommends -y postgresql-client-13 \
        && rm -f /etc/apt/sources.list.d/pgdg.list \
        && rm -rf /var/lib/apt/lists/*

# Install rtlcss (on Debian buster)
RUN npm install -g rtlcss

# Install Odoo
ENV ODOO_VERSION 12.0
ARG ODOO_RELEASE=20210614
ARG ODOO_SHA=cdec569bd9834df10b48564cf11f18ed8b11ac1c
RUN curl -o odoo.deb -sSL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo "${ODOO_SHA} odoo.deb" | sha1sum -c - \
        && apt-get update \
        && apt-get -y install --no-install-recommends ./odoo.deb \
        && rm -rf /var/lib/apt/lists/* odoo.deb

RUN apt-get update
RUN apt-get install -y wget
RUN sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt buster-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            libpcap-dev \
            libpq-dev \
            libffi-dev \
            cargo \
            build-essential \
            python3-dev \
            python3-pandas \
            default-jdk \
            default-jre \
            libreoffice-writer \
            libreoffice-calc \
            git \
            nano

RUN pip3 install wheel

RUN pip3 install \
        pyotp \
        wand \
        checksumdir \
        cachetools \
        validate_email \
        simplejson \
        oauthlib \
        pyqrcode \ 
        zeep \
        xmltodict \
        odoorpc \
        openpyxl \
        pyzk \
        viivakoodi \
        raven \
        email_validator \
        py3o.template \
        py3o.formats \
        netifaces \
        wdb \
        shapely \
        geojson \
        ftpretty \
        jsonrpcclient \
        humanize \
        lameenc

RUN python3 -m pip install redis
RUN python3 -m pip install paramiko
RUN pip3 install git+git://github.com/OCA/openupgradelib.git

# Required to use onedrive/sharepoint integration.
RUN pip3 install microsoftgraph-python

# Ensure odoo dependencies as in requirements.txt
RUN python3 -m pip install --upgrade \
        Babel==2.3.4 \
        chardet==3.0.4 \
        decorator==4.0.10 \
        docutils==0.12 \
        ebaysdk==2.1.5 \
        feedparser==5.2.1 \
        gevent==1.5.0 \
        greenlet==0.4.14 \
        html2text==2016.9.19 \
        Jinja2==2.10.1 \
        libsass==0.12.3 \
        lxml==4.2.3 \
        Mako==1.0.4 \
        MarkupSafe==0.23 \
        mock==2.0.0 \
        num2words==0.5.6 \
        ofxparse==0.16 \
        passlib==1.6.5 \
        Pillow==6.1.0 \
        psutil==4.3.1 \
        psycopg2==2.7.3.1 \
        pydot==1.2.3 \
        pyparsing==2.1.10 \
        PyPDF2==1.26.0 \
        pyserial==3.1.1 \
        python-dateutil==2.5.3 \
        pytz==2016.7 \
        pyusb==1.0.0 \
        qrcode==5.3 \
        reportlab==3.3.0 \
        requests==2.20.0 \
        suds-jurko==0.6 \
        vatnumber==1.2 \
        vobject==0.9.3 \
        Werkzeug==0.11.15 \
        XlsxWriter==0.9.3 \
        xlwt==1.3.* \
        xlrd==1.0.0

RUN apt-get clean
RUN apt-get purge -y build-essential python3-dev && apt-get -y autoremove && apt-get clean

# Set user id and guid as 1500
RUN usermod -u 1500 odoo
RUN groupmod -g 1500 odoo

# Copy entrypoint script and Odoo configuration file
COPY ./entrypoint.sh /
COPY ./odoo.conf /etc/odoo/

# Mount /var/lib/odoo to allow restoring filestore and /mnt/extra-addons for users addons
RUN chown odoo /etc/odoo/odoo.conf \
    && mkdir -p /mnt/extra-addons \
    && chown -R odoo /mnt/extra-addons
VOLUME ["/var/lib/odoo", "/mnt/extra-addons"]

# Expose Odoo services
EXPOSE 8069 8071 8072

# Set the default config file
ENV ODOO_RC /etc/odoo/odoo.conf

COPY wait-for-psql.py /usr/local/bin/wait-for-psql.py

# Set default user when running the container
USER odoo

ENTRYPOINT ["/entrypoint.sh"]
CMD ["odoo"]
