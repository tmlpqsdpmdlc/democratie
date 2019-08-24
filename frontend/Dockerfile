FROM openjdk:8

# #####
# Create user

ARG user=quartz
ARG group=quartz
ARG uid=1000
ARG gid=1000

ARG USER_HOME_DIR="/tmp"
ARG APP_DIR="/app"
ARG USER_ID=1000

RUN groupadd --gid ${gid} ${group} \
  && useradd --uid ${uid} --gid ${group} --shell /bin/bash --create-home ${user}

# #####
# Install node

ARG NG_CLI_VERSION=1.7.3

RUN curl -sL https://deb.nodesource.com/setup_8.x -o nodesource_setup.sh
RUN bash nodesource_setup.sh
RUN apt-get install nodejs
RUN nodejs --version

RUN set -xe \
    && mkdir -p $USER_HOME_DIR \
    && chown $USER_ID $USER_HOME_DIR \
    && chmod a+rw $USER_HOME_DIR \
    && mkdir -p /usr/lib/node_modules \
    && chmod a+rw /usr/lib/node_modules \
    && chown -R ${user} /usr/local/lib /usr/local/include /usr/local/share /usr/local/bin /usr/bin \
    && (cd "$USER_HOME_DIR"; su ${user} -c "npm install -g @angular/cli@$NG_CLI_VERSION; npm install -g yarn; npm cache clean --force")

RUN ng --version

COPY docker-conf/docker-entrypoint.sh /docker-entrypoint.sh
RUN chmod +x /docker-entrypoint.sh

WORKDIR /app



COPY ./package*.json ./
RUN npm install
COPY . .

RUN chown 1000:1000 -R /app

USER 1000

VOLUME /app
VOLUME /app/node_modules

ENTRYPOINT ["/docker-entrypoint.sh"]
