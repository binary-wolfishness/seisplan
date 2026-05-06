FROM python:3.11-slim

#WORKDIR /workspace

#COPY . .
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# System deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        python3.11 python3.11-dev python3-pip \
        git build-essential \
    && ln -sf /usr/bin/python3.11 /usr/bin/python \
    && ln -sf /usr/bin/python3.11 /usr/bin/python3 \
    && rm -rf /var/lib/apt/lists/*

#RUN apt-get update && apt-get install -y \
#    build-essential \
#    curl \
#    && pip install --no-cache-dir -r requirements.txt

RUN python -m pip install --upgrade pip setuptools wheel

WORKDIR /workspace

# Install CUDA-enabled torch FIRST, from the official PyTorch index
RUN pip install \
    --index-url https://download.pytorch.org/whl/cu121 \
    torch==2.2.2 torchvision==0.17.2 torchaudio==2.2.2

# torch_geometric needs torch present at install time
RUN pip install torch_geometric==2.5.3

COPY . /workspace

# Install all other deps from pyproject + your own package
RUN pip install -e .

# Install sub-repos as editable WITHOUT their deps (we already have them)
RUN pip install --no-deps -e external/seisLM
# PLAN has no setup.py — just put it on PYTHONPATH
ENV PYTHONPATH="/workspace/external/PLAN:${PYTHONPATH}"

EXPOSE 8888

# Start JupyterLab
CMD ["jupyter", "lab", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--ServerApp.token=''", "--ServerApp.password=''"]