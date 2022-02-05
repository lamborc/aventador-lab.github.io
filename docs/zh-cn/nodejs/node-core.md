# 


### Centos Nodejs Install

```bash
cd /opt
wget https://npmmirror.com/mirrors/node/latest-v14.x/node-v14.0.0-linux-x64.tar.xz
tar -xvf node-v14.0.0-linux-x64.tar.xz

mv node-v14.0.0-linux-x64 node-v14
ln -s /opt/node-v14/bin/node /usr/local/bin/node
```