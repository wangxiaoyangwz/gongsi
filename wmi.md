WMI：像写SQL般去写程序
#### 1. 什么是wmi？可以做什么？
WMI技术一个非常大的优势就是可以`访问远程计算机`
WMI的全称是Windows Management Instrumentation，即Windows管理工具
#### 2. wmi怎么去管理计算机上的资源
Windows操作系统中管理数据和操作的基础模块。我们可以通过WMI脚本或者应用程序去管理本地或者远程计算机上的资源(比如用go语言编写的wmi-exporter)
可用于获取诸如CPU序列号和硬盘序列号等信息
`它提供`了一个通过操作系统、网络和企业环境去`管理`本地或远程计算机的`统一接口集`。应用程序和脚本语言`使用这套接口集`去完成任务，而不是直接通过Windows API
#### 3. 他提供的接口集和windows API的区别，为什么不直接使用windows API?
Windows API:不支持远程调用或者脚本调用的
wmi:可以通过WMI脚本或者应用程序去管理本地或者远程计算机上的资源
#### 4. wmi遵守的行业标准？
WBEM：wmi是对wbem模型的实现，webm【Web-Based Enterprise Management】
#### 5. 什么是wbem？
行业倡议：企业环境下`访问管理信息`的标准技术,`规范`了企业网络中`受管资源的描述和使用`\
#### 6. wmi组件：CIM，MOF
CIM：【common information Modal】公共信息模型
MOF: 【Managed Object Format】   托管对象格式
WMI使用了这两个标准实现相关功能
#### 7. 特点
不同的系统中，调用相同的操作将会获得不同的信息（得到的子集不同）
#### 8. wmi技术
`获取信息`：
    “获取信息”需要`WMI Classes`(以后称为WMI类), 
`提供数据`：
    提供数据”需要`WMI Provider`(以后称为WMI提供者)
    ![这里写图片描述](//img-blog.csdn.net/20180322152735729?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L2dpdGh1Yl80MDA5NDEwNQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

#### wmi框架
1. wmi consumers【wmi使用者】
位于WMI构架的最顶层，是WMI技术使用的载体，C++可以通过COM【CIM Object Manager】技术直接与下层通信
`Scripts`:比如用go语言写的wmi-exporter去获取windows的信息的脚本，其中需要支持WMI Scripting API， 间接与下层通信
`.net平台语言`:需要使用System.Management域相关功能与下层通信
#### wmi使用者怎么通过操作获取数据
这些WMI的使用者，可以查询、枚举数据，也可以运行Provider的方法，还可以向WMI订阅消息。当然这些数据操作都是要有相应的Provider来提供。
####  WMI Infrastructure(WMI基础结构)
 WMI基础结构是Windows系统的系统组件。它包含两个模块：包含WMI Core(WMI核心)的WMI Service(WMI服务)(Winmgmt)和WMI Repository(WMI存储库)。
#####  WMI Repository(WMI存储库)。
WMI Namespace(WMI命名空间)组织起来的，
`系统启动`，WMI服务会创建诸如`root\default、root\cimv2和root\subscription`等WMI命名空间，预安装一部分`WMI类的定义信息`到这些命名空间中，
`其他命名空间`是在`操作系统或者产品调用`有关`WMI提供者`(WMI Provider)时才被创建出来的
`WMI存储库是用于存储WMI静态数据的存储空间。优先介绍访问WMI存储库来获取有用的信息`

#### WMI服务
 WMI服务扮演着WMi提供者、管理应用和WMI存储库之间的`协调者角色`

 待续...
