通用日志系统
===============

主要实现技术：

 + Thinkphp5.1
 + Ant Design Pro
 + nginx
 + mongodb
 + redis
 + kafka
 + supervisor
 + docker
 + docker-compose

## 安装

> 安装前，机器必须安装docker、和docker-compose环境

~~~
docker-compose -d up
~~~

# 查看容器运行状态
state都处于up,则证明运行良好
~~~
[root@localhost common_log]# docker-compose ps
        Name                       Command               State                      Ports
-------------------------------------------------------------------------------------------------------------
commonlog_mongo_1       /entrypoint.sh /run.sh           Up      27017/tcp
commonlog_php_nginx_1   /usr/sbin/init                   Up      0.0.0.0:8888->80/tcp, 0.0.0.0:9002->9001/tcp
commonlog_redis_1       docker-entrypoint.sh redis ...   Up      6379/tcp
[root@localhost common_log]#
~~~


然后就可以在浏览器中访问

~~~
http://localhost:8888
~~~

需要修改访问端口的，只需编辑docker-compose.yml的ports节点

完成搭建

# 简单使用教程

- 运行起来之后，就可以访问后台了

```
访问：http://ip:8888/
```

- 默认配置

```
appid：admin
appsecret：linzhou
```

- 相关接口

1. 获取access_token接口
```
GET：http://ip:8888/api/getAccessToken
参数：
appid：admin
appsecret：linzhou
返回结果：
{
    "message": "获取成功",
    "code": 200,
    "info": {
        "access_token": "XXXXXXXXXXXX",
        "token_type": "Bearer",
        "expires_in": 7200
    }
}
```

2. 记录日志接口
```
POST：http://ip:8888/api/saveLog
参数：
{
    "authorization":"XXXXXXXXXXXX",
    "project_name": "test",
    "level": "error", //日志等级
    "message":"日志摘要信息",
    "other":"其他信息",
    "ip":"127.0.0.1/127.0.0.1",
    "create_time":"2019-11-11 18:18:18"
}
返回结果：
{
    "message": "保存成功",
    "code": 200,
    "info": []
}
```

3. kafka方式
```
            $param = [
                'project_name' => config('app.app_name') ?: 'common_log',
                'level' => $level,
                'ip' => ($_SERVER['SERVER_ADDR'] ?? '127.0.0.1') . '/' . request()->ip(),
                'message' => $message,
                'other' => $other,
                'create_time' => date('Y-m-d H:i:s'),
            ];
            if (config('app.common_log.use_kafka')) {
                KafkaHelper::instance(config('app.common_log.kafka_brokers'), 'common_log')->produce($param);
                return true;
            }
```
