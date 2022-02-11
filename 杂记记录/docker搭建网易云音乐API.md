# docker搭建网易云音乐API

## Docker 容器运行

> 注意: 在 docker 中运行的时候, 由于使用了 request 来发请求, 所以会检查几个 proxy 相关的环境变量\(如下所列\), 这些环境变量 会影响到 request 的代理, 详情请参考[request 的文档](https://github.com/request/request#proxies), 如果这些环境变量 指向的代理不可用, 那么就会造成错误, 所以在使用 docker 的时候一定要注意这些环境变量. 不过, 要是你在 query 中加上了 proxy 参数, 那么环境变量会被覆盖, 就会用你通过 proxy 参数提供的代理了.

request 相关的环境变量

1. http\_proxy
2. https\_proxy
3. HTTP\_PROXY
4. HTTPS\_PROXY
5. no\_proxy
6. NO\_PROXY

```
docker pull binaryify/netease_cloud_music_api

docker run -d -p 3000:3000 --name netease_cloud_music_api    binaryify/netease_cloud_music_api


// 或者 
docker run -d -p 3000:3000 binaryify/netease_cloud_music_api

// 去掉或者设置相关的环境变量

docker run -d -p 3000:3000 --name netease_cloud_music_api -e http_proxy= -e https_proxy= -e no_proxy= -e HTTP_PROXY= -e HTTPS_PROXY= -e NO_PROXY= binaryify/netease_cloud_music_api

// 或者
docker run -d -p 3000:3000 -e http_proxy= -e https_proxy= -e no_proxy= -e HTTP_PROXY= -e HTTPS_PROXY= -e NO_PROXY= binaryify/netease_cloud_music_api
```

> 以下是自行 build docker 镜像方式

```
$ git clone https://github.com/Binaryify/NeteaseCloudMusicApi && cd NeteaseCloudMusicApi

$ sudo docker build . -t netease-music-api

$ sudo docker run -d -p 3000:3000 netease-music-api
```



