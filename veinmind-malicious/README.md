<h1 align="center"> veinmind-malicious </h1>

<p align="center">
veinmind-malicious 是由长亭科技自研的一款镜像恶意文件扫描工具 
</p>

## 功能特性

- 快速扫描镜像中的恶意文件 (目前支持`ClamAV`以及`VirusTotal`)
- 支持 `docker`/`containerd` 容器运行时
- 支持`JSON`/`CSV`/`HTML`等多种报告格式输出

## 兼容性

- linux/amd64
- linux/386
- linux/arm64
- linux/arm

## 开始之前

### 安装方式一

请先安装`libveinmind`，安装方法可以参考[官方文档](https://github.com/chaitin/libveinmind)

确保机器上安装了`docker`以及`docker-compose`，并启动`ClamAV`。

```
chmod +x veinmind-malicious && ./veinmind-malicious extract && cd scripts && docker-compose pull && docker-compose up -d
```

如果您使用的是`VirusTotal`，则需要在环境变量或`scripts/.env`文件中声明`VT_API_KEY`
```
export VT_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### 安装方式二

创建名为`veinmind`的网络，获取`clamav`镜像并启动
```
docker network create veinmind
docker run -d --network veinmind --name clamav clamav/clamav
```

基于平行容器的模式，获取 `veinmind-malicious` 的镜像并启动
```
docker run --rm -it --network veinmind --mount 'type=bind,source=/,target=/host,readonly,bind-propagation=rslave' -v `pwd`:/tool/data -e CLAMD_HOST=clamav -e CLAMD_PORT=3310 veinmind/veinmind-malicious scan
```

或者使用项目提供的脚本启动
```
chmod +x parallel-container-run.sh && ./parallel-container-run.sh scan
```

## 使用

1.指定镜像名称或镜像ID并扫描 (需要本地存在对应的镜像)

```
./veinmind-malicious scan [imagename/imageid]
```

2.扫描所有本地镜像

```
./veinmind-malicious scan
```

3.指定输出报告格式 (目前支持html/csv/json)

```
./veinmind-malicious scan -f [html/csv/json]
```

4.指定输出报告名称

```
./veinmind-malicious scan -n [reportname]
```

5.指定输出路径

```
./veinmind-malicious scan -o [outputpath]
```

6.指定容器运行时类型
```
./veinmind-malicious scan --containerd
```

容器运行时类型
- dockerd
- containerd

## 演示
1.扫描指定镜像名称 `xmrig/xmrig`
![](https://dinfinite.oss-cn-beijing.aliyuncs.com/image/20220119111800.png)

2.扫描指定镜像ID `sha256:ba6acccedd2923aee4c2acc6a23780b14ed4b8a5fa4e14e252a23b846df9b6c1`
![](https://dinfinite.oss-cn-beijing.aliyuncs.com/image/20220119112217.png)

3.指定输出格式以及输出名称
![](https://dinfinite.oss-cn-beijing.aliyuncs.com/image/20220119112058.png)

## 报告
![](https://dinfinite.oss-cn-beijing.aliyuncs.com/image/20220119142131.png)