# smarthaven

## Notice
In order to project can better applicable to complex of business scenarios. We have made changes to Smarthaven:
The business in the project is stripped, and the functions are decoupled to separate five independent individuals,they are [Metadb](https://github.com/vmeta42/metadb), [Metaedge](https://github.com/vmeta42/metaedg), [Metatoc](https://github.com/vmeta42/metato), [Metaai](https://github.com/vmeta42/metaai), [Metashow](https://github.com/vmeta42/metashow).

After decoupling, the project flexibility is greatly improved, making the project more lightweight and convenient for community partners to choose appropriate projects according to their own business needs.

If you made it here, I assume you are somewhat interested in smarthaven - so checkout the site at [Meta42,](https://github.com/vmeta42) you are welcome to pull issues, or even better, contribute on GitHub.

## 通知
为了项目能更好的适用于广泛的业务场景。我们对smarthaven进行的改革：将项目中业务进行剥离，功能进行解耦合，独立出5个项目。改革后的项目命名**Meta42**，包括:Metadb、Metaedge、Metatoc、Metaai、Metashow。

| 项目名称                                         | 功能介绍                                                                                                                                                                              | 2022年4月21日                                                                                    | 2022年Q2~Q4                                                                   |
| :---------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------- | :---------------------------------------------------------------------------- |
| [Metadb](https://github.com/vmeta42/metadb)     | Metadb是元数据管理平台，依托比较成熟的蓝鲸数据管理开源项目。互联主要贡献基于IDC管理经验的数据中心、机柜、设备以及传感器的模型，是Meta42项目的核心组件。                                          | 简化架构，使用云原生架构；简化组件部署                                                             | 发布其他管理模型，比如CRM、HR、SCM等业务模型。                                   |
| [Metaedge](https://github.com/vmeta42/metaedge) | 边缘计算和Flink实时计算的结合，在边缘侧采用云原生和实时计算，解决分布式IDC数据实时计算，提高事件响应速度                                                                                      | 发布Flink on KubeEdge                                                                           | 发布DevOps套件，配合Flink以perJob方式运行在边缘计算节点上探索边缘Flink集群算力控制 |
| [Metatoc](https://github.com/vmeta42/metatoc)   | 采用云原生技术在边缘侧进行物联网数据实时采集；支持AI模型实时下发到边缘侧；结合云原生区块链开源项目Violas对数据交易、确权和追溯。ToC(Thing to Chain)项目是机房运营数据去中心化鉴权和追踪的最佳实践。 | 支持边缘侧数据采集；链原生确认数据所有权；支持数据所有权转让上链；支持数据转让追溯；支持边缘智能模型下发 | 支持链上数据确权                                                               |
| [Metaai](https://github.com/vmeta42/metaai)     | 基于对大量电池故障指标数据集的训练，来对IDC机房UPS电池故障进行预测                                                                                                                         | 发布基于Pythoch on Kubernetes 的训练模型预测准确率达到86%                                          | 实现自动化标注；实现迁移学习                                                    |
| [Metashow](https://github.com/vmeta42/metashow) | 主要是开源网络监控模型和快速实施的云原生能力；丰富告警功能，为企业提供网络链路的性能监控服务，助力企业提升异常问题排查与解决的效率；采用GitOps理念，加速和简化应用程序部署和运维任务。               | 发布云原生部署能力；常用网络监控模型。                                                             | 发布事件驱动告警能力；发布事件自动化运维能力                                      |

 解耦后，将大大提高项目灵活性，使项目更加轻量级，同时也方便社区小伙伴根据自身业务需求，选择合适的项目。此后，如需了解项目的发展，请继续关注[Meta42](https://github.com/vmeta42)，欢迎大家提issues，或者成为我们贡献者！

---

With the evolution of cloud computing and cloud-native technologies, more and more services, larger cluster sizes, and more complex applications make data centers bigger and bigger. TPOS, PUE and server density pose great challenges to the management level of data centers. 

The "Smarthaven" is an important part of IDC's open source management projects, using cloud native, big data, and AI technology. We want to share:

<img alt="Cloud Native IDC" src="docs/img/Cloud_Native_IDC.png">

## Features
* Collect and manage data, especially background audio information. The data is processed on the edge side.Saving 60% bandwidth and speed up 10 times. (Kubeedge/Kubernetes)
* Manage 1M+ sensors and 10TB+/day with Bigdata/IoT (Flink and Kubeedge)
* Perform edge detection fault alarm reasoning and prediction with AI. Saving 2%+ energy.(Kubeflow)
* Transmit and distribute AI models in 5 seconds. (Nats/Cefco)

## Architecture
[Architecture](docs/architecture.md) 

## Core Technologies
* [Cefco](cefco) 
* [Flink on Kubernetes](flinkonk8s)
* [Pytorch on Kubernetes](pytorchonk8s)
* [Promethus + Grafana](monitoring)
* [DevOps + GitOps](devgitops)

## Design Philosophy
* Opensource
* Everything as Code
* CRD
* Cloud Native
* Technology neutral and open
* DevOps (GitOps)

## Community
[Online Community Meeting (open)] (Coming soon.)  
[Online Community Meeting (VNET internal link)] (Coming soon.)  

## Contribution
Smarthaven is developed in the open, and is constantly being improved by our **users, contributors, and maintainers**. It is because of you that we can bring great software to the community.

If you are interested in contributing to smarthaven projects, please read this [Contribution Guide Document](CONTRIBUTING.md). 

## License
Copyright (c) <2021> <VNET Group>

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Smarthaven"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Partners
Inspur Software Co.

21vianet Blue Cloud

Tenxcloud

etc.

## Supporting Technologies
...(Coming soon.)

## About
...(Coming soon.)
