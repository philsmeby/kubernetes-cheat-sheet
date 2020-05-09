# Install Stern

sudo curl -L -o /usr/local/bin/stern \
https://github.com/wercker/stern/releases/download/1.11.0/stern_linux_amd64

sudo chmod +x /usr/local/bin/stern

## Getting logs from your pods
|Stern Command|Stern Action|
---|---
|stern pod_regex|streams color coded log messages from all your pods|