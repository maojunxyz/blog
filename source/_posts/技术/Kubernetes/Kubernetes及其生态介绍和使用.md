---
title: Kubernetes及其生态介绍和使用
date: 2024/04/11 06:23:20
updated: 2024/04/11 06:23:20
categories:
- [技术,Kubernetes]
tags:
- Kubernetes
- Docker
---

## Kubernetes简介

Kubernetes是一个全新的基于容器技术的分布式系统支撑平台。是Googe开源的容器集群管理系统(谷歌内部:Borg)。在Docker技术的基础上，为容器化的应用提供部署运行、资源调度、服务发现和动态伸缩等一系列完整功能，提高了大规模容器集群管理的便捷性。并且具有完备的集群管理能力，多层次的安全防护和准入机制、多租户应用支撑能力、透明的服务注册和发现机制、內建智能负载均衡器、强大的故障发现和自我修复能力、服务滚动升级和在线扩容能力、可扩展的资源自动调度机制以及多粒度的资源配额管理能力。





## Kubernetes和Docker的关系

- Docker提供容器的生命周期管理和，Docker镜像构建运行时容器。它的主要优点是将将软件/应用程序运行所需的设置和依赖项打包到一个容器中，从而实现了可移植性等优点。
- Kubernetes用于关联和编排在多个主机上运行的容器。



## Minikube、Kubect、Kubelet？

- Minikube是一种可以在本地轻松运行一个单节点Kubernetes群集的工具。
- Kubect是一个命令行工具，可以使用该工具控制Kubernetes集群管理器，如检查群集资源，创建、删除和更新组件，查看应用程序。
- Kubelet是一个代理服务，它在每个节点上运行，并使从服务器与主服务器通信。



## Kubernetes的部署方式

- kubeadm：也是推荐的一种部署方式；
- 二进制;
- minikube：在本地轻松运行一个单节点Kubernetes群集的工具。

## Kubernetes集群管理

在集群管理方面，Kubernetes将集群中的机器划分为一个Master节点和一群工作节点Node。其中，在Master节点运行着集群管理相关的一组进程kube-apiserver、kube-controer-manager和kube-scheduer，这些进程实现了整个集群的资源管理、Pod调度、弹性伸缩、安全控制、系统监控和纠错等管理能力，并且都是全自动完成的。

## Kubernetes的优势和特点

Kubernetes作为一个完备的分布式系统支撑平台，其主要优势：

- 容器编排
- 轻量级
- 开源
- 弹性伸缩
- 负载均衡

Kubernetes常见场景：

- 快速部署应用
- 快速扩展应用
- 无缝对接新的应用功能
- 节省资源，优化硬件资源的使用

Kubernetes相关特点：

- 可移植:支持公有云、私有云、混合云、多重云(muti-coud)。
- 可扩展:模块化,、插件化、可挂载、可组合。
- 自动化:自动部署、自动重启、自动复制、自动伸缩/扩展。



## Kubernetes的缺点或不足

- 安装过程和配置相对困难复杂。
- 管理服务相对繁琐。
- 运行和编译需要很多时间。
- 它比其他替代品更昂贵。
- 对于简单的应用程序来说，可能不需要涉及Kubernetes即可满足。



## Kubernetes基础概念

- master：k8s集群的管理节点，负责管理集群，提供集群的资源数据访问入口。拥有Etcd存储服务(可选)，运行ApiServer进程，ControerManager服务进程及Scheduer服务进程。
- node(worker)：Node(worker)是Kubernetes集群架构中运行Pod的服务节点，是Kubernetes集群操作的单元，用来承载被分配Pod的运行，是Pod运行的宿主机。运行dockereninge服务，守护进程kuneet及负载均衡器kube-proxy。
- pod：运行于Node节点上，若干相关容器的组合。Pod内包含的容器运行在同一宿主机上，使用相同的网络命名空间、IP地址和端口，能够通过ocahost进行通信。Pod是Kurbernetes进行创建、调度和管理的最小单位，它提供了比容器更高层次的抽象，使得部署和管理更加灵活。一个Pod可以包含一个容器或者多个相关容器。
- labe：Kubernetes中的abe实质是一系列的Key/Vaue键值对，其中key与vaue可自定义。labe可以附加到各种资源对象上，如Node、Pod、Service、RC等。一个资源对象可以定义任意数量的labe，同一个labe也可以被添加到任意数量的资源对象上去。Kubernetes通过abeSeector(标签选择器)查询和筛选资源对象。
- RepicationControer：RepicationControer用来管理Pod的副本，保证集群中存在指定数量的Pod副本。集群中副本的数量大于指定数量，则会停止指定数量之外的多余容器数量。反之，则会启动少于指定数量个数的容器，保证数量不变。RepicationControer是实现弹性伸缩、动态扩容和滚动升级的核心。
- Depoyment：Depoyment在内部使用了RS来实现目的，Depoyment相当于RC的一次升级，其最大的特色为可以随时获知当前Pod的部署进度。
- HPA(HorizontaPodAutoscaer)：Pod的横向自动扩容，也是Kubernetes的一种资源，通过追踪分析RC控制的所有Pod目标的负载变化情况，来确定是否需要针对性的调整Pod副本数量。
- Service：Service定义了Pod的逻辑集合和访问该集合的策略，是真实服务的抽象。Service提供了一个统一的服务访问入口以及服务代理和发现机制，关联多个相同abe的Pod，用户不需要了解后台Pod是如何运行。
- Voume：Voume是Pod中能够被多个容器访问的共享目录，Kubernetes中的Voume是定义在Pod上，可以被一个或多个Pod中的容器挂载到某个目录下。
- 
  Namespace：Namespace用于实现多租户的资源隔离，可将集群内部的资源对象分配到不同的Namespace中，形成逻辑上的不同项目、小组或用户组，便于不同的Namespace在共享使用整个集群的资源的同时还能被分别管理。




## Kubernetes集群相关组件

KubernetesMaster控制组件，调度管理整个系统(集群)，包含如下组件:

- KubernetesAPIServer：作为Kubernetes系统的入口，其封装了核心对象的增删改查操作，以RESTfuAPI接口方式提供给外部客户和内部组件调用，集群内各个功能模块之间数据交互和通信的中心枢纽。
- KubernetesScheduer：为新建立的Pod进行节点(node)选择(即分配机器)，负
  责集群的资源调度。
- KubernetesControer：负责执行各种控制器，目前已经提供了很多控制器来保证Kubernetes的正常运行。
- RepicationControer：管理维护RepicationControer，关联Repication Controer和Pod，保证RepicationControer定义的副本数量与实际运行Pod数量一致。
- NodeControer：管理维护Node，定期检查Node的健康状态，标识出(失效|未失效)的Node节点。
- NamespaceControer：管理维护Namespace，定期清理无效的Namespace，包括Namesapce下的API对象，比如Pod、Service等。
- ServiceControer：管理维护Service，提供负载以及服务代理。
- EndPointsControer：管理维护Endpoints，关联Service和Pod，创建Endpoints为Service的后端，当Pod发生变化时，实时更新Endpoints。
- ServiceAccountControer：管理维护ServiceAccount，为每个Namespace创建默认的ServiceAccount，同时为ServiceAccount创建ServiceAccountSecret。
- PersistentVoumeControer：管理维护PersistentVoume和PersistentVoumeCaim，为新的PersistentVoumeCaim分配PersistentVoume进行绑定，为释放的PersistentVoume执行清理回收。
- DaemonSetControer：管理维护DaemonSet，负责创建DaemonPod，保证指定的Node上正常的运行DaemonPod。
- DepoymentControer：管理维护Depoyment，关联Depoyment和RepicationControer，保证运行指定数量的Pod。当Depoyment更新时，控制实现RepicationControer和Pod的更新。
- JobControer：管理维护Job，为Jod创建一次性任务Pod，保证完成Job指定完成的任务数目
- PodAutoscaerControer：实现Pod的自动伸缩，定时获取监控数据，进行策略匹配，当满足条件时执行Pod的伸缩动作。



## 简述Kubernetes RC的机制？

RepicationControer用来管理Pod的副本，保证集群中存在指定数量的Pod副本。当定义了RC并提交至Kubernetes集群中之后，Master节点上的ControerManager组件获悉，并同时巡检系统中当前存活的目标Pod，并确保目标Pod实例的数量刚好等于此RC的期望值，若存在过多的Pod副本在运行，系统会停止一些Pod，反之则自动创建一些Pod。

## RepicaSet和RepicationControer的区别？

RepicaSet和RepicationControer类似，都是确保在任何给定时间运行指定数量的Pod副本。不同之处在于RS使用基于集合的选择器，而RepicationControer使用基于权限的选择器。

## kube-proxy的作用

kube-proxy运行在所有节点上，它监听apiserver中service和endpoint的变化情况，创建路由规则以提供服务IP和负载均衡功能。简单理解此进程是Service的透明代理兼负载均衡器，其核心功能是将到某个Service的访问请求转发到后端的多个Pod实例上。

## kube-proxyiptabes的原理

Kubernetes从1.2版本开始，将iptabes作为kube-proxy的默认模式。iptabes模式下的kube-proxy不再起到Proxy的作用，其核心功能：通过APIServer的Watch接口实时跟踪Service与Endpoint的变更信息，并更新对应iptabes规则，Cient的请求流量则通过iptabes的NAT机制“直接路由”到目标Pod。



## kube-proxy ipvs原理

IPVS在Kubernetes1.11中升级为GA稳定版。IPVS则专门用于高性能负载均衡，并使用更高效的数据结构(Hash表)，允许几乎无限的规模扩张，因此被kube-proxy采纳为最新模式。

在IPVS模式下，使用iptabes的扩展ipset，而不是直接调用iptabes来生成规则链。iptabes规则链是一个线性的数据结构，ipset则引入了带索引的数据结构，因此当规则很多时，也可以很高效地查找和匹配。

可以将ipset简单理解为一个IP(段)的集合，这个集合的内容可以是IP地址、IP网段、端口等，iptabes可以直接添加规则对这个“可变的集合”进行操作，这样做的好处在于可以大大减少iptabes规则的数量，从而减少性能损耗。

## kube-proxyipvs和iptabes的异同？

iptabes与IPVS都是基于Netfiter实现的，但因为定位不同，二者有着本质的差别：iptabes是为防火墙而设计的；IPVS则专门用于高性能负载均衡，并使用更高效的数据结构(Hash表)，允许几乎无限的规模扩张。

与iptabes相比，IPVS拥有以下明显优势：

- 为大型集群提供了更好的可扩展性和性能；
- 支持比iptabes更复杂的复制均衡算法(最小负载、最少连接、加权等)；
- 支持服务器健康检查和连接重试等功能；
- 可以动态修改ipset的集合，即使iptabes的规则正在使用这个集合。

## Kubernetes的静态Pod


静态pod是由kubeet进行管理的仅存在于特定Node的Pod上，他们不能通过APIServer进行管理，无法与RepicationControer、Depoyment或者DaemonSet进行关联，并且kubeet无法对他们进行健康检查。静态Pod总是由kubelet进行创建，并且总是在kubelet所在的Node上运行。

## Kubernetes中Pod的状态

- Pending：APIServer已经创建该Pod，且Pod内还有一个或多个容器的镜像没有创建，包括正在下载镜像的过程。
- Running：Pod内所有容器均已创建，且至少有一个容器处于运行状态、正在启动状态或正在重启状态。
- Succeeded：Pod内所有容器均成功执行退出，且不会重启。
- Faied：Pod内所有容器均已退出，但至少有一个容器退出为失败状态。
- Unknown：由于某种原因无法获取该Pod状态，可能由于网络通信不畅导致。



## Kubernetes创建Pod的流程？

Kubernetes中创建一个Pod涉及多个组件之间联动，主要流程如下：

1. 客户端提交Pod的配置信息(可以是yam文件定义的信息)到kube-apiserver。
2. Apiserver收到指令后，通知给controer-manager创建一个资源对象。
3. Controer-manager通过api-server将pod的配置信息存储到ETCD数据中心中。
4. Kube-scheduer检测到pod信息会开始调度预选，会先过滤掉不符合Pod资源配置要求的节点，然后开始调度调优，主要是挑选出更适合运行pod的节点，然后将pod的资源配置单发送到node节点上的kubeet组件上。
5. Kubeet根据scheduer发来的资源配置单运行pod，运行成功后，将pod的运行信息返回给scheduer，scheduer将返回的pod运行状况的信息存储到etcd数据中心。



## Kubernetes中Pod的重启策略

Pod重启策略(RestartPoicy)应用于Pod内的所有容器，并且仅在Pod所处的Node上由kubeet进行判断和重启操作。当某个容器异常退出或者健康检查失败时，kubeet将根据RestartPoicy的设置来进行相应操作。
Pod的重启策略包括Aways、OnFaiure和Never，默认值为Aways。

- Aways：当容器失效时，由kubeet自动重启该容器；
- OnFaiure：当容器终止运行且退出码不为0时，由kubeet自动重启该容器；
- Never：不论容器运行状态如何，kubeet都不会重启该容器。

同时Pod的重启策略与控制方式关联，当前可用于管理Pod的控制器包括RepicationControer、Job、DaemonSet及直接管理kubelet管理(静态Pod)。不同控制器的重启策略限制如下：

- RC和DaemonSet：必须设置为Aways，需要保证该容器持续运行；
- Job：OnFaiure或Never，确保容器执行完成后不再重启；
- kubelet：在Pod失效时重启，不论将RestartPoicy设置为何值，也不会对Pod进行健康检查。



## Kubernetes中Pod的健康检查方式

对Pod的健康检查可以通过两类探针来检查：LivenessProbe和ReadinessProbe。

- LivenessProbe探针：用于判断容器是否存活(running状态)，如果ivenessProbe探针探测到容器不健康，则kubeet将杀掉该容器，并根据容器的重启策略做相应处理。若一个容器不包含ivenessProbe探针，kubeet认为该容器的ivenessProbe探针返回值用于是“Success”。
- ReadineeProbe探针：用于判断容器是否启动完成(ready状态)。如果ReadinessProbe探针探测到失败，则Pod的状态将被修改。EndpointControer将从Service的Endpoint中删除包含该容器所在Pod的Eenpoint。
- startupProbe探针：启动检查机制，应用一些启动缓慢的业务，避免业务长时间启动而被上面两类探针ki掉。



## KubernetesPod的LivenessProbe探针的常见方式

kubeet定期执行ivenessProbe探针来诊断容器的健康状态，通常有以下三种方式：

- ExecAction：在容器内执行一个命令，若返回码为0，则表明容器健康。
- TCPSocketAction：通过容器的IP地址和端口号执行TCP检查，若能建立TCP连接，则表明容器健康。
- HTTPGetAction：通过容器的IP地址、端口号及路径调用HTTPGet方法，若响应的状态码大于等于200且小于400，则表明容器健康。



## KubernetesPod的常见调度方式

Kubernetes中，Pod通常是容器的载体，主要有如下常见调度方式：

- Depoyment或RC：该调度策略主要功能就是自动部署一个容器应用的多份副本，以及持续监控副本的数量，在集群内始终维持用户指定的副本数量。
- NodeSelector：定向调度，当需要手动指定将Pod调度到特定Node上，可以通过Node的标签(abe)和Pod的nodeSeector属性相匹配。
- NodeAffinity亲和性调度：亲和性调度机制极大的扩展了Pod的调度能力，目前有两种节点亲和力表达：
  - requiredDuringScheduingIgnoredDuringExecution：硬规则，必须满足指定的规则，调度器才可以调度Pod至Node上(类似nodeSeector，语法不同)。
  - preferredDuringScheduingIgnoredDuringExecution：软规则，优先调度至满足的Node的节点，但不强求，多个优先级规则还可以设置权重值。

- Taints和Toerations(污点和容忍)：
- Taint：使Node拒绝特定Pod运行；
- Toeration：为Pod的属性，表示Pod能容忍(运行)标注了Taint的Node。



## Kubernetes初始化容器(initcontainer)

initcontainer的运行方式与应用容器不同，它们必须先于应用容器执行完成，当设置了多个initcontainer时，将按顺序逐个运行，并且只有前一个initcontainer运行成功后才能运行后一个initcontainer。当所有initcontainer都成功运行后，Kubernetes才会初始化Pod的各种信息，并开始创建和运行应用容器。



## Kubernetes depoyment升级过程

1. 初始创建Depoyment时，系统创建了一个RepicaSet，并按用户的需求创建了对应数量的Pod副本。
2. 当更新Depoyment时，系统创建了一个新的RepicaSet，并将其副本数量扩展到1，然后将旧RepicaSet缩减为2。
3. 之后，系统继续按照相同的更新策略对新旧两个RepicaSet进行逐个调整。
4. 最后，新的RepicaSet运行了对应个新版本Pod副本，旧的RepicaSet副本数量则缩减为0。



## Kubernetes depoyment升级策略

在Depoyment的定义中，可以通过spec.strategy指定Pod更新的策略，目前支持两种策略：Recreate(重建)和RoingUpdate(滚动更新)，默认值为RoingUpdate。

- Recreate：设置spec.strategy.type=Recreate，表示Depoyment在更新Pod时，会先杀掉所有正在运行的Pod，然后创建新的Pod。
- RoingUpdate：设置spec.strategy.type=RoingUpdate，表示Depoyment会以滚动更新的方式来逐个更新Pod。同时，可以通过设置spec.strategy.roingUpdate下的两个参数(maxUnavaiabe和maxSurge)来控制滚动更新的过程。



## Kubernetes DaemonSet类型的资源特性

DaemonSet资源对象会在每个Kubernetes集群中的节点上运行，并且每个节点只能运行一个pod，这是它和depoyment资源对象的最大也是唯一的区别。因此，在定义yam文件中，不支持定义repicas。
它的一般使用场景如下：

- 在去做每个节点的日志收集工作。
- 监控每个节点的的运行状态。



## Kubernetes自动扩容机制

Kubernetes使用HorizontaPodAutoscaer(HPA)的控制器实现基于CPU使用率进行自动Pod扩缩容的功能。HPA控制器周期性地监测目标Pod的资源性能指标，并与HPA资源对象中的扩缩容条件进行对比，在满足条件时对Pod副本数量进行调整。

### HPA原理

Kubernetes中的某个MetricsServer(Heapster或自定义MetricsServer)持续采集所有Pod副本的指标数据。HPA控制器通过MetricsServer的API(Heapster的API或聚合API)获取这些数据，基于用户定义的扩缩容规则进行计算，得到目标Pod副本数量。
当目标Pod副本数量与当前副本数量不同时，HPA控制器就向Pod的副本控制器(Depoyment、RC或RepicaSet)发起scae操作，调整Pod的副本数量，完成扩缩容操作。



## Kubernetes Service类型

通过创建Service，可以为一组具有相同功能的容器应用提供一个统一的入口地址，并且将请求负载分发到后端的各个容器应用上。其主要类型有：

- CusterIP：虚拟的服务IP地址，该地址用于Kubernetes集群内部的Pod访问，在Node上kube-proxy通过设置的iptabes规则进行转发；
- NodePort：使用宿主机的端口，使能够访问各Node的外部客户端通过Node的IP地址和端口号就能访问服务；
- LoadBaancer：使用外接负载均衡器完成到服务的负载分发，需要在spec.status.oadBaancer字段指定外部负载均衡器的IP地址，通常用于公有云。



## KubernetesService分发后端的策略

Service负载分发的策略有：RoundRobin和SessionAffinity

- RoundRobin：默认为轮询模式，即轮询将请求转发到后端的各个Pod上。
- SessionAffinity：基于客户端IP地址进行会话保持的模式，即第1次将某个客户端发起的请求转发到后端的某个Pod上，之后从相同的客户端发起的请求都将被转发到后端相同的Pod上。

## Kubernetes Headess Service

在某些应用场景中，若需要人为指定负载均衡器，不使用Service提供的默认负载均衡的功能，或者应用程序希望知道属于同组服务的其他实例。Kubernetes提供了HeadessService来实现这种功能，即不为Service设置CusterIP(入口IP地址)，仅通过abeSeector将后端的Pod列表返回给调用的客户端。

## Kubernetes外部如何访问集群内的服务

对于Kubernetes，集群外的客户端默认情况，无法通过Pod的IP地址或者Service的虚拟IP地址:虚拟端口号进行访问。通常可以通过以下方式进行访问Kubernetes集群内的服务：

- 映射Pod到物理机：将Pod端口号映射到宿主机，即在Pod中采用hostPort方式，以使客户端应用能够通过物理机访问容器应用。
- 映射Service到物理机：将Service端口号映射到宿主机，即在Service中采用nodePort方式，以使客户端应用能够通过物理机访问容器应用。
- 映射Sercie到LoadBaancer：通过设置oadBaancer映射到云服务商提供的LoadBaancer地址。这种用法仅用于在公有云服务提供商的云平台上设置Service的场景。

## Kubernetes ingress

Kubernetes的Ingress资源对象，用于将不同UR的访问请求转发到后端不同的Service，以实现HTTP层的业务路由机制。

Kubernetes使用了Ingress策略和IngressControer，两者结合并实现了一个完整的Ingress负载均衡器。使用Ingress进行负载分发时，IngressControer基于Ingress规则将客户端请求直接转发到Service对应的后端Endpoint(Pod)上，从而跳过kube-proxy的转发功能，kube-proxy不再起作用，全过程为：ingresscontroer+ingress规则---->services。
同时当IngressControer提供的是对外服务，则实际上实现的是边缘路由器的功能。



## Kubernetes镜像的下载策略

K8s的镜像下载策略有三种：Aways、Never、IFNotPresent。

- Aways：镜像标签为atest时，总是从指定的仓库中获取镜像。
- Never：禁止从仓库中下载镜像，也就是说只能使用本地镜像。
- IfNotPresent：仅当本地没有对应镜像时，才从目标仓库中下载。默认的镜像下载策略是：当镜像标签是latest时，默认策略是Aways；当镜像标签是自定义时(也就是标签不是latest)，那么默认策略是IfNotPresent。



## Kubernetes的负载均衡器

负载均衡器是暴露服务的最常见和标准方式之一。

根据工作环境使用两种类型的负载均衡器，即内部负载均衡器或外部负载均衡器。

- 内部负载均衡器自动平衡负载并使用所需配置分配容器;
- 外部负载均衡器将流量从外部负载引导至后端容器。

## Kubernetes各模块与APIServer通信

KubernetesAPIServer作为集群的核心，负责集群各功能模块之间的通信。集群内的各个功能模块通过APIServer将信息存入etcd，当需要获取和操作这些数据时，则通过APIServer提供的REST接口(用GET、IST或WATCH方法)来实现，从而实现各模块之间的信息交互。
如kubeet进程与APIServer的交互：每个Node上的kubeet每隔一个时间周期，就会调用一次APIServer的REST接口报告自身状态，APIServer在接收到这些信息后，会将节点状态信息更新到etcd中。
如kube-controer-manager进程与APIServer的交互：kube-controer-manager中的NodeControer模块通过APIServer提供的Watch接口实时监控Node的信息，并做相应处理。
如kube-scheduer进程与APIServer的交互：Scheduer通过APIServer的Watch接口监听到新建Pod副本的信息后，会检索所有符合该Pod要求的Node列表，开始执行Pod调度逻辑，在调度成功后将Pod绑定到目标节点上。

## Kubernetes Scheduer作用及实现原理

Kubernetes Scheduer是负责Pod调度的重要功能模块，KubernetesScheduer在整个系统中承担了“承上启下”的重要功能，“承上”是指它负责接收ControerManager创建的新Pod，为其调度至目标Node；“启下”是指调度完成后，目标Node上的kubeet服务进程接管后继工作，负责Pod接下来生命周期。KubernetesScheduer的作用是将待调度的Pod(API新创建的Pod、ControerManager为补足副本而创建的Pod等)按照特定的调度算法和调度策略绑定(Binding)到集群中某个合适的Node上，并将绑定信息写入etcd中。
在整个调度过程中涉及三个对象，分别是待调度Pod列表、可用Node列表，以及调度算法和策略。


KubernetesScheduer通过调度算法调度为待调度Pod列表中的每个Pod从Node列表中选择一个最适合的Node来实现Pod的调度。随后，目标节点上的kubeet通过APIServer监听到KubernetesScheduer产生的Pod绑定事件，然后获取对应的Pod清单，下载Image镜像并启动容器。

## KubernetesScheduer使用算法将Pod绑定到worker节点

KubernetesScheduer根据如下两种调度算法将Pod绑定到最合适的工作节点：

- 预选(Predicates)：输入是所有节点，输出是满足预选条件的节点。kube-scheduer根据预选策略过滤掉不满足策略的Nodes。如果某节点的资源不足或者不满足预选策略的条件则无法通过预选。如“Node的abe必须与Pod的Seector一致”。
- 优选(Priorities)：输入是预选阶段筛选出的节点，优选会根据优先策略为通过预选的Nodes进行打分排名，选择得分最高的Node。例如，资源越富裕、负载越小的Node可能具有越高的排名。

## Kubernetes kubelet的作用

在Kubernetes集群中，每个Node(又称Worker)上都会启动一个kubelet服务进程。该进程用于处理Master下发到本节点的任务，管理Pod及Pod中的容器。每个kubelet进程都会在APIServer上注册节点自身的信息，定期向Master汇报节点资源的使用情况，并通过cAdvisor监控容器和节点资源。

## Kubernetes kubelet监控Worker节点资源


kubelet使用cAdvisor对worker节点资源进行监控。在Kubernetes系统中，cAdvisor已被默认集成到kubelet组件内，当kubelet服务启动时，它会自动启动cAdvisor服务，然后cAdvisor会实时采集所在节点的性能指标及在节点上运行的容器的性能指标。

## Kubernetes保证集群的安全性

Kubernetes通过一系列机制来实现集群的安全控制，主要有如下不同的维度：

- 基础设施方面：保证容器与其所在宿主机的隔离；

- 权限方面
  - 最小权限原则：合理限制所有组件的权限，确保组件只执行它被授权的行为，
    通过限制单个组件的能力来限制它的权限范围。
  - 用户权限：划分普通用户和管理员的角色。
- 集群方面：
  - APIServer的认证授权：Kubernetes集群中所有资源的访问和变更都是通过KubernetesAPIServer来实现的，因此需要建议采用更安全的HTTPS或Token来识别和认证客户端身份(Authentication)，以及随后访问权限的授权(Authorization)环节。
  - APIServer的授权管理：通过授权策略来决定一个API调用是否合法。对合法用户进行授权并且随后在用户访问时进行鉴权，建议采用更安全的RBAC方式来提升集群安全授权。
  - 敏感数据引入Secret机制：对于集群敏感数据建议使用Secret方式进行保护。
  - AdmissionContro(准入机制)：对kubernetesapi的请求过程中，顺序为：先经过认证&授权，然后执行准入操作，最后对目标对象进行操作。



## Kubernetes准入机制

在对集群进行请求时，每个准入控制代码都按照一定顺序执行。如果有一个准入控制拒绝了此次请求，那么整个请求的结果将会立即返回，并提示用户相应的error信息。
准入控制(AdmissionContro)准入控制本质上为一段准入代码，在对kubernetesapi的请求过程中，顺序为：先经过认证&授权，然后执行准入操作，最后对目标对象进行操作。常用组件(控制代码)如下：

- AwaysAdmit：允许所有请求
- AwaysDeny：禁止所有请求，多用于测试环境。
- ServiceAccount：它将serviceAccounts实现了自动化，它会辅助serviceAccount做一些事情，比如如果pod没有serviceAccount属性，它会自动添加一个defaut，并确保pod的serviceAccount始终存在。
- LimitRanger：观察所有的请求，确保没有违反已经定义好的约束条件，这些条件定义在namespace中：LimitRange对象中。
- NamespaceExists：观察所有的请求，如果请求尝试创建一个不存在的namespace，则这个请求被拒绝。



## Kubernetes RBAC及其特点

RBAC是基于角色的访问控制，是一种基于个人用户的角色来管理对计算机或网络资源的访问的方法。
相对于其他授权模式，RBAC具有如下优势：

- 对集群中的资源和非资源权限均有完整的覆盖。
- 整个RBAC完全由几个API对象完成，同其他API对象一样，可以用kubelet或API进行操作。
- 可以在运行时进行调整，无须重新启动APIServer。



## Kubernetes Secret作用

Secret对象，主要作用是保管私密数据，比如密码、OAuthTokens、SSHKeys等信息。将这些私密信息放在Secret对象中比直接放在Pod或DockerImage中更安全，也更便于使用和分发。

## Kubernetes Secret使用方式

创建完secret之后，可通过如下三种方式使用：

- 在创建Pod时，通过为Pod指定ServiceAccount来自动使用该Secret。
- 通过挂载该Secret到Pod来使用它。
- 在Docker镜像下载时使用，通过指定Pod的spc.ImagePuSecrets来引用它。



## Kubernetes PodSecurityPoicy机制

KubernetesPodSecurityPoicy是为了更精细地控制Pod对资源的使用方式以及提升安全策略。在开启PodSecurityPoicy准入控制器后，Kubernetes默认不允许创建任何Pod，需要创建PodSecurityPoicy策略和相应的RBAC授权策略(AuthorizingPoicies)，Pod才能创建成功。



## Kubernetes PodSecurityPoicy能实现的安全策略

在PodSecurityPoicy对象中可以设置不同字段来控制Pod运行时的各种安全策略，常见的有：

- 特权模式：privileged是否允许Pod以特权模式运行。
- 宿主机资源：控制Pod对宿主机资源的控制，如hostPID：是否允许Pod共享宿主机的进程空间。
- 用户和组：设置运行容器的用户ID(范围)或组(范围)。
- 提升权限：AllowPrivilegeEscalation：设置容器内的子进程是否可以提升权限，通常在设置非root用户(MustRunAsNonRoot)时进行设置。
- SELinux：进行SEinux的相关配置。



## Kubernetes网络模型

Kubernetes网络模型中每个Pod都拥有一个独立的IP地址，并假定所有Pod都在一个可以直接连通的、扁平的网络空间中。所以不管它们是否运行在同一个Node(宿主机)中，都要求它们可以直接通过对方的IP进行访问。设计这个原则的原因是，用户不需要额外考虑如何建立Pod之间的连接，也不需要考虑如何将容器端口映射到主机端口等问题。


同时为每个Pod都设置一个IP地址的模型使得同一个Pod内的不同容器会共享同一个网络命名空间，也就是同一个inux网络协议栈。这就意味着同一个Pod内的容器可以通过ocahost来连接对方的端口。
在Kubernetes的集群里，IP是以Pod为单位进行分配的。一个Pod内部的所有容器共享一个网络堆栈(相当于一个网络命名空间，它们的IP地址、网络设备、配置等都是共享的)。

## Kubernetes CNI模型

CNI提供了一种应用容器的插件化网络解决方案，定义对容器网络进行操作和配置的规范，通过插件的形式对CNI接口进行实现。CNI仅关注在创建容器时分配网络资源，和在销毁容器时删除网络资源。在CNI模型中只涉及两个概念：容器和网络。

- 容器(Container)：是拥有独立inux网络命名空间的环境，例如使用Docker或rkt创建的容器。容器需要拥有自己的inux网络命名空间，这是加入网络的必要条件。
- 网络(Network)：表示可以互连的一组实体，这些实体拥有各自独立、唯一的IP地址，可以是容器、物理机或者其他网络设备(比如路由器)等。



对容器网络的设置和操作都通过插件(Pugin)进行具体实现，CNI插件包括两种类型：CNI Plugin和IPAM(IPAddress Management)Plugin。CNI Plugin负责为容器配置网络资源，IPAM Plugin负责对容器的IP地址进行分配和管理。IPAM Plugin作为CNI Plugin的一部分，与CNI Plugin协同工作。



## Kubernetes 网络策略

为实现细粒度的容器间网络访问隔离策略，Kubernetes引入NetworkPolicy。NetworkPolicy的主要功能是对Pod间的网络通信进行限制和准入控制，设置允许访问或禁止访问的客户端Pod列表。Network Policy定义网络策略，配合策略控制器(PoicyControer)进行策略的实现。

## Kubernetes网络策略原理

NetworkPoicy的工作原理主要为：poicycontroer需要实现一个APIistener，监听用户设置的NetworkPoicy定义，并将网络访问规则通过各Node的Agent进行实际设置(Agent则需要通过CNI网络插件实现)。

## Kubernetes中flanne的作用

Flanne可以用于Kubernetes底层网络的实现，主要作用有：

- 它能协助Kubernetes，给每一个Node上的Docker容器都分配互相不冲突的IP地址。
- 它能在这些IP地址之间建立一个覆盖网络(OverayNetwork)，通过这个覆盖网络，将数据包原封不动地传递到目标容器内。



## Kubernetes Caico网络组件实现原理

Caico是一个基于BGP的纯三层的网络方案，与OpenStack、Kubernetes、AWS、GCE等云平台都能够良好地集成。

Caico在每个计算节点都利用inuxKerne实现了一个高效的vRouter来负责数据转发。每个vRouter都通过BGP协议把在本节点上运行的容器的路由信息向整个Caico网络广播，并自动设置到达其他节点的路由转发规则。

Caico保证所有容器之间的数据流量都是通过IP路由的方式完成互联互通的。Caico节点组网时可以直接利用数据中心的网络结构(2或者3)，不需要额外的NAT、隧道或者OverayNetwork，没有额外的封包解包，能够节约CPU运算，提高网络效率。

## Kubernetes共享存储的作用

Kubernetes对于有状态的容器应用或者对数据需要持久化的应用，因此需要更加可靠的存储来保存应用产生的重要数据，以便容器应用在重建之后仍然可以使用之前的数据。因此需要使用共享存储。

## Kubernetes数据持久化的方式有哪些？

Kubernetes通过数据持久化来持久化保存重要数据，常见的方式有：

- EmptyDir(空目录)：没有指定要挂载宿主机上的某个目录，直接由Pod内保部映射到宿主机上。类似于docker中的managervoume。
  - 场景
    - 只需要临时将数据保存在磁盘上，比如在合并/排序算法中；
    - 作为两个容器的共享存储。
  - 特性
    - 同个pod里面的不同容器，共享同一个持久化目录，当pod节点删除时，voume的数据也会被删除。
    - emptyDir的数据持久化的生命周期和使用的pod一致，一般是作为临时存储使用。
- Hostpath：将宿主机上已存在的目录或文件挂载到容器内部。类似于docker中的bindmount挂载方式。
  - 特性
    - 增加了pod与节点之间的鹅合。
- PersistentVoume(简称PV)：如基于NFS服务的PV，也可以基于GFS的PV。它的作用是统一数据持久化目录，方便管理。

## Kubernetes PV和PVC

PV是对底层网络共享存储的抽象，将共享存储定义为一种“资源”。

PVC则是用户对存储资源的一个“申请”。

## Kubernetes PV生命周期内的阶段

某个PV在生命周期中可能处于以下4个阶段(Phaes)之一。

- Avaiabe：可用状态，还未与某个PVC绑定。
- Bound：已与某个PVC绑定。
- Reeased：绑定的PVC已经删除，资源已释放，但没有被集群回收。
- Faied：自动资源回收失败。

## Kubernetes所支持的存储供应模式

答：Kubernetes支持两种资源的存储供应模式：静态模式(Static)和动态模式(Dynamic)。

- 静态模式：集群管理员手工创建许多PV，在定义PV时需要将后端存储的特性进行设置。
- 动态模式：集群管理员无须手工创建PV，而是通过StorageCass的设置对后端

存储进行描述，标记为某种类型。此时要求PVC对存储的类型进行声明，系统将自动完成PV的创建及与PVC的绑定。

## Kubernetes CSI模型

Kubernetes CSI是Kubernetes推出与容器对接的存储接口标准，存储提供方只需要基于标准接口进行存储插件的实现，就能使用Kubernetes的原生存储机制为容器提供存储服务。CSI使得存储提供方的代码能和Kubernetes代码彻底解耦，部署也与Kubernetes核心组件分离，显然，存储插件的开发由提供方自行维护，就能为Kubernetes用户提供更多的存储功能，也更加安全可靠。
CSI包括CSI Controer和CSI Node：

- CSI Controer的主要功能是提供存储服务视角对存储资源和存储卷进行管理和
- 操作。
- CSI Node的主要功能是对主机(Node)上的Voume进行管理和操作。



## Kubernetes Worker节点加入集群的过程


通常需要对Worker节点进行扩容，从而将应用系统进行水平扩展。主要过程如下：

1. 在该Node上安装Docker、kubeet和kube-proxy服务；
2. 然后配置kubeet和kubeproxy的启动参数，将MasterUR指定为当前Kubernetes集群Master的地址，最后启动这些服务；
3. 通过kubeet默认的自动注册机制，新的Worker将会自动加入现有的Kubernetes集群中；
4. KubernetesMaster在接受了新Worker的注册之后，会自动将其纳入当前集群的调度范围。



## Kubernetes Pod如何实现对节点的资源控制

Kubernetes集群里的节点提供的资源主要是计算资源，计算资源是可计量的能被申请、分配和使用的基础资源。当前Kubernetes集群中的计算资源主要包括CPU、GPU及Memory。CPU与Memory是被Pod使用的，因此在配置Pod时可以通过参数CPURequest及MemoryRequest为其中的每个容器指定所需使用的CPU与Memory量，Kubernetes会根据Request的值去查找有足够资源的Node来调度此Pod。
通常，一个程序所使用的CPU与Memory是一个动态的量，确切地说，是一个范围，跟它的负载密切相关：负载增加时，CPU和Memory的使用量也会增加。

## Kubernetes Requests和imits影响Pod的调度


当一个Pod创建成功时，Kubernetes调度器(Scheduer)会为该Pod选择一个节点来执行。对于每种计算资源(CPU和Memory)而言，每个节点都有一个能用于运行Pod的最大容量值。调度器在调度时，首先要确保调度后该节点上所有Pod的CPU和内存的Requests总和，不超过该节点能提供给Pod使用的CPU和Memory的最大容量值。

## Kubernetes Metric Service

在Kubernetes从1.10版本后采用MetricsServer作为默认的性能数据采集和监控，主要用于提供核心指标(CoreMetrics)，包括Node、Pod的CPU和内存使用指标。
对其他自定义指标(CustomMetrics)的监控则由Prometheus等组件来完成。



## Kubernetes中，使用EFK实现日志的统一管理

在Kubernetes集群环境中，通常一个完整的应用或服务涉及组件过多，建议对日志系统进行集中化管理，通常采用EFK实现。
EFK是Easticsearch、Fuentd和Kibana的组合，其各组件功能如下：

- Easticsearch：是一个搜索引擎，负责存储日志并提供查询接口；
- Fuentd：负责从Kubernetes搜集日志，每个node节点上面的fuentd监控并收集该节点上面的系统日志，并将处理过后的日志信息发送给Easticsearch；
- Kibana：提供了一个WebGUI，用户可以浏览和搜索存储在Easticsearch中的日志。


通过在每台node上部署一个以DaemonSet方式运行的fuentd来收集每台node上的日志。Fuentd将docker日志目录/var/ib/docker/containers和/var/og目录挂载到Pod中，然后Pod会在node节点的/var/og/pods目录中创建新的目录，可以区别不同的容器日志输出，该目录下有一个日志文件链接到/var/ib/docker/contianers目录下的容器日志输出。



## Kubernetes进行优雅的节点关机维护

由于Kubernetes节点运行大量Pod，因此在进行关机维护之前，建议先使用kubect drain将该节点的Pod进行驱逐，然后进行关机维护。

## Kubernetes集群联邦

Kubernetes集群联邦可以将多个Kubernetes集群作为一个集群进行管理。因此，可以在一个数据中心/云中创建多个Kubernetes集群，并使用集群联邦在一个地方控制/管理所有集群。

## Hem及其优势

Hem是Kubernetes的软件包管理工具。类似Ubuntu中使用的apt、Centos中使用的yum或者Python中的pip一样。
Hem能够将一组K8S资源打包统一管理,是查找、共享和使用为Kubernetes构建的软件的最佳方式。

Hem中通常每个包称为一个Chart，一个Chart是一个目录(一般情况下会将目录进行打包压缩，形成name-version.tgz格式的单一文件，方便传输和存储)。

### Hem优势

在Kubernetes中部署一个可以使用的应用，需要涉及到很多的Kubernetes资源的共同协作。使用hem则具有如下优势：

- 统一管理、配置和更新这些分散的k8s的应用资源文件；
- 分发和复用一套应用模板；
- 将应用的一系列资源当做一个软件包管理。
  - 对于应用发布者而言，可以通过Hem打包应用、管理应用依赖关系、管理应用版本并发布应用到软件仓库。
  - 对于使用者而言，使用Hem后不用需要编写复杂的应用部署文件，可以以简单的方式在Kubernetes上查找、安装、升级、回滚、卸载应用程序。



## etcd及其特点

etcd是CoreOS团队发起的开源项目，是一个管理配置信息和服务发现(servicediscovery)的项目，它的目标是构建一个高可用的分布式键值(key-vaue)数据库，基于Go语言实现。
特点：

- 简单：支持REST风格的HTTP+JSONAPI
- 安全：支持HTTPS方式的访问
- 快速：支持并发1k/s的写操作
- 可靠：支持分布式结构，基于Raft的一致性算法，Raft是一套通过选举主节点来实现分布式系统一致性的算法。



## etcd适应的场景

- 服务发现(ServiceDiscovery)：服务发现主要解决在同一个分布式集群中的进程或服务，要如何才能找到对方并建立连接。本质上来说，服务发现就是想要了解集群中是否有进程在监听udp或tcp端口，并且通过名字就可以查找和连接。
- 消息发布与订阅：在分布式系统中，最适用的一种组件间通信方式就是消息发布与订阅。即构建一个配置共享中心，数据提供者在这个配置中心发布消息，而消息使用者则订阅他们关心的主题，一旦主题有消息发布，就会实时通知订阅者。通过这种方式可以做到分布式系统配置的集中式管理与动态更新。应用中用到的一些配置信息放到etcd上进行集中管理。
- 负载均衡：在分布式系统中，为了保证服务的高可用以及数据的一致性，通常都会把数据和服务部署多份，以此达到对等服务，即使其中的某一个服务失效了，也不影响使用。etcd本身分布式架构存储的信息访问支持负载均衡。etcd集群化以后，每个etcd的核心节点都可以处理用户的请求。所以，把数据量小但是访问频繁的消息数据直接存储到etcd中也可以实现负载均衡的效果。
- 分布式通知与协调：与消息发布和订阅类似，都用到了etcd中的Watcher机制，通过注册与异步通知机制，实现分布式环境下不同系统之间的通知与协调，从而对数据变更做到实时处理。
- 分布式锁：因为etcd使用Raft算法保持了数据的强一致性，某次操作存储到集
  群中的值必然是全局一致的，所以很容易实现分布式锁。锁服务有两种使用方式，
  一是保持独占，二是控制时序。
- 集群监控与eader竞选：通过etcd来进行监控实现起来非常简单并且实时性强。

（完）