# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Dockerfile for installing the necessary dependencies for building Avro.
# See BUILD.txt.

FROM java:8-jdk

WORKDIR /root

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Add the repository for node.js 4.x
RUN curl -sL https://deb.nodesource.com/setup_4.x | bash -

# Install dependencies from packages
RUN apt-get -qq update && \
  # Add the repository for Mono (https://www.mono-project.com/download/stable/#download-lin-debian)
  apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF && \
  apt-get -qq install -y apt-transport-https && \
  echo "deb https://download.mono-project.com/repo/debian stable-jessie main" | tee /etc/apt/sources.list.d/mono-official-stable.list && \
  apt-get -qq update && \
  # END Add the repository for Mono
  apt-get -qq install --no-install-recommends -y \
    ant \
    asciidoc \
    bison \
    bzip2 \
    cmake \
    curl \
    doxygen \
    flex \
    g++ \
    gcc \
    git \
    libboost-all-dev \
    libfontconfig1-dev \
    libfreetype6-dev \
    libglib2.0-dev \
    libjansson-dev \
    libsnappy-dev \
    libsnappy1 \
    make \
    maven \
    mono-devel \
    mono-complete \
    nodejs \
    perl \
    php5 \
    php5-gmp \
    phpunit \
    python \
    python-setuptools \
    python3-setuptools \
    rake \
    ruby \
    ruby-dev \
    source-highlight \
    subversion \
    valgrind && \
  apt-get -qq clean && \
  rm -rf /var/lib/apt/lists/*

# Install Forrest in /usr/local/apache-forrest
RUN curl -L -s "http://www.apache.org/dyn/closer.lua?action=download&filename=/forrest/apache-forrest-0.9-sources.tar.gz" | tar -xzf - -C /usr/local/ && \
    curl -L -s "http://www.apache.org/dyn/closer.lua?action=download&filename=/forrest/apache-forrest-0.9-dependencies.tar.gz" | tar -xzf - -C /usr/local/ && \
    mv /usr/local/apache-forrest-0.9 /usr/local/apache-forrest && \
    cd /usr/local/apache-forrest/main && \
    ./build.sh

# The solution for https://issues.apache.org/jira/browse/PIG-3906
# Configure where forrest can be found
RUN mkdir -p /usr/local/apache-forrest/plugins       && chmod a+rwX -R /usr/local/apache-forrest/plugins && \
    mkdir -p /usr/local/apache-forrest/build/plugins && chmod a+rwX -R /usr/local/apache-forrest/build/plugins && \
    echo 'forrest.home=/usr/local/apache-forrest' > build.properties

ENV FORREST_HOME /usr/local/apache-forrest

# Install Perl modules
RUN curl -L http://cpanmin.us | perl - --self-upgrade && \
  cpanm install Module::Install Module::Install::ReadmeFromPod \
  Module::Install::Repository \
  Math::BigInt JSON::XS Try::Tiny Regexp::Common Encode \
  IO::String Object::Tiny Compress::Zlib Test::More \
  Test::Exception Test::Pod

# Install Ruby modules
RUN gem install echoe yajl-ruby multi_json snappy

# Install global Node modules
RUN npm install -g grunt-cli

CMD ["/avro/share/docker/run-tests.sh"]
