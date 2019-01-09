# RabbitMQ 安装及使用

## 依赖版本

- Centos 7.3
- [RabbitMQ 3.7.8](https://hub.docker.com/_/rabbitmq)
- Docker 18.06.1-ce

## 安装与基本配置

### 1. 拉取镜像并启动容器

``` bash
# 拉取镜像
docker pull rabbitmq:3.7.8-management-alpine

# amqp端口5672，web后台端口15672，mqtt端口1883，websocket端口15675
docker run -d -p 5672:5672 -p 15672:15672 -p 1883:1883 -p 15675:15675 -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=q1w2e3r4 --name rabbit rabbitmq:3.7.8-management-alpine

# 待启动成功后进入容器，
docker exec -it rabbit /bin/bash

# 增加guest用户
rabbitmqctl add_user guest 123456
rabbitmqctl set_permissions -p / guest ".*" ".*" ".*"
rabbitmqctl set_user_tags guest management

# 设置mqtt默认连接用户为guest
vi /etc/rabbitmq/rabbitmq.conf
mqtt.default_user = guest
mqtt.default_pass = 123456

# 启动rabbitmq_web_mqtt插件
rabbitmq-plugins enable rabbitmq_web_mqtt

# 退出容器
exit
```

### 2. mqtt web 客户端连接

``` html
<script src="https://cdnjs.cloudflare.com/ajax/libs/paho-mqtt/1.0.1/mqttws31.min.js" type="text/javascript"></script>
<script src="./config.js" type="text/javascript"></script>
<script src="https://cdn.bootcss.com/crypto-js/3.1.9-1/crypto-js.min.js"></script>
<script type="text/javascript">
var mqtt;
var reconnectTimeout = 2000;
var username = accessKey;
var password = CryptoJS.HmacSHA1(groupId, secretKey).toString(CryptoJS.enc.Base64);
function MQTTconnect() {
	mqtt = new Paho.MQTT.Client(
          "121.199.50.180", //MQTT 域名
          15675, //WebSocket 端口，如果使用 HTTPS 加密则配置为443,否则配置80
          "/ws",
          "myclientid_" + parseInt(Math.random() * 100, 10) //客户端 ClientId
        );
        var options = {
          timeout: 3,
          onSuccess: onConnect,
          mqttVersion: 4,
          cleanSession: true,
          onFailure: function(message) {
            setTimeout(MQTTconnect, reconnectTimeout);
          }
        };
        mqtt.onConnectionLost = onConnectionLost;
        mqtt.onMessageArrived = onMessageArrived;
        // if (username != null) {
        //   options.userName = "mqtt-test";
        //   options.password = "mqtt-test";
        //   options.useSSL = useTLS; //如果使用 HTTPS 加密则配置为 true
        // }
        mqtt.connect(options);
      }
      function onConnect() {
        // Connection succeeded; subscribe to our topic
        // mqtt.subscribe(topic, { qos: 0 });
        var i = 0;

        setInterval(function() {
          var str = "Hello, this is " + i++;

          message = new Paho.MQTT.Message(str); //set body
          message.destinationName = topic; // set topic
          message.qos = 1;
          mqtt.send(message);
          console.log(str);
        }, 2000);
      }
      function onConnectionLost(response) {
        setTimeout(MQTTconnect, reconnectTimeout);
      }
      function onMessageArrived(message) {
        var topic = message.destinationName;
        var payload = message.payloadString;
        console.log("recv msg : " + topic + "   " + payload);
      }
      MQTTconnect();
    </script>
```

