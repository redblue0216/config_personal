# gRPC和Consul安装配置示例


标签（空格分隔）： 施华 版本(beta 0.1)

---

[TOC]
# **Consul安装配置**
+ 从https://www.consul.io/下载最新版本的压缩包zip
+ 在固定文件夹下，解压
```
$cd consul/
$unzip consul_xxxxxxx.zip
```
+ 配置到快捷命令
```
$sudo mv consul /usr/local/bin/
```
+ 测试Consul
```
$consul
```
+ 启动Consul
```
$consul agent -dev ### 默认启动Consul
$consul agent -server -ui -bootstrap --data-dir . -bind=10.2.15.126 -client=0.0.0.0 ### bind一定要连接到本机IP
```
+ 测试端口默认85


# **gRPC安装配置**
+ 定义功能：在calculator.py文件中封装好需要调用的实际函数
```
import math


def square_root(x):
    y = math.sqrt(x)
    return y


if __name__ == '__main__':
    print(square_root(16))
```
+ 设置协议缓冲区：编写calculator.proto,用来定义我们的服务要使用的消息和服务结构
```
syntax = "proto3";

message Number {
    float value = 1;
}

service Calculator {
    rpc SquareRoot(Number) returns (Number) {}
}
```
+ 为Python生成gRPC类：使用特定工具自动生成python类
```
$pip install grpcio
$pip install grpcio-tools
$python -m grpc_tools.protoc -I. --python_out=. --grpc_python_out=. calculator.proto
```
Calculator_pb2.py--包含消息类
（Calculator_pb2,请求/响应变量的编号x和y)
Calculator_pb2_grpc.py--包含服务器和客户端类
（服务器的Calculator_pb2_grpc.CalculatorServicer,客户端的Calculator_pb2_grpc.CalculatorStub）
+ 创建一个gRPC服务器：通过上面配置的所有组件创建一个gRPC服务器calc_server.py
```
import grpc
from concurrent import futures
import time

# import the generated classes
from example import calculator_pb2, calculator
from example import calculator_pb2_grpc


# 创建一个类来定义服务器功能
# 派生自Calculator_pb2_grpc.CalculatorServicer
class CalculatorServicer(calculator_pb2_grpc.CalculatorServicer):

    # 公开 Calculator.square_root
    # 请求和响应属于数据类型,生成Calculator_pb2.Number
    def SquareRoot(self, request, context):
        response = calculator_pb2.Number()
        response.value = calculator.square_root(request.value)
        return response


# 创建一个gRPC服务器
server = grpc.server(futures.ThreadPoolExecutor(max_workers=10))

# 使用生成的函数`add_CalculatorServicer_to_server`
# 将定义的类添加到创建的服务器
calculator_pb2_grpc.add_CalculatorServicer_to_server(CalculatorServicer(), server)

# listen on port 50051
print('Starting server. Listening on port 50051.')
server.add_insecure_port('[::]:50051')
server.start()

# server.start不会阻塞,添加了一个睡眠循环以保持
try:
    while True:
        time.sleep(86400)
except KeyboardInterrupt:
    server.stop(0)
```
启动服务器,监听端口50051
```
$python calc_server.py
```
+ 创建一个gRPC客户端：calc_client.py
```
import grpc
from example import calculator_pb2
from example import calculator_pb2_grpc

# 打开一个gRPC通道
channel = grpc.insecure_channel('localhost:50051')
# 创建一个存根（客户端）
stub = calculator_pb2_grpc.CalculatorStub(channel)

# 创建有效的请求消息
number = calculator_pb2.Number(value=16)

# 调用
response = stub.SquareRoot(number)
print(response.value)
```
运行客户端
```
$python calc_client.py
```
+ 文件结构
```
basic-grpc-python/
├── example/calculator.py          #包含函数的模块
|
├── example/calculator.proto       # protobuf定义文件
|
├── example/calculator_pb2_grpc.py # 为服务器/客户端生成的类
├── example/calculator_pb2.py      # 生成的消息类
|
├── server/calc_server.py              # 服务器
└── client/calc_client.py              # 客户端
```






