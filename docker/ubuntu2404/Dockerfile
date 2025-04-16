FROM ubuntu:24.04

# Set noninteractive installation to avoid prompts
ENV DEBIAN_FRONTEND=noninteractive

# Update package lists and upgrade existing packages
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install development tools and Python
RUN apt-get update && \
    apt-get install -y \
    gcc \
    g++ \
    python3 \
    python3-dev \
    python3-pip \
    python3-venv \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Set Python3 as the default python
RUN ln -sf /usr/bin/python3 /usr/bin/python

# Verify installations
RUN gcc --version && \
    g++ --version && \
    python --version && \
    pip --version

# Set working directory
WORKDIR /app

CMD ["/bin/bash"]
