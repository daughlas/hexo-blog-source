---
title: LINUX 配置 Node 生产环境
date: 
categories:
- 运维
tags:
- linux
- CentOS
- Node.js
---

# LINUX 配置 Node 生产环境

## 安装 Node.js 生产环境

更新

```bash
sudo yum update
sudo yum install vim openssl build-essential libssl-dev wget curl git
sudo yum groupinstall "Development Tools"
sudo yum install openssl-devel
```

nvm

```bash
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.1/install.sh | bash
nvm install v6.9.5
nvm use v6.9.5
nvm alias default v6.9.5
node -v
npm --registry=https://registry.npm.taobao.org install -g npm
echo fs.inotify.max_user_watches=524288 | sudo tee -a /etc/sysctl.conf && sudo sysctl 
```

npm 常用 工具包

```bash
npm i pm2 webpack gulp grunt-cli -g
```



```bash
sudo vi /etc/iptables.up.rules
```



## pm2 使用

```bash
# 开启服务
pm2 start app.js

# 查看所有服务
pm2 list

# 详情
pm2 show app(name) 

# 当前的日志
pm2 logs
```



## 配置 nginx

停掉阿帕奇(ubuntu)

```bash
sudo service apache2 stop
```

卸载阿帕奇(ubuntu)

```bash
update-rc.d -f apache2 remove

sudo apt-get remove apache2
```

安装 nginx 

配置 nginx

```bash
upstream blog {
	server 127.0.0.1:8080;
}

server {
	listen 80;
	server_name 47.106.204.77;
	
	location / {
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
		proxy_set_header Host $http_host;
		proxy_set_header X-Nginx-Proxy true;
		
		proxy_pass http://blog;
		proxy_redirect off;
	}
}
```

