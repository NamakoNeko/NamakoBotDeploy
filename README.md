# NamakoBotDeploy

魔改自[YoshinoBot部署脚本](https://github.com/LQBing/YoshinoBotDeploy)

## 关联项目

[YoshinoBot](https://github.com/LQBing/YoshinoBot)

[docker-cqhttp-mirai](https://github.com/LQBing/docker-cqhttp-mirai)

[cqhttp-mirai](https://github.com/yyuueexxiinngg/cqhttp-mirai)

## 第一次运行

安装docker（如果已经安装好，请跳过）

```shell script
wget get.docker.com -O install_docker.sh
sh install_docker.sh --mirror Aliyun
```

拉取镜像

海外：

```shell script
docker pull lqbing/cqhttp-mirai
docker pull haishenmao/namakobot
```

国内：

```shell script
docker pull registry.cn-hongkong.aliyuncs.com/lqbing/cqhttp-mirai
docker pull registry.cn-hangzhou.aliyuncs.com/haishenmao/namakobot
docker tag registry.cn-hongkong.aliyuncs.com/lqbing/cqhttp-mirai lqbing/cqhttp-mirai
docker tag registry.cn-hangzhou.aliyuncs.com/haishenmao/namakobot haishenmao/namakobot
```

~~当前设备第一次运行可能需要输入验证码，步骤见[docker-cqhttp-mirai文档](https://github.com/LQBing/docker-cqhttp-mirai#env)~~

请在本机运行一次cqhttp-mirai,然后将生成的config.json导入到data文件夹中

## docker-compose启动

参照上面的文档第一次运行需要完成的步骤（新设备验证码输入和扫描认证，如果不完成的话可能导致容器启动要求输入验证码而无法完成登陆）后可以运行下面命令来启动mirai和YoshinoBot

```shell script
docker-compose up -d
```

文件挂载说明：

staticfiles:用于nginx和bot之间的静态文件共享

bot：
```yaml
# hos的日志目录
- ./data/log:/workdir/log
# yobot生成的配置文件目录
- ./data/yobot_data:/workdir/hoshino/modules/yobot/yobot/src/client/yobot_data
# yobot的静态文件共享
- staticfiles:/workdir/hoshino/modules/yobot/yobot/src/client/public/static/
# yobot的static目录，方便外面的nginx访问
- ./data/static:/workdir/hoshino/modules/yobot/yobot/src/client/public/static/
# hoshino的res目录，方便nginx容器访问来提供http服务
- ./data/res:/workdir/res
# hoshino的配置目录，如果不挂载出来可能导致销毁容器后hoshino的配置丢失
- ./data/conf:/root/.hoshino
# bot的帮助页面
- ./data/res/help.html:/workdir/hoshino/modules/yobot/yobot/src/client/public/template/help.html
```

nginx:
```yaml
# nginx配置文件挂载进nginx容器中，让nginx容器向hoshino提供http访问res目录的服务
- ./nginx.conf:/etc/nginx/conf.d/default.conf
# 挂载hoshino的res目录
- ./data/res:/workdir/res
# yobot的静态文件共享
- staticfiles:/workdir/hoshino/modules/yobot/yobot/src/client/public/static/
```


