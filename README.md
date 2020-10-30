[TOC]

# 思路

分离运维领域和应用领域

运维领域为: 运维功能, 对外展现为CMDB、指令库(内/外部融合)、指令编排

应用领域为: 研发领域的各个领域(如: 开发、测试、产品), 对外展现为变更系统和运维系统

## 落地可持续运作的运维领域([laas-soa-operate](https://github.com/laashub-soa/laas-soa-operate))

抽离数据+指令, 编辑时数据使用CMDB作为载体, 数据查询/增加/修改/删除/订阅变更, 在线直接编辑生成CMDB数据, 数据具有版本特点(溯源: 版本/修改人), 使用时后台直接使用数据即可; 指令需要和agent进行联动, agent切入到目标环境, 接口指令执行并返回结果, 系统可以在任意环境部署, 但是指令会自动部署在需要的环境中, 与系统保持联系(主动/被动/数据订阅); 

运维进行在线运维, 整理脚本为指令, 抽离数据到数据模型, 并沉淀到CMDB中, 由分离了数据的指令沉淀形成指令库, 指令[需要]声明数据: 依赖的数据源、修改的数据源、订阅的数据源、触发的数据源;

关于数据, 需要对敏感数据进行分别加密(rsa加密密钥, aes加密原文), 支持二进制文件

同时支持数据模型的横向领域的数据模型建设, 数据状态变更、数据组合

## 构思

### 数据

从用户层看来是以数据为主视角, 数据驱动指令; 从执行层看来是以指令为主视角, 指令驱动数据

数据就是单表的数据模型的生成, 表名、表描述、字段、字段类型、字段描述、引用表-字段名称, 使用的地方就是单表的增删改查, 数据插入时可以作为默认值, 数据使用可以进行限制以及设置默认值

### 指令

分为三块

1、数据依赖

data_require.json

```
{
	"data1": [{
		"": ""
	}]
}
```

2、数据变更

data_change.json

```
{
	"insert": [{"data1": [{"col1": "value1"}]}],
	"update": [{"data_name": "data1", "data": {"col1": "value1"}, "data_condition": {"col1": "value1"}}],
	"delete": [[{"data_name": "data1", "data_condition": {"col1": "value1"}}],
}
```

3、指令集

```
文件路径
文件内容
```

一个启动文件(main.sh)和多个伴随文件



在执行时会将状态、日志会增量收集同步到服务器上(filebeat)





## 落地可持续运作的应用领域([laas-soa-apply](https://github.com/laashub-soa/laas-soa-apply))

用户、角色、权限、流程、表单(与CMDB联动)、动作;

由表单供给数据, 动作关联运维指令, 实现流程审批点环节触发指定目标的指定数据的指定动作执行, 同时支持动作执行器前进行触发时间的延迟判定

同时提供回滚/补偿功能

同时展示执行日志、metrics/tracing数据

同时可以数据开放、数据查看、数据配置、关注数据

在变更时自动作为作为多流程组合

## 同时落地可持续运作的监控领域([laas-soa-monitor](https://github.com/laashub-soa/laas-soa-monitor))

基于CMDB会自动落地监控系统, 完成metrics和tracing数据采集、展示、分析、告警, metrics为数据状态指标, 软件-功能-指标的状态及性能指标, tracing为数据过程指标, 软件-功能-指标的过程数据

一方面是运维软件本身的运行数据, 另一方面是运维环境本身的运作数据, 实现系统分析/决策, 例如分析资源使用情况进行调整, 根据当前运行数据进行推断预期数据

与sli-slo-sla思路保持一致

同时, log(日志)也是同等重要的

## 同时落地可持续运作的安全领域([laas-soa-security](https://github.com/laashub-soa/laas-soa-security))

操作数据审计、数据监测

## 研发(暂略)([laas-soa-develop](https://github.com/laashub-soa/laas-soa-develop))

暂略(支撑研发流程)

使用工具:

​	源码管理: gitlab

​	文档管理: iwiki

​	okr管理: tapd

​	api文档管理: showdoc

基于这种运维系统, 支撑运维在运维系统中进行常规运维, 在常规运维的基础上进行自动化改造, 运维开发进行填充骨骼, 使用人员使用骨骼思路, 关注自己需要的业务, 基于审批流实现自动化使用

建设难度在于思路转变, 由运维开发完成提供运维思路变成支撑运维, 目前运维开发承担了太多角色(web开发/运维)

后续发展: 

​	运维填充运维内容, 研究运维领域技术并应用, 完成深度和广度的填充

​	运维开发完善运维系统

