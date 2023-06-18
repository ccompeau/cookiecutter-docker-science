{%- if cookiecutter.use_nvidia_docker == 'yes' -%}
FROM nvidia/cuda:10.1-cudnn7-devel-ubuntu18.04
{%- else -%}
FROM ubuntu:20.04
{%- endif %}

# Set a default shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Make sure you avoid the timezone interactive dialog
ARG DEBIAN_FRONTEND=noninteractive

# Update and install dependencies
RUN apt-get update -qq && apt-get install -y --no-install-recommends \
  git \
  libgtk2.0-dev \
  build-essential \
  software-properties-common \
  && rm -rf /var/lib/apt/lists/*

# Add the Deadsnakes PPA and install Python 3.11
RUN add-apt-repository ppa:deadsnakes/ppa \
  && apt-get update \
  && apt-get install -y python3.11 python3.11-dev python3.11-venv python3.11-distutils

# Create a new user
ARG UID
RUN useradd docker -u $UID -s /bin/bash -m

# Create a Python virtual environment
RUN python3.11 -m venv /opt/venv
RUN chown -R docker:docker /opt/venv

# Switch to the new user
USER docker
WORKDIR /home/docker

# Make sure we use the virtualenv
ENV PATH="/opt/venv/bin:$PATH"

# Copy the requirements.txt first, for separate dependency resolving and downloading
COPY ./requirements.txt /requirements.txt

# Install Python dependencies
RUN python -m pip install --no-cache-dir -r /requirements.txt