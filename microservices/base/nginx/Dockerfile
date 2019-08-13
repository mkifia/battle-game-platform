FROM nginx:stable

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        bc \
        curl \
        unzip \
    && rm -rf /var/lib/apt/lists/*

ENV CONSUL_TEMPLATE_VERSION 1.5.3
ENV CONSUL_TEMPLATE_SHA1 b402e1a0db26adb9638a9e85c6c672acd137df233e8c69f26180f2e2fd6f4cbc

RUN curl --retry 7 -kLso /tmp/consul-template.zip \
    "https://releases.hashicorp.com/consul/${CONSUL_TEMPLATE_VERSION}/consul_${CONSUL_TEMPLATE_VERSION}_linux_amd64.zip" \
    && echo "${CONSUL_TEMPLATE_SHA1} /tmp/consul-template.zip" | sha256sum -c \
    && unzip /tmp/consul-template.zip -d /usr/local/bin \
    && rm /tmp/consul-template.zip

ENV CONTAINERPILOT_VERSION 3.8.0
ENV CONTAINERPILOT_SHA1 84642c13683ddae6ccb63386e6160e8cb2439c26
ENV CONTAINERPILOT file:///etc/containerpilot.json

RUN curl --retry 7 -kLso /tmp/containerpilot.tar.gz \
    "https://github.com/joyent/containerpilot/releases/download/${CONTAINERPILOT_VERSION}/containerpilot-${CONTAINERPILOT_VERSION}.tar.gz" \
    && echo "${CONTAINERPILOT_SHA1} /tmp/containerpilot.tar.gz" | sha1sum -c \
    && tar zxf /tmp/containerpilot.tar.gz -C /usr/local/bin \
    && rm /tmp/containerpilot.tar.gz

COPY config/ /etc
COPY scripts/ /usr/local/bin

RUN chmod +x /usr/local/bin/reload.sh
RUN chmod +x /usr/local/bin/sensor.sh

CMD [ "/usr/local/bin/containerpilot", "nginx", "-g", "daemon off;"]
