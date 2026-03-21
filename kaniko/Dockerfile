FROM gcr.io/kaniko-project/executor:v1.23.2-debug AS kaniko

FROM node:20-slim

# Copy Kaniko executor and its SSL certs
COPY --from=kaniko /kaniko/executor /kaniko/executor
COPY --from=kaniko /kaniko/docker-credential-gcr /kaniko/docker-credential-gcr
COPY --from=kaniko /kaniko/docker-credential-ecr-login /kaniko/docker-credential-ecr-login
COPY --from=kaniko /etc/nsswitch.conf /etc/nsswitch.conf

# Install git (needed for actions/checkout and Kaniko git context)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    curl \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Kaniko expects /kaniko/.docker for auth config
RUN mkdir -p /kaniko/.docker

WORKDIR /workspace
