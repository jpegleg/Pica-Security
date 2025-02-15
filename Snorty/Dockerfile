FROM ubuntu:20.04
RUN apt-get update && \
    apt-get install -y tzdata && \
    dpkg-reconfigure -f noninteractive tzdata && \
    apt-get install -y \
        gcc \
        libluajit-5.1-dev \
        openssl \ 
        libssl-dev \
        libnghttp2-dev \ 
        libdumbnet-dev \
        libdnet \ 
        autoconf \ 
        libtool \
        python-setuptools \
        python3-pip \
        python-dev \
        wget \
        build-essential \
        bison \
        flex \
        libpcap-dev \
        libpcre3-dev \
        libdumbnet-dev \
        zlib1g-dev \
        libxtables-dev \
        libip6tc-dev \
        libip4tc-dev \
        libnetfilter-queue1 \
        tcpdump \
        unzip \
        vim && pip install -U pip dpkt snortunsock

WORKDIR /opt
# define DAQ Variable (Data Acquisition library), latest is 2.0.7 https://www.snort.org/downloads/
ENV DAQ_VERSION 2.0.7
# pulll DAQ and Install
RUN mkdir ~/snort_src \
    && cd ~/snort_src  \
    && wget https://www.snort.org/downloads/archive/snort/daq-${DAQ_VERSION}.tar.gz \
    && tar -xvfz daq-${DAQ_VERSION}.tar.gz \
    && cd daq-${DAQ_VERSION} \
    && autoreconf -f -i \
    && ./configure; make; make install
ENV SNORT_VERSION 2.9.18.1
# pull snort source code from snort.org
RUN cd ~/snort_src \
    && wget https://www.snort.org/downloads/archive/snort/snort-${SNORT_VERSION}.tar.gz \
    && tar -xvfz snort-${SNORT_VERSION}.tar.gz \
    && cd snort-${SNORT_VERSION} \
    && ./configure; make; make install
RUN ldconfig 
RUN wget --no-check-certificate \
        https://github.com/John-Lin/snortunsock/archive/master.zip \
    && unzip master.zip
# create a symbolic link to /usr/sbin/snort.
RUN ln -s /usr/local/bin/snort /usr/sbin/snort \
    #create folder structure to house the Snort configuration
    && mkdir -p /etc/snort/rules \
    && mkdir /var/log/snort \
    && mkdir /usr/local/lib/snort_dynamicrules \
    #Make Rules
    && touch /etc/snort/rules/white_list.rules \
    && touch /etc/snort/rules/black_list.rules \
    && touch /etc/snort/rules/local.rules \
    && cp ~/snort_src/snort-2.9.16/etc/*.conf* /etc/snort \
    && cp ~/snort_src/snort-2.9.16/etc/*.map /etc/snort 
# set rules
RUN wget https://www.snort.org/rules/community -O ~/community.tar.gz \
    && tar -xvf ~/community.tar.gz -C ~ \
    && cp ~/community-rules/* /etc/snort/rules \
    && sudo sed -i 's/include \$RULE\_PATH/#include \$RULE\_PATH/' /etc/snort/snort.conf
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    ~/snort_src/snort-${SNORT_VERSION}.tar.gz ~/snort_src/daq-${DAQ_VERSION}.tar.gz 
ENV NETWORK_INTERFACE eth0
# snort -T -i eth0 -c /etc/snort/etc/snort.conf
CMD ["snort", "-T", "-i", "echo ${NETWORK_INTERFACE}", "-c", "/etc/snort/etc/snort.conf"]
