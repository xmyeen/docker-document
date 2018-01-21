## 注册DCLOUD的会员（使用github账号授权）

## 获得加速脚本链接地址,设置并启动加速
```sh
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://b2339ee9.m.daocloud.io
systemctl restart docker
```

## 拉取gocd-server镜像
```sh
docker pull gocd/gocd-server:v17.12.0
```

## 启动gocd-server镜像：[官方](https://github.com/gocd/docker-gocd-server)
- 简单启动
```sh
docker run --name gocd-server -d -p 8153:8153 -p 8154:8154 gocd/gocd-server:v17.12.0
```

- 使用环境变量可以安装指定插件
```sh
docker run \
-e GOCD_PLUGIN_INSTALL_a-plugin=https://example.com/a-plugin.jar \
-e GOCD_PLUGIN_INSTALL_b-plugin=https://example.com/b-plugin.jar \
gocd/gocd-server:v17.12.0
```

- 使用自定义脚本安装插件
```sh
mkdir -p /godata/plugins/external
curl --location --fail https://example.com/plugin.jar > /path/to/godata/plugins/external/plugin.jar
chown -R 1000 /godata/plugins/external
```

- 执行自定义的entrypoint脚本
```sh
docker run -v /path/to/your/script.sh:/docker-entrypoint.d/your-script.sh ... gocd/gocd-server:v17.12.0
```

- 安装addons扩展
```sh
mkdir -p /path/to/godata/addons
curl --location --fail https://example.com/addon.jar > /path/to/godata/addons/plugin.jar
chown -R 1000 /path/to/godata/addons
```

- 命令行解析ip地址
```sh
docker inspect --format='{{(index (index .NetworkSettings.IPAddress))}}' server
docker inspect --format='{{(index (index .NetworkSettings.Ports "8153/tcp") 0).HostPort}}' server
docker inspect --format='{{(index (index .NetworkSettings.Ports "8154/tcp") 0).HostPort}}' server
```

- 挂载和目录

| 目录              | 说明                                                                   |
| ----------------: | ---------------------------------------------------------------------- |
| /godata           | configuration, pipeline history database, artifacts, plugins, and logs |
| /godata/addons    | addons                                                                 |
| /godata/artifacts | artifacts                                                              |
| /godata/config    | configuration                                                          |
| /godata/db        | database and configuration change history                              |
| /godata/logs      | logs                                                                   |
| /godata/plugins   | plugins                                                                |
| /home/go          | gocd服务执行用户，可以挂载此目录提供SSH私密证书                        |

## 拉取agent镜像
```sh
docker pull gocd/gocd-agent-centos-7
```

## 启动agent镜像：[官方](https://github.com/gocd/docker-gocd-agent-centos-7)
- 简单启动
```sh
docker run -d -e GO_SERVER_URL=... gocd/gocd-agent-centos-7:v17.12.0
```
> **Note**: `GO_SERVER_URL`是一个`/go`结尾的url，如：`https://ip.add.re.ss:8154/go`

- 根据本地的gocd-server部署
```sh
docker run -itd -e GO_SERVER_URL=https://$(docker inspect --format='{{(index (index .NetworkSettings.IPAddress))}}' gocd-server):8154/go gocd/gocd-agent-centos-7:v17.12.0
```
或者
```sh
docker run -itd -e GO_SERVER_URL=https://<ip_of_host_machine>:$(docker inspect --format='{{(index (index .NetworkSettings.Ports "8154/tcp") 0).HostPort}}' angry_feynman)/go gocd/gocd-agent-centos-7:v17.12.0
```

- 自动注册agent
```sh
docker run -d \
        -e AGENT_AUTO_REGISTER_KEY=... \
        -e AGENT_AUTO_REGISTER_RESOURCES=... \
        -e AGENT_AUTO_REGISTER_ENVIRONMENTS=... \
        -e AGENT_AUTO_REGISTER_HOSTNAME=... \
        gocd/gocd-agent-centos-7:v17.12.0
```
> **Note**: 如果`AGENT_AUTO_REGISTER_KEY_*`变量提供，代理将自动被服务核准。详情参见[自动注册文档](https://docs.gocd.org/current/advanced_usage/agent_auto_register.html)

- [docker弹性代理插件](https://github.com/gocd-contrib/docker-elastic-agents)

- 挂载和目录：同服务器版本相同
