FROM python:3.11-slim

# Build args for flexibility
ARG PRECOMMIT_CONFIG_PATH="../../config/pre-commit-config.yaml"
ARG GIT_USER_EMAIL="ci@opencloudhub.dev"
ARG GIT_USER_NAME="OpenCloudHub CI"

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    curl \
    shellcheck \
    hadolint \
    && rm -rf /var/lib/apt/lists/*

# Install Node.js for JavaScript tools
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs

# Install pre-commit and common tools
RUN pip install --no-cache-dir \
    pre-commit \
    ruff \
    bandit \
    safety \
    commitizen \
    cz-conventional-gitmoji

# Install JavaScript tools globally
RUN npm install -g \
    @biomejs/biome \
    prettier

# Copy pre-commit config and install hooks
COPY ${PRECOMMIT_CONFIG_PATH} /app/.pre-commit-config.yaml
RUN cd /app && pre-commit install-hooks

# Set working directory
WORKDIR /workspace

# Set git config
RUN git config --global --add safe.directory /workspace \
    && git config --global user.email "${GIT_USER_EMAIL}" \
    && git config --global user.name "${GIT_USER_NAME}"

# Copy the config to workspace and run pre-commit
CMD ["sh", "-c", "cp /app/.pre-commit-config.yaml . && pre-commit run --all-files"]