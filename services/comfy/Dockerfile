# syntax=docker/dockerfile:1

FROM python:3.10.9-slim

SHELL ["/bin/bash", "-ceuxo", "pipefail"]

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

RUN --mount=type=cache,target=/root/.cache/pip pip install torch==1.13.0 torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cu117

RUN apt-get update && apt install fonts-dejavu-core rsync git jq moreutils -y && apt-get clean

ARG BRANCH=master SHA=07e9a6b8266326c5f51ea7b0cc20c6129bf5d238
RUN --mount=type=cache,target=/root/.cache/pip <<EOF
git config --global http.postBuffer 1048576000
git clone https://github.com/comfyanonymous/ComfyUI.git stable-diffusion
cd stable-diffusion
git checkout ${BRANCH}
git reset --hard ${SHA}
pip install -r requirements.txt
EOF

RUN --mount=type=cache,target=/root/.cache/pip pip install -U 'transformers>=4.24'

# add info
COPY . /docker/
RUN <<EOF
chmod +x /docker/entrypoint.sh
EOF

WORKDIR /stable-diffusion
ENV PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""
EXPOSE 7860
ENTRYPOINT ["/docker/entrypoint.sh"]
CMD python -u main.py --listen --port 7860 ${CLI_ARGS}
