# buildpack-deps:jessie is debian:jessie with build tools:
FROM buildpack-deps:jessie

RUN apt-get update

# Common dependencies and setup, omitting stuff already included in 'buildpack-deps':
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y libpcap-dev libcrypto++-dev libboost-all-dev

# Missing dependencies: http://redmine.named-data.net/issues/2013
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y procps psmisc sudo

ENV NDN_CXX_REPO https://github.com/named-data/ndn-cxx.git
ENV NDN_CXX_VERSION ndn-cxx-0.4.1

ENV NDN_NFD_REPO https://github.com/named-data/NFD.git
ENV NDN_NFD_VERSION NFD-0.4.1

RUN mkdir -p /scripts /source

# First checkout sources, so it's easier to just rebuild changing options
RUN cd /source && git clone --recursive ${NDN_CXX_REPO}
RUN cd /source && git clone --recursive ${NDN_NFD_REPO}

ADD ./scripts/install-ndn-cxx.sh /scripts/
RUN /scripts/install-ndn-cxx.sh

ADD ./scripts/install-nfd.sh /scripts/
RUN /scripts/install-nfd.sh

RUN ndnsec-keygen /localhost/operator | ndnsec-install-cert -
# fix nfd-start for docker
RUN perl -pi -e 's/bash/bash -i/' /bin/nfd-start
RUN echo 'fg'>> /bin/nfd-start

CMD /bin/nfd-start
