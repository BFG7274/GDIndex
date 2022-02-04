# GDIndex

![preview](https://i.imgur.com/ENkZwCU.png)

这个 fork 可以让 Emby 客户端直接从 CloudFlare Worker 获取视频和字幕。

### 前置条件

1. 域名需要托管在 CloudFlare
2. 为 Emby 服务器使用的地址打开 CloudFlare CDN

### 注意
启用时转码不可用
### 安装

1. 安装 [rclone](https://rclone.org/)
2. 设定 Google Drive: https://rclone.org/drive/
3. 执行 `rclone config file` 以找到你的 `rclone.conf`
4. 在 `rclone.conf` 中寻找 `refresh_token` 以及 `root_folder_id` (可选)
5. 复制 [worker/dist/worker.js](worker/dist/worker.js) 的内容到 CloudFlare Workers
6. 在脚本顶端填上 `refresh_token`, `root_folder_id` 以及其他的选项
7. 将 `emby_redirect` 设置为 `true`，将 Emby 服务器的地址填在 `emby_server` 中。
8. 部署!
9. 将下面的地址添加到上一步部署的 CloudFlare Worker 的路由中
```
用你的服务器地址替换/videos/*
用你的服务器地址替换/Videos/*
用你的服务器地址替换/emby/Videos/*
用你的服务器地址替换/emby/videos/*
```
10. 将路径映射填在 `path_match` 中：
```
path_match: {
    		'Emby服务器路径0': 'GDINDEX路径0',
            'Emby服务器路径1': 'GDINDEX路径1',
  		},
```
例如，通过 GDindex 可以在 `https://xxx.123.workers.dev/Mediafile/Movie/title/filename.mkv` 访问文件，Emby 服务器可以通过 `/mnt/external/Movie/title/filename.mkv` 访问到同一文件。
`path_match` 应该写成这样：
```
path_match: {
    		'/mnt/external': '/Mediafile',
  		},
```
### 使用服务帐户

1. 创建一个服务帐户，一个相应的服务帐户密钥，然后从[Google Cloud Platform控制台]获取JSON（https://cloud.google.com/iam/docs/creating-managing-service-account-keys）
2. 在props对象中，将`service_account_json`值替换为服务帐户JSON文件的内容，并将`service_account`设置为`true`。
3. 确保所涉及的服务帐户有权访问“ root_folder_id”中指定的文件夹
4. 部署
