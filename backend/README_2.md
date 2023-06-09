# [Nighttime-Wisdom-Star](https://github.com/14790897/Nighttime-Wisdom-Star/tree/main)

[ENGLISH](README_EN.md)


## 项目概述

本项目旨在创建一个平台，让无法直接使用 GPT-4 的用户能够通过plus账号持有者的共享体验到 GPT-4 的强大功能。

演示地址：[Registration and Login System --- 注册和登录系统](http://35.166.94.139:5001/) 由于我可能会调试，所以有时无法访问，演示可以看[个人制作-plus用户向他人分享GPT4使用次数方法_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1kk4y1s7Ke/?spm_id_from=333.1007.top_right_bar_window_history.content.click&vd_source=4a82a11c5d10f93386761e52d7c21d95)

如果有需要的话，我会出一期视频详细讲解代码。

界面展示：

![preview](/asset/preview.jpg)

## 工作原理

GPT-4 的免费使用额度限制为每三小时最多25条请求。由于账号持有者晚上需要休息，有大约75条的额度无法使用，因此我们可以在白天收集用户的问题，然后在晚上利用plus账号持有者闲置的GPT-4额度进行处理。用户提交的问题将被暂存到Redis数据库中，键名为"{username}:data"。项目中的进程会监听时间，当到达凌晨0点后，启动处理程序，读取用户提交的问题，处理后将结果连同原始问题一起存入Redis数据库，键名为"f"{username}:results"。这些数据将被长期保存，以便用户在后续访问主页时能看到与GPT-4的历史对话记录。

本项目如何与网页版ChatGPT进行交互？其实是用了[pengzhile/pandora: 潘多拉，一个让你呼吸顺畅的ChatGPT。Pandora, a ChatGPT that helps you breathe smoothly. (github.com)](https://github.com/pengzhile/pandora)

## 使用方法（新版，方便）

欢迎使用 Nighttime Wisdom Star 应用。以下是如何在您的vps上安装和运行本应用的步骤。

### 前置条件

要使用此应用，您需要先在您的机器上安装 Docker 和 Docker Compose。以下是在Ubuntu系统上的安装指南，其他操作系统请参考官方文档。（若已安装Docker及Docker Compose，请跳过此部分）

#### 安装 Docker:

1. 更新您的现有列表包：
```bash
sudo apt-get update
```

2. 安装 Docker 的依赖项：
```bash
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```
3. 添加 Docker 官方 GPG 密钥：
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
4. 添加 Docker 存储库：
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
5. 更新存储库列表：
```bash
sudo apt-get update
```
6. 安装 Docker CE：
```bash
sudo apt-get install docker-ce
```
验证安装成功：
```bash
sudo docker run hello-world
```

#### 安装 Docker Compose:

1. 下载 Docker Compose 的当前稳定版本：
```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
2. 设置可执行权限：
```bash
sudo chmod +x /usr/local/bin/docker-compose
```
验证安装成功：
```bash
docker-compose --version
```

### 运行 Nighttime Wisdom Star

1. 下载 [docker-compose.yml](https://github.com/14790897/Nighttime-Wisdom-Star/blob/main/docker-compose.yml)文件
2. 下载[ .env.template]([Nighttime-Wisdom-Star/.env.template at main · 14790897/Nighttime-Wisdom-Star · GitHub](https://github.com/14790897/Nighttime-Wisdom-Star/blob/main/.env.template)) ，重命名为.env，修改PANDORA_ACCESS_TOKEN，里面存放从https://chat.openai.com/api/auth/session 获得的accessToken，这是与ChatGPT网页端交互的关键。
3. 转到 `docker-compose.yml` 文件所在的目录。
4. 运行以下命令：
```bash
docker-compose up
```
或者加上-d，后台运行

```bash
docker-compose up -d
```

4. 等待 Docker Compose 拉取所需的镜像并启动服务，这可能需要一些时间，具体取决于你的网络速度和机器性能。

5. 当所有服务都启动后，你就可以通过浏览器访问 [http://vps的IP:5001](http://vps的IP:5001) 来使用 Nighttime Wisdom Star 应用了。

6. 如果想查看注册用户的所有数据？

   首先使用后台运行模式，然后运行以下命令

   ```
   docker-compose exec redis sh
   ```

   输入`redis-cli`，进入数据库，然后请请教ChatGPT如何查看redis的数据。

7. 如何修改运行时间，总回答次数？

   首先将整个仓库克隆到你的vps上，在vps仓库目录下，修改pogress_in_back.py对应部分，保存退出，然后运行`docker-compose build --build-arg CACHEBUST=$(date +%s) web`，重新构建镜像，之后就可以运行了，运行代码：`docker-compose up -d`。

如果你在使用过程中遇到任何问题，或者有任何建议，欢迎提issue。

谢谢！
## 使用方法（旧版，复杂，不建议使用，适合不会用docker的用户，但目前还不完善） 

将.env.template修改为.env并填入相应参数，SECRET_KEY可以随意生成，URL为自己的服务器Pandora项目域名，或者不会配置域名，用运行的Pandora项目的IP:端口替换也可以。

### 简化步骤(这个脚本还没完成)：

```
chmod +x script.sh
./script.sh
```

### 具体步骤：

#### 安装我的项目

假设你的vps是Ubuntu系统，

在/home/ubuntu目录（目录随意，但注意要在同一目录下进行操作），下载我的GitHub项目

```
git clone https://github.com/14790897/Nighttime-Wisdom-Star.git
```

修改app.py中的`host=redis_host`(请用搜索功能找到这一行)为`host='localhost'`

```
sudo -i
```

输入以下命令

```
pip install -r requirements.txt
```

```
vim /etc/systemd/system/myapp.service
```

按i进入插入模式，复制以下内容，ctrl+shift+v粘贴，这样可以使项目后台运行

```
[Unit]
Description=My Python App
After=network.target

[Service]
User=ubuntu
WorkingDirectory=/home/ubuntu/app
ExecStart=/usr/local/bin/gunicorn --bind 0.0.0.0:5000 app:app
Restart=always

[Install]
WantedBy=multi-user.target
```

按下esc，输入:wq回车，保存并退出编辑器

开始运行命令

```
systemctl start myapp
```

命令行输入以下命令，使其开机自启动

```
systemctl enable myapp
```

#### 安装Pandora

下载Pandora项目

```
git clone https://github.com/pengzhile/pandora.git
```

然后使项目后台运行

```
vim /etc/systemd/system/pandora.service
```

和之前一样，将以下内容复制进去

```
[Unit]
Description=Pandora Service
After=network.target

[Service]
ExecStart=/usr/local/bin/pandora -t t.json -s 0.0.0.0:8008
Restart=always
# 需要根据实际路径替换
WorkingDirectory=/home/ubuntu

[Install]
WantedBy=multi-user.target
```

开始运行命令

```
systemctl start myapp
```

开机启动命令

```
systemctl enable myapp
```

在/home/ubuntu目录下创建t.json文件，输入https://chat.openai.com/api/auth/session破解得到的账号access token。

#### 安装redis数据库。

省略...

### 恭喜你，大功告成

接下来可以根据需求，进行nginx配置，可以使用域名访问（本教程就不教了），也可以IP:端口（端口是5000）直接访问。



