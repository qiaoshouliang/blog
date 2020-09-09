---
layout: post
title: docker
tags: [七牛]
---

# gitlab 的搭建

使用docker的方式快速搭建docker

```shell
sudo docker run --detach \
    --publish 443:443 --publish 80:80 --publish 222:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest

```



# gitlab的工作流





