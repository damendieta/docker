FROM debian:stretch-slim
MAINTAINER Odoo S.A. <info@odoo.com>

SHELL ["/bin/bash", "-xo", "pipefail", "-c"]

# Generate locale C.UTF-8 for postgres and general locale data
ENV LANG C.UTF-8

# Use backports to avoid install some libs with pip
RUN echo 'deb http://deb.debian.org/debian stretch-backports main' > /etc/apt/sources.list.d/backports.list

# Install some deps, lessc and less-plugin-clean-css, and wkhtmltopdf
RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            ca-certificates \
            curl \
            dirmngr \
            fonts-noto-cjk \
            gnupg \
            libssl1.0-dev \
            node-less \
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
        && curl -o wkhtmltox.deb -sSL https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.5/wkhtmltox_0.12.5-1.stretch_amd64.deb \
        && echo '7e35a63f9db14f93ec7feeb0fce76b30c08f2057 wkhtmltox.deb' | sha1sum -c - \
        && apt-get install -y --no-install-recommends ./wkhtmltox.deb \
        && rm -rf /var/lib/apt/lists/* wkhtmltox.deb

# install latest postgresql-client
RUN echo 'deb http://apt.postgresql.org/pub/repos/apt/ stretch-pgdg main' > /etc/apt/sources.list.d/pgdg.list \
        && GNUPGHOME="$(mktemp -d)" \
        && export GNUPGHOME \
        && repokey='B97B0AFCAA1A47F044F244A07FCC7D46ACCC4CF8' \
        && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
        && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/pgdg.gpg.asc \
        && gpgconf --kill all \
        && rm -rf "$GNUPGHOME" \
        && apt-get update  \
        && apt-get install --no-install-recommends -y postgresql-client \
        && rm -f /etc/apt/sources.list.d/pgdg.list \
        && rm -rf /var/lib/apt/lists/*

# Install rtlcss (on Debian stretch)
RUN echo "deb http://deb.nodesource.com/node_8.x stretch main" > /etc/apt/sources.list.d/nodesource.list \
    && GNUPGHOME="$(mktemp -d)" \
    && export GNUPGHOME \
    && repokey='9FD3B784BC1C6FC31A8A0A1C1655A0AB68576280' \
    && gpg --batch --keyserver keyserver.ubuntu.com --recv-keys "${repokey}" \
    && gpg --batch --armor --export "${repokey}" > /etc/apt/trusted.gpg.d/nodejs.gpg.asc \
    && gpgconf --kill all \
    && rm -rf "$GNUPGHOME" \
    && apt-get update \
    && apt-get install --no-install-recommends -y nodejs \
    && npm install -g rtlcss \
    && rm -rf /var/lib/apt/lists/*

# Install Odoo
ENV ODOO_VERSION 12.0
ARG ODOO_RELEASE=20201209
ARG ODOO_SHA=da9a0ed9e05f1b79d5bb7a8ab268b42a81bc3fcb
RUN curl -o odoo.deb -sSL http://nightly.odoo.com/${ODOO_VERSION}/nightly/deb/odoo_${ODOO_VERSION}.${ODOO_RELEASE}_all.deb \
        && echo "${ODOO_SHA} odoo.deb" | sha1sum -c - \
        && apt-get update \
        && apt-get -y install --no-install-recommends ./odoo.deb \
        && rm -rf /var/lib/apt/lists/* odoo.deb

RUN apt-get update
RUN apt-get install -y wget
RUN sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt stretch-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
RUN wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -

RUN apt-get update \
        && apt-get install -y --no-install-recommends \
            libpcap-dev \
            libpq-dev \
            build-essential \
            python3-dev \
            python3-pandas \
            default-jdk \
            default-jre \
            libreoffice-writer \
            libreoffice-calc \
            git

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
        ftpretty

RUN python3 -m pip install redis
RUN python3 -m pip install paramiko
RUN pip3 install git+git://github.com/OCA/openupgradelib.git

# Ensure odoo dependencies as in requirements.txt
RUN python3 -m pip install --upgrade \
        Babel==2.3.4 \
        chardet==3.0.4 \
        decorator==4.0.10 \
        docutils==0.12 \
        ebaysdk==2.1.5 \
        feedparser==5.2.1 \
        gevent==1.1.2 \
        greenlet==0.4.10 \
        html2text==2016.9.19 \
        Jinja2==2.10.1 \
        libsass==0.12.3 \
        lxml==3.7.1 \
        Mako==1.0.4 \
        MarkupSafe==0.23 \
        mock==2.0.0 \
        num2words==0.5.6 \
        ofxparse==0.16 \
        passlib==1.6.5 \
        Pillow==4.0.0 \
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
