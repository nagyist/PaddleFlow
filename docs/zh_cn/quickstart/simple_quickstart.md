# 1.快速上手说明
通过PaddleFlow提供的例子运行简单的工作流，开启PaddleFlow之旅！
在开始之前确认[PaddleFlow服务端部署安装](../deployment/how_to_install_paddleflow.md) 完成
- （必须）安装PaddleFlow客户端， 初始化计算资源，包含（集群、队列初始化），默认使用PaddleFlow管理员用户
- （可选）用默认管理员新增账号
- （必须）初始化本地工作区并注册到PaddleFlow
- （可选）下载示例到指定本地工作区
- 提交示例工作流
- 查看工作流运行详情
- 查看工作流运行日志
# 2.安装PaddleFlow客户端和初始化
## 基于kubernetes
[基于K8s PaddleFlow客户端安装和初始化](../deployment/install_paddleflow_on_k8s.md)
## 基于k3s
[基于K3s PaddleFlow客户端安装和初始化](../deployment/install_paddleflow_on_k3s.md)
# 3.新增账号（可跳过）
```shell
## 3.1新增用户user123，密码 passwd123456
paddleflow user add user123 passwd123456
## 3.2查看是否增加成功
paddleflow user list 
## 3.3更改paddleflow默认配置文件的用户名密码，默认在~/.paddleflow/config文件中
```
# 4.初始化工作区（三选一）
确保本地和PaddleFlow网络互通，可通过curl测试连通性
```shell
curl http://paddleflow-host:8999
```
## 4.1 初始化本地文件（训练数据和代码在本地文件系统中）
本地文件系统当前PaddleFlow为了保障安全性，使用SFTP协议进行传输数据，因此首先确认文件所在机器是否已经开启了SSHD，如果没有则需要安装。

1.创建本地工作区
```shell
mkdir /home/work/paddleflow/workspace/project1/ ##可按需自己规划
```
2.注册工作区到PaddleFlow中
```shell
paddleflow fs create yourworkspace sftp://your-desktop-ip:22/home/work/paddleflow/workspace/project1 -o user=username -o password=xx
## 注意：username password 是用户自己终端的用户名和登录密码，如若ssh端口默认非22，可以自行更换
```
3.检查注册是否成功
```shell
paddleflow fs show yourworkspace
```
## 4.2 初始化S3（训练数据和代码在对象存储中）
待补充，S3的数据

## 4.3 初始化HDFS（训练数据和代码在HDFS中）
待补充
# 5.下载示例到工作区
以本地文件系统为例，下载[example](https://github.com/PaddlePaddle/PaddleFlow/tree/release-0.14.2/example) 代码
```shell
## 组织文件到工作区
cp -rf example /home/work/paddleflow/workspace/project1/
## 此处注意这个路径经过PaddleFlow处理后统一挂载的新路径为：/home/work/paddleflow/mnt/storage
# 因此提交所有的文件最好是通过workdir+相对路径的方式，否则会出现无法找到文件
```
# 6.提交示例工作流
```shell
cd /home/work/paddleflow/workspace/project1/
paddleflow run create -f myworkspace -yp example/pipeline/base_pipeline/run.yaml
## 此处注意这个路径经过PaddleFlow处理后统一挂载的新路径为：/home/work/paddleflow/mnt/storage
# 因此提交所有的文件最好是通过workdir+相对路径的方式，否则会出现无法找到文件导致作业无法成功
# 如若程序中需要使用对应工作区的文件，最好使用绝对路径：sh /home/work/paddleflow/mnt/storage/your-train.sh
# 如果不想要用绝对路径，可以使用相对路径，PaddleFlow内部在执行所有命令前统一会切换到：/home/work/paddleflow/mnt/storage这个目录中，因此可以 sh your-train.sh

## 运行其他示例
## artifact示例
paddleflow run create -f myworkspace -yp example/pipeline/artifact_example/run.yaml
## cache断点续跑示例
paddleflow run create -f myworkspace -yp example/pipeline/cache_example/run.yaml
## 失败处理策略和后处理示例
paddleflow run create -f myworkspace -yp example/pipeline/failure_options_and_post_process_example/run.yaml
```
# 7.查看工作流运行详情
```shell
paddleflow run status run-0000xx
```
# 8.查看工作流运行日志
```shell
## 默认展示工作流第一个节点的日志
paddleflow log show run-0000xx
## 从run status中获取每个job的job_id,如 job-run-0000xx-train-b5a91ac2
paddleflow log show run-0000xx -j job-run-0000xx-train-b5a91ac2
```
# 9.其他所有命令行参考
点击[命令行操作说明](../reference/client_command_reference.md) 获取所有操作命令和示例。





