name: Stable Diffusion WebUI CI

on:
  workflow_dispatch:

jobs:
  webui-test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Stable Diffusion WebUI
        uses: actions/checkout@v4
        with:
          repository: Edmond2007/stable-diffusion-webui

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.10'

      - name: Install system dependencies
        run: |
          sudo apt-get update
          sudo apt-get install -y libgl1 libglib2.0-0

      - name: Upgrade pip and install requirements
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Download Stable Diffusion v1.5 Model
        run: |
          mkdir -p models/Stable-diffusion
          wget -O models/Stable-diffusion/model.ckpt https://huggingface.co/runwayml/stable-diffusion-v1-5/resolve/main/v1-5-pruned-emaonly.ckpt

      - name: Start WebUI (background, for testing)
        run: |
          nohup python launch.py --listen --port 7860 --ckpt models/Stable-diffusion/model.ckpt > webui.log 2>&1 &

      - name: Wait for WebUI to become available
        run: |
          for i in {1..30}; do
            if curl -s http://localhost:7860/ > /dev/null; then
              echo "WebUI is up!"
              exit 0
            fi
            sleep 5
          done
          echo "WebUI did not start in time." && exit 1

      - name: Upload WebUI logs
        uses: actions/upload-artifact@v4
        with:
          name: webui-log
          path: webui.log
