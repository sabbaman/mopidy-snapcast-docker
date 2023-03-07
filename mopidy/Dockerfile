FROM debian:buster-slim

RUN set -ex \
    # Official Mopidy install for Debian/Ubuntu along with some extensions
    # (see https://docs.mopidy.com/en/latest/installation/debian/ )
 && apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        curl \
        dumb-init \
        gnupg \
        gstreamer1.0-alsa \
        gstreamer1.0-plugins-bad \
        python3-crypto \
        python3-distutils \
 && curl -L https://bootstrap.pypa.io/get-pip.py | python3 - \
 && pip install pipenv
    # Clean-up

run set -ex \
&& mkdir -p /usr/local/share/keyrings \
&& DEBIAN_FRONTEND=noninteractive apt-get install -y wget \
&& wget -q -O /usr/local/share/keyrings/mopidy-archive-keyring.gpg \
  https://apt.mopidy.com/mopidy.gpg \
&& wget -q -O /etc/apt/sources.list.d/mopidy.list https://apt.mopidy.com/buster.list

RUN set -ex \
 && apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y \
        mopidy \
        mopidy-soundcloud \
        mopidy-spotify

RUN pip install Mopidy-Youtube
RUN pip install --upgrade youtube-dl
RUN pip install --upgrade ytmusicapi
RUN pip install Mopidy-Iris

RUN set -ex \
 && mkdir -p /var/lib/mopidy/.config \
 && ln -s /config /var/lib/mopidy/.config/mopidy

# Start helper script.
COPY entrypoint.sh /entrypoint.sh

# Default configuration.
COPY mopidy.conf /config/mopidy.conf

# Copy the pulse-client configuratrion.
COPY pulse-client.conf /etc/pulse/client.conf

# Allows any user to run mopidy, but runs by default as a randomly generated UID/GID.
ENV HOME=/var/lib/mopidy

# Basic check,

VOLUME ["/var/lib/mopidy/local", "/var/lib/mopidy/media"]

EXPOSE 6600 6680 5555/udp

ENTRYPOINT ["/usr/bin/dumb-init", "/entrypoint.sh"]
CMD ["/usr/bin/mopidy"]

HEALTHCHECK --interval=5s --timeout=2s --retries=20 \
    CMD curl --connect-timeout 5 --silent --show-error --fail http://localhost:6680/ || exit 1
