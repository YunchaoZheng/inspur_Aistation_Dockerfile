# inspur_Aistation_Dockerfile
浪潮 Aistation 制作镜像环境使用的 Dockerfile

inspur Aistation 带的 Pytorch 镜像的 Python3.6 版本实在是太低了，自己写了 Dockerfile 解决问题

## Available
|Repo|Tags|Comments|
|:-|:-|:-|
|pytorch|[py3.9-torch1.12-cuda11.1-cudnn8](https://github.com/YunchaoZheng/inspur_Aistation_Dockerfile/tree/main/dockerfiles/py3.9-torch1.12-cuda11.1-cudnn8)|Not support A100|

## Next to-do
|Repo|Tags|Comments|
|:-|:-|:-|
|Tensorflow2|py3.9||

## How to use?

1. Install Docker on your local machine

2. Download the **Dockerfile** you want to use

3. Run docker command in the directory contains Dockerfile

```shell
docker build -t repo/tag .
```
For example
```shell
docker build -t pytorch/py3.9-torch1.12-cuda11.1-cudnn8 . --no-cache
```
```--no-cache``` cleans build cache to release disk space after build, but requires build from start if error occured half way.
