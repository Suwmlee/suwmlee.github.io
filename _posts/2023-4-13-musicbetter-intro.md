---
layout: post
title: Musicbetter使用说明 v0.2
categories: [musicbetter, transcode, upload]
---

Musicbetter是一个脚本工具，它可以对音乐进行转码然后自动上传到站点。

### 准备阶段

- docker下载镜像:
    ```sh
    docker pull suwmlee/musicbetter
    ```
- 新建 `musicbetter` 文件夹 (后续操作均在此文件夹内)

- 复制 docker-compose 文件与创建文件夹

    - 复制  https://github.com/Suwmlee/REDBetter 仓库内的 `docker-compose.yml`, `docker-compose.override.example.yml` 与 `config.example` 到本地

    - 创建 `data`, `config`, `output`, `torrents` 文件夹

    __文件夹说明__
    - data 存放需要转码的音乐(从网站下载)
    - config 存放配置
    - output 存放转码后输出的文件
    - torrents 转码后生成的种子文件

### 配置与运行

1. 重命名 `docker-compose.override.example.yml` 为 `docker-compose.override.yml` 并更改内部配置
    - 更改 user 权限，默认 `1026:101`，linux系统使用自己的 'user: uid:gid' 参数，如果不清楚可使用root用户即更改为 `0:0`，如果运行过程中出现`权限`或`无法访问`问题，一般就是此处。
        ```sh
        # 在shell内使用 id 命令查看
        id abc
        uid=1026(abc) gid=101(users) groups=101(users)
        ```
    - 按文件夹说明更改 volume 里对应的文件映射

2. 重命名 `config.example` 为 `config` 并放到 `config` 文件夹里，修改文件里的 cookies 、账号、密码
    - cookies 可以在浏览器内 F12 获得，__必须__

3. 进行转码

    确保`data`路径下有需要转码的音乐，在shell内运行以下命令
    ```sh
    docker-compose run --rm musicbetter -U "http://xxxx.ch/torrents.php?id=1000&torrentid=1000000"
    ```
    第一次使用 `-U` 参数测试，只在本地转码，不上传到red，测试转码成功后，取消 `-U` 参数即可自动上传
    此地址的种子文件必须已经下载到 data 文件夹
    指定种子链接必须带有 id 与 torrentid，获取方法:
    1. 点击red网站上 flac 种子的 PL 按钮即可在浏览器地址栏看到
    2. 查看下载器对应的种子，种子描述内有链接，点击后浏览器跳转的地址


### 进阶

以 qbittorrent 为例，操作流程:

1. 设置 qb 的 __默认保存路径__ 为 __output文件夹__
2. 设置 qb 的 __监控文件夹__ 为 __torrents文件夹__
3. qb 中新增分类 `已完成`
4. 在 red 内选择要转码的flac种子下载，下载路径为 __data文件夹__ 
    
    注意!! 不是默认保存路径
5. 重复 步骤4 下载需要转码种子
6. 开始转码操作，按照 `配置与运行` 中步骤3进行操作
7. 转码完成后将种子分类为 `已完成` ，同时可以观察到qb已经自动做种转码后的种子
8. 回到步骤4


## 其他

仅启动docker，可以进入docker内操作，可参考其他文档，此处不做过多说明
```sh
# 请自行添加 -v 文件映射
docker run -d --name musicbetter  -it --entrypoint /bin/bash suwmlee/musicbetter:latest
```
