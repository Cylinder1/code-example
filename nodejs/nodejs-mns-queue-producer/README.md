# Nodejs14 消息服务 MNS 队列模型生产者示例

本示例为您展示了 nodejs runtime 的 [消息服务MNS](https://help.aliyun.com/document_detail/27414.html) 队列模型生产者示例。
本示例使用了MNS 的队列模型作为示例，与函数计算中的 MNS 队列触发器一起实现了消息服务的生产者-消费者模型。
MNS 的配置在函数的环境变量配置中（参考s.yaml)。

> 若使用主题模型，请参考nodejs-mns-topic-producer示例。

MNS 官方没有 Nodejs SDK , 请多参考官方 [MNS队列发送消息API](https://help.aliyun.com/document_detail/35134.html)

## 准备开始
- 一个可用的mns队列，可参考MNS官方文档[队列模型快速入门-创建队列](https://help.aliyun.com/document_detail/34417.html) 创建。
- 有MNS权限的RAM用户
  - 建议直接使用函数计算默认的角色 AliyunFCDefaultRole
  - 也可参考MNS官方文档[开通消息服务MNS并授权](https://help.aliyun.com/document_detail/27423.html)，函数计算需要该RAM密钥访问MNS队列。
- [可选] 安装并配置 Serverless Devs 工具。（https://help.aliyun.com/document_detail/195474.html）

## 快速开始

### 方式一、使用控制台创建

#### 1. 安装依赖和部署代码包

```shell
# 安装依赖到 /code 目录
cd code && npm install 
# 打包文件
cd code && zip -r nodejs-mns-queue-producer.zip *
```

#### 2. 创建函数
选择服务（或创建服务）后，单击创建函数，如图所示
- 选择 `从零开始创建`
- 填入函数名称
- 选择运行环境 Node.js 14
- 选择函数触发方式：通过事件请求触发
- 其他设置使用默认

> 详细创建函数流程见文档: [使用控制台创建函数](https://help.aliyun.com/document_detail/51783.html)

#### 3. 设置 initializer 回调函数配置和环境变量配置

回调函数配置：
![img_2.png](assets/20220719164834.jpg)

函数环境变量配置：
![img_2.png](assets/20220719164825.jpg)

#### 4. 设置服务角色配置
在编辑服务页面，选择服务角色，推荐选择函数计算默认设置的角色 AliyunFCDefaultRole。
也可以自定义服务角色，并添加权限策略AliyunMNSFullAccess，或自定义权限策略，详情见文档 [授权策略和示例](https://help.aliyun.com/document_detail/27447.html)
![img_3.png](assets/20220719171807.jpg)

#### 5. 测试函数

返回结果如下所示
```bash
succ
```

### 方式二、使用 Serverless Devs 工具编译部署

#### 1. 修改 s.yaml 配置
- 根据需要修改 access 配置
- 修改 environmentVariables 配置，填入 MnsEndpoint 和 QueueName

```yaml
        environmentVariables:
          MnsEndpoint: "http://{AccountID}.mns.{Region}.aliyuncs.com" # 设置MNS访问地址
          QueueName: "fc-example" # 设置MNS队列名称
```

#### 2. 安装依赖并部署

部署代码

```bash
s deploy
```
s.yaml文件中以下配置会安装所需依赖

```yaml
    actions:
      pre-deploy:
        - run: npm install  # 下载依赖
          path: ./code
```

#### 3. 调用测试

```shell
s invoke
```

调用函数时收到的响应如下所示：

```bash
========= FC invoke Logs begin =========
FC Invoke Start RequestId: b24f8d99-6489-4fbb-b166-14e99c79xxxx
2022-07-27T07:47:04.266Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] method: POST
2022-07-27T07:47:04.266Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] request headers: {"date":"Wed, 27 Jul 2022 07:47:04 GMT","x-mns-version":"2015-06-06","content-type":"application/xml;charset=utf-8","content-length":164,"content-md5":"Mzc4NGZlZGFmYTIwMjM4MmUyZTg0xxxxxxxxxxxxxxxx","authorization":"MNS STS.NUwwV5Nmmxxxxxxxxxxxxxxxx:2pfMiaTGk8OIxxxxxxxxxxx"}
2022-07-27T07:47:04.266Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] request body: <?xml version="1.0" encoding="UTF-8"?><Message xmlns="http://mns.aliyuncs.com/doc/v1/"><MessageBody>hello mns</MessageBody><DelaySeconds>20</DelaySeconds></Message>
2022-07-27T07:47:04.417Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] statusCode 201
2022-07-27T07:47:04.417Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] response headers: {"server":"AliyunMQS","date":"Wed, 27 Jul 2022 07:47:04 GMT","content-type":"text/xml;charset=utf-8","content-length":"279","connection":"keep-alive","x-mns-version":"2015-06-06","x-mns-request-id":"62E0ED78333842C815F2xxxx"}
</Message>Handle>7-wc8JGKHzcIz5LULFM9rz4utz7i5ghxxxxxx</ReceiptHandle>] response body: <?xml version="1.0" ?>
2022-07-27T07:47:04.418Z b24f8d99-6489-4fbb-b166-14e99c79xxxx [verbose] Send message succ: MessageID:EB0A77CA80764167483B8948xxxxxxxx,BodyMD5:0C91FF67AF5B07A61C82F0DDxxxxxxxx
FC Invoke End RequestId: b24f8d99-6489-4fbb-b166-14e99c79xxxx

Duration: 254.87 ms, Billed Duration: 255 ms, Memory Size: 128 MB, Max Memory Used: 51.38 MB
========= FC invoke Logs end =========

FC Invoke instanceId: c-62e0ec52-b9a3ba9fda434082xxxx

FC Invoke Result:
succ


End of method: invoke

```

## 注意事项
1. MNS消息服务和函数计算建议部署在同一个地域
