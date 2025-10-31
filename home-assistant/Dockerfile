FROM python:3.12-slim

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    bash \
    && rm -rf /var/lib/apt/lists/*

# Install Python dependencies
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir \
    yamllint \
    jinja2-cli \
    jinja2 

# Set up default working directory
WORKDIR /workspace

# Default command
CMD ["/bin/bash"]

