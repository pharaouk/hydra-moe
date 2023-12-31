FROM nvidia/cuda:11.8.0-runtime-ubuntu22.04
ENV PYTHONFAULTHANDLER=1
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
ENV MPLCONFIGDIR /tmp/matplotlib
ENV TRANSFORMERS_CACHE=/models
# Install dependencies
RUN --mount=type=cache,target=/var/cache/apt,id=apt \
    apt-get update && \
    apt-get install --no-install-recommends -y git curl python3.10 python3-dev python3-pip tini && \
    apt-get clean && rm -rf /var/lib/apt/lists/*
RUN python3 -m pip install --upgrade pip

RUN ln -s /usr/bin/python3 /usr/bin/python

# Create appuser
RUN groupadd -r appuser && useradd --no-log-init -r -g appuser appuser

# Create app directory and change ownership to appuser
RUN mkdir /hydramoe_api && \
    mkdir -p /home/appuser/.cache && \
    chown -R appuser:appuser /hydramoe_api /home/appuser
COPY ./requirements.txt /hydramoe_api/requirements.txt
COPY ./api/requirements-api.txt /hydramoe_api/requirements-api.txt
RUN pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

RUN --mount=type=cache,target=~/.cache/pip \
    pip install -r /hydramoe_api/requirements.txt
RUN --mount=type=cache,target=~/.cache/pip \
    pip install -r /hydramoe_api/requirements-api.txt
# # Set working directory to /app

COPY --chown=appuser:appuser ./api /hydramoe_api
WORKDIR /hydramoe_api
RUN chmod +x ./run.sh
ENTRYPOINT ["tini", "--", "bash", "./run.sh"]   

