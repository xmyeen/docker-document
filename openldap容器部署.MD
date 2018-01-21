## 注册DCLOUD的会员（使用github账号授权）

## 获得加速脚本链接地址,设置并启动加速
```sh
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://b2339ee9.m.daocloud.io
systemctl restart docker
```

## 拉取openldap镜像
```sh
docker pull osixia/openldap:1.1.11
```

## 启动openldap镜像：[官方](https://github.com/osixia/docker-openldap)