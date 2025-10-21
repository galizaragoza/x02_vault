
### One Liner Install
```shell
sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get install -y software-properties-common && sudo add-apt-repository ppa:mozillateam/ppa -y && sudo apt-get install -y firefox-esr tesseract-ocr git nodejs npm && git clone https://github.com/qeeqbox/social-analyzer.git && cd social-analyzer && npm update && npm install && npm start
```