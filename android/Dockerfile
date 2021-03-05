FROM ubuntu

ENV ANDROID_SDK_ROOT /opt/android
# Preserved for backwards compatibility
ENV ANDROID_HOME /opt/android

ENV TZ=Europe/Minsk
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

WORKDIR /

SHELL ["/bin/bash", "-c"]

RUN apt update && apt install -y openjdk-14-jdk vim unzip libglu1 libpulse-dev libasound2 libc6  libstdc++6 libx11-6 libx11-xcb1 libxcb1 libxcomposite1 libxcursor1 libxi6  libxtst6 libnss3 wget curl
ENV JAVA_HOME /usr/lib/jvm/java-14-openjdk-amd64/
RUN export JAVA_HOME

ARG GRADLE_VERSION=6.5
ARG ANDROID_API_LEVEL=28
ARG ANDROID_BUILD_TOOLS_LEVEL=28.0.3
ARG EMULATOR_NAME='test'
ARG ARCHITECTURE='x86'

RUN cd /opt \
    && wget -q https://dl.google.com/android/repository/commandlinetools-linux-6609375_latest.zip -O android-commandline-tools.zip \
    && mkdir -p ${ANDROID_SDK_ROOT}/cmdline-tools \
    && unzip -q android-commandline-tools.zip -d ${ANDROID_SDK_ROOT}/cmdline-tools \
    && rm android-commandline-tools.zip

ENV PATH "${PATH}:${ANDROID_SDK_ROOT}/emulator:${ANDROID_SDK_ROOT}/platform-tools:${ANDROID_SDK_ROOT}/cmdline-tools/tools/bin"
ENV LD_LIBRARY_PATH "${ANDROID_SDK_ROOT}/emulator/lib64:${ANDROID_SDK_ROOT}/emulator/lib64/qt/lib"

RUN yes | sdkmanager --licenses

RUN touch /root/.android/repositories.cfg

RUN yes | sdkmanager --install "platform-tools" "system-images;android-${ANDROID_API_LEVEL};google_apis;${ARCHITECTURE}" "platforms;android-${ANDROID_API_LEVEL}" "build-tools;${ANDROID_BUILD_TOOLS_LEVEL}" "emulator"

RUN echo "no" | avdmanager -v create avd -n "${EMULATOR_NAME}" -k "system-images;android-${ANDROID_API_LEVEL};google_apis;${ARCHITECTURE}" -f

ENV PATH=$PATH:"/opt/gradle/gradle-${GRADLE_VERSION}/bin/"
RUN wget https://services.gradle.org/distributions/gradle-${GRADLE_VERSION}-bin.zip -P /tmp \
    && unzip -d /opt/gradle /tmp/gradle-*.zip \
    && chmod +775 /opt/gradle \
    && gradle --version \
    && rm -rf /tmp/gradle*

ADD start.sh /

RUN chmod +x start.sh

ADD appium-poc /appium-poc

#==========================
# Install Appium
#==========================
ENV APPIUM_VERSION 1.20.2
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash && \
    apt-get -qqy install nodejs && \
    npm install -g appium@${APPIUM_VERSION} --unsafe-perm=true --allow-root && \
    exit 0 && \
    npm cache clean && \
    apt-get remove --purge -y npm && \
    apt-get autoremove --purge -y && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    apt-get clean

EXPOSE 4723
EXPOSE 5555
