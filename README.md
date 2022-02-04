# GDIndex

![preview](https://i.imgur.com/ENkZwCU.png)

[简体中文](README.zh.md)

This fork is modified for Emby server. It allows Emby clients get videos and subtitles from CloudFlare Worker instead of Emby server.

### Precondition

1. You should add your domain to  CloudFlare.
2. Set the domain name of your Emby server to proxied.

### Attention
Transcode will not work when enabled.
### Instaillation

1. Install [rclone](https://rclone.org/)
2. Setup your Google Drive: https://rclone.org/drive/
3. Run `rclone config file` to find your `rclone.conf` location
4. Find `refresh_token` in your `rclone.conf`, and `root_folder_id` too(optionally).
5. Copy the content of [worker/dist/worker.js](worker/dist/worker.js) to CloudFlare Workers.
6. Fill `refresh_token`, `root_folder_id` and other options on the top of the script.
7. Set `emby_redirect` to `ture`, and fill the url of your Emby server in `emby_server`. 
8. Deploy!
9. Add the following URLs to Routes of the CloudFlare Worker you deploy in the last step.
```
REPLACE_WITH_YOUR_EMBY_SERVER_URL/videos/*
REPLACE_WITH_YOUR_EMBY_SERVER_URL/Videos/*
REPLACE_WITH_YOUR_EMBY_SERVER_URL/emby/Videos/*
REPLACE_WITH_YOUR_EMBY_SERVER_URL/emby/videos/*
```
10.  Fill path mappings in `path_match`:
```
path_match: {
    		'EMBY_SERVER_PATH_0': 'GDINDEX_PATH_0',
            'EMBY_SERVER_PATH_1': 'GDINDEX_PATH_1',
  		},
```
For example, GDIndex can access a file at `https://xxx.123.workers.dev/Mediafile/Movie/title/filename.mkv`, the Emby can access with the same file at `/mnt/external/Movie/title/filename.mkv`. The `path_match` field should be:
```
path_match: {
    		'/mnt/external': '/Mediafile',
  		},
```
### Using service accounts

1. Create a service account, a corresponding service account key, and get the JSON from the [Google Cloud Platform console](https://cloud.google.com/iam/docs/creating-managing-service-account-keys) 
2. In the props object, replace the `service_account_json` value with the contents of the service account JSON file and set `service_account` to `true`.
3. Make sure that the service account in question has access to the folder specified in `root_folder_id`
4. Deploy

## Lite mode

This mode will serve a simple nginx-like directory listing, and it only work with one drive. `upload` will be ignored in this mode.

On the top of the script, change `lite: false` into `lite: true`, than thats all.

To enable on-the-fly lite mode, especially with command-line applications, you can include a HTTP header `x-lite: true` in your requests.

[Lite mode demo](https://gdindex-demo-lite.maple3142.workers.dev/)
