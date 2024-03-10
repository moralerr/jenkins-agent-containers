FROM jenkins/inbound-agent:3206.vb_15dcf73f6a_9-3

USER root

RUN apt-get update \
  && apt-get --yes --no-install-recommends install \
    nodejs \
    npm \
  && apt-get clean \
  && rm -rf /tmp/* /var/cache/* /var/lib/apt/lists/*


RUN npm i -g @angular/cli

ENV PATH="$PATH:/usr/local/sbin:/usr/local/bin"

USER jenkins