# Build with:
#	docker build -t ghcr.io/termux/package-builder .
# Push to GitHub registry with:
#	docker push ghcr.io/termux/package-builder
# This is done after changing this file or any of the
# scripts/setup-{ubuntu,android-sdk}.sh setup scripts.
FROM ubuntu:22.04

# Fix locale to avoid warnings:
ENV LANG en_US.UTF-8

# Needed for setup:
COPY ./setup-ubuntu.sh ./setup-android-sdk.sh ./setup-cgct.sh ./properties.sh ./openjdk-r-ppa.gpg /tmp/
RUN mkdir /tmp/build
COPY ./build/termux_download.sh /tmp/build/

# Setup needed packages and the Android SDK and NDK:
RUN apt-get update && \
	apt-get -yq upgrade && \
	apt-get install -yq sudo lsb-release software-properties-common && \
	useradd -u 1001 -U -m -s /bin/bash builder && \
	echo "builder ALL=(root) NOPASSWD:ALL" > /etc/sudoers.d/builder && \
	chmod 0440 /etc/sudoers.d/builder && \
	su - builder -c /tmp/setup-ubuntu.sh && \
	su - builder -c /tmp/setup-android-sdk.sh && \
	su - builder -c /tmp/setup-cgct.sh && \
	# Removed unused parts to make a smaller Docker image:
	apt-get remove -yq --autoremove lsb-release software-properties-common && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/*

# Switch User
USER builder:builder

# We expect this to be mounted with '-v $PWD:/home/builder/termux-packages':
WORKDIR /home/builder/termux-packages
