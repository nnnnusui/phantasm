ARG VARIANT="hirsute"
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

WORKDIR /tmp

# Install apps
  RUN apt update

# Build workspace
  ARG work_user
  USER ${work_user}
  ARG work_path
  ENV workspace=${work_path}
WORKDIR ${workspace}
