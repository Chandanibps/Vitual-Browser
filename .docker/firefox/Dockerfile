ARG BASE_IMAGE=m1k1o/neko:base
FROM $BASE_IMAGE

ARG SRC_URL="https://download.mozilla.org/?product=firefox-latest&os=linux64&lang=en-US&v=LATEST"

#
# install required packages including 'file' to check file types
RUN set -eux; apt-get update; \
    apt-get install -y --no-install-recommends openbox \
    xz-utils bzip2 libgtk-3-0 libdbus-glib-1-2 file; \
    #
    # fetch latest release
    wget --no-cache -O /tmp/firefox-setup.tar.bz2 "${SRC_URL}"; \
    # Check the file type to ensure it's a .tar.bz2 file
    file /tmp/firefox-setup.tar.bz2; \
    mkdir /usr/lib/firefox; \
    # Extract based on file format
    tar -xjf /tmp/firefox-setup.tar.bz2 -C /usr/lib || { \
        echo "Failed to extract bzip2 file, trying tar.gz"; \
        tar -xzf /tmp/firefox-setup.tar.bz2 -C /usr/lib; \
    }; \
    rm -f /tmp/firefox-setup.tar.bz2; \
    ln -s /usr/lib/firefox/firefox /usr/bin/firefox; \
    #
    # create a profile directory
    mkdir -p /home/neko/.mozilla/firefox/profile.default/extensions; \
    chown -R neko:neko /home/neko/.mozilla/firefox/profile.default; \
    #
    # clean up
    apt-get --purge autoremove -y xz-utils bzip2; \
    apt-get clean -y; \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/*

#
# copy configuration files
COPY supervisord.conf /etc/neko/supervisord/firefox.conf
COPY neko.js /usr/lib/firefox/mozilla.cfg
COPY autoconfig.js /usr/lib/firefox/defaults/pref/autoconfig.js
COPY policies.json /usr/lib/firefox/distribution/policies.json
COPY --chown=neko profiles.ini /home/neko/.mozilla/firefox/profiles.ini
COPY openbox.xml /etc/neko/openbox.xml
