[toc]
#Mock Server工具
##1.Mock Server介绍
　伴随着Swagger OpenAPI标准推出，基于Swagger　OpenAPI文档的生态也成长起来，其中mock server属于该生态中的重要组成部分。市面上，各种mock server工具层出不穷，百花齐放，基于各种技术堆栈，语言的mock server都有。基于zdata项目实际情况考虑，现在使用的是prism这个mock server工具。它有以下优点:
- 可以通过url参数控制的方式，动态返回随机模拟数据或者固定模拟数据
- 可以通过url参数控制的方式，返回不同模拟数据，包括HTTP Status状态等
- 部署启动方便，仅仅依赖编写的swagger文档，没有其他数据库依赖
  它同时也有以下缺点:
- 该工具到现在为止没有开源，不能通过阅读源代码方式，做一些定制化修改
- 官方文档偏老，和发布的最新版prism不匹配，只能通过工具的help文档和github的issue列表来查找，解决部分问题。

##2.Prism Mock Server部署启动
通过已下命令启动mock server。
```console
[roxas@roxas]# ./prism_linux_amd64 mock --spec zdata_front_api.yaml  -d &
```
启动后，可以通过在浏览器输入192.168.0.121:4010来访问，检查Mock Server是否启动成功。

##3.Mock Data数据的编辑
　随着项目产品的迭代，API接口可能会新增或者变化，需要后端人员适当添加新的mock data数据，满足前端开发人员的开发需求。现在添加mock data主要分两部分，添加固定模拟数据，添加随机模拟数据。
- 添加固定模拟数据，只需要在examples字段下编辑前端需要的数据，依葫芦画瓢即可。
- 添加随机模拟数据，需要在返回的Reponse中定义的数据结构，进行详细具体的编辑，包括对随机数据取值范围的编辑，取值数量随机编辑，取值格式format的编辑，一些特殊值的编辑，比如时间戳，IP地址等。具体可以参考
[Swagger Spec文档](https://swagger.io/specification/)。