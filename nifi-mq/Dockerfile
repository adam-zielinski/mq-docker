FROM mq
RUN useradd cs186081 -G mqm && \
    echo cs186081:cs186081 | chpasswd
COPY config.mqsc /etc/mqm/
