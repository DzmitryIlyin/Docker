FROM gradle:5.5-jdk11

USER root

RUN apt-get update && \
    apt-get -y install curl && \
    apt-get -y install gnupg && \
    apt-get -y install rsync && \
    apt-get -y install git

# Set the Chrome repo.
RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - \
&& echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list
# Install Chrome latest.
RUN apt-get update && apt-get -y install google-chrome-stable

USER gradle
