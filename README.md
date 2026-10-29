# My-First MLOps

This repository demonstrates a tiny reproducible MLOps flow:
1. Train a small model (`train.py`) — writes `artifacts/model.pkl` and `artifacts/metrics.json`
2. Run predictions from the command line with `run_model.py --input "[5.1,3.5,1.4,0.2]"`
3. Start a minimal Flask app with `python src/app.py` that serves `/predict`
4. Build a Docker image with `docker build -t first-mlops .`
5. CI trains the model and uploads artifacts

## Quick start (local)
1. Create and activate a venv (example using python 3.13 or 3.11):
    python -m venv .venv
    source .venv/bin/activate

2. Install dependencies:
    pip install --upgrade pip setuptools wheel
    pip install -r requirements.txt

3. Train the model:
    python train.py

4. Run a single prediction from CLI:
    python run_model.py --input "[5.1, 3.5, 1.4, 0.2]"

5. Start the API:
    python src/app.py
   Then test:
    curl -X POST "http://127.0.0.1:5000/predict" -H "Content-Type: application/json" -d '{"features":[5.1,3.5,1.4,0.2]}'

## Docker Setup, Build & Troubleshooting Guide

### 1. Build the Docker Image

Build using modern BuildKit syntax:

```bash
DOCKER_BUILDKIT=1 docker build -t hello-mlops:latest .

Ensure a .dockerignore file exists in the root to keep build context small and fast:

Plaintext
.git
.github
__pycache__
*.pyc
.venv/
venv/
*.egg-info
.pytest_cache

3. Troubleshooting & Common Fixes
Issue A: Temporary failure in name resolution during pip install (WSL2 DNS Issue)
Diagnosis:
Verify container DNS resolution and reachability:

Bash
docker run --rm alpine nslookup pypi.org
docker run --rm alpine cat /etc/resolv.conf

Fix Option 3 (Static DNS in WSL Ubuntu):
If using standard legacy NAT, disable auto-generation in /etc/wsl.conf:

Ini, TOML
[network]
generateResolvConf = false
Then set static nameservers:

Bash
sudo rm -f /etc/resolv.conf
sudo bash -c 'cat << EOF > /etc/resolv.conf
nameserver 1.1.1.1
nameserver 8.8.8.8
EOF'

