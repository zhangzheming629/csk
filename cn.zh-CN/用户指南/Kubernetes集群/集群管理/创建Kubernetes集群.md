# 创建Kubernetes集群 {#CS_user_guide_kubernetes .concept}

您可以通过容器服务管理控制台非常方便地快速创建 Kubernetes 集群。

## 使用须知 {#section_sgh_hvv_ydb .section}

创建集群过程中，容器服务会进行如下操作：

-   创建 ECS，配置管理节点到其他节点的 SSH 的公钥登录，通过 CloudInit 安装配置 Kubernetes 集群。
-   创建安全组，该安全组允许 VPC 入方向全部 ICMP 端口的访问。
-   创建 VPC 路由规则。
-   创建 NAT 网关和共享带宽包（或EIP）。
-   创建 RAM 子账号和 AK，该子账号拥有 ECS 的查询、实例创建和删除的权限，添加和删除云盘的权限，SLB 的全部权限，云监控的全部权限，VPC 的全部权限，日志服务的全部权限，NAS 的全部权限。Kubernetes 集群会根据用户部署的配置相应的动态创建 SLB，云盘，VPC路由规则。
-   创建内网 SLB，暴露 6443 端口。
-   创建公网 SLB，暴露 6443端口（如果您在创建集群的时候选择开放公网 SSH 登录，则会暴露 22 端口；如果您选择不开放公网 SSH 访问，则不会暴露 22 端口）。

## 前提条件 {#section_ugh_hvv_ydb .section}

您需要开通容器服务、资源编排（ROS）服务、弹性伸缩（ESS）服务和访问控制（RAM）服务。

登录 [容器服务管理控制台](https://cs.console.aliyun.com/)、[ROS 管理控制台](https://ros.console.aliyun.com/) 、 [RAM 管理控制台](https://ram.console.aliyun.com/) 和 [弹性伸缩控制台](https://essnew.console.aliyun.com) 开通相应的服务。

**说明：** 容器服务 Kubernetes 集群部署依赖阿里云资源编排 ROS 的应用部署能力，所以创建 Kubernetes 集群前，您需要开通 ROS。

## 使用限制 {#section_kqj_pvv_ydb .section}

-   随集群一同创建的负载均衡实例只支持按量付费的方式。
-   Kubernetes 集群仅支持专有网络 VPC。
-   每个账号默认可以创建的云资源有一定的配额，如果超过配额创建集群会失败。请在创建集群前确认您的配额。如果您需要提高您的配额，请提交工单申请。
    -   每个账号默认最多可以创建 5 个集群（所有地域下），每个集群中最多可以添加 40 个节点。如果您需要创建更多的集群或者节点，请提交工单申请。

        **说明：** Kubernetes集群中，VPC默认路由条目不超过48条，意味着Kubernetes集群使用VPC时，默认节点上限是48个，如果需要更大的节点数，需要您先对目标VPC开工单，提高VPC路由条目，再对容器服务提交工单。

    -   每个账号默认最多可以创建 100 个安全组。
    -   每个账号默认最多可以创建 60 个按量付费的负载均衡实例。
    -   每个账号默认最多可以创建 20 个EIP。
-   ECS 实例使用限制：
    -   仅支持 CentOS 操作系统。
    -   支持创建按量付费和包年包月的ECS实例。

## 操作步骤 {#section_cpd_qvv_ydb .section}

1.  登录 [容器服务管理控制台](https://cs.console.aliyun.com)。
2.  在 Kubernetes 菜单下，单击左侧导航栏的**集群** \> **集群**，进入集群列表页面。
3.  单击页面右上角的创建**Kubernetes 集群**，在弹出的选择集群模板中，选择**标准专有集群**页面，单击**创建**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339035_zh-CN.png)

    默认进入**Kubernetes**集群配置页面。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155807433310858_zh-CN.png)

4.  填写集群的名称。

    集群名称应包含1-63个字符，可包含数字、汉字、英文字符或连字符（-）。

5.  选择集群所在的资源组。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155807433345326_zh-CN.png)

6.  选择集群所在的地域和可用区。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339036_zh-CN.png)

7.  设置集群的网络。Kubernetes 集群仅支持专有网络。

    您可以在已有 VPC 列表中选择所需的 VPC 和交换机。

    -   如果您使用的 VPC 中当前已有 NAT 网关，容器服务会使用已有的 NAT 网关。
    -   如果 VPC 中没有 NAT 网关，系统会默认自动为您创建一个 NAT 网关。如果您不希望系统自动创建 NAT 网关，可以取消勾选页面下方的**为专有网络配置 SNAT**。

        **说明：** 若选择不自动创建 NAT 网关，您需要自行配置 NAT 网关实现 VPC 安全访问公网环境，或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339037_zh-CN.png)

8.  设置节点类型，容器服务支持按量付费和包年包月两种节点类型。
9.  设置 Master 节点的配置信息。

    您需要选择 Master 节点的实例规格。

    **说明：** 

    -   目前支持 CentOS、Windows 操作系统。
    -   目前仅支持创建 3 个 Master 节点。
    -   默认为Master节点挂载系统盘，支持SSD云盘和高效云盘。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339038_zh-CN.png)

10. 设置 Worker 节点的配置信息。您可选择新增实例或添加已有实例。

    **说明：** 

    -   目前支持 CentOS、Windows 操作系统。
    -   每个集群最多可包含 37 个 r 节点。如果您需要创建更多的节点，请提交工单申请。
    -   默认为Worker节点挂载系统盘，支持SSD云盘和高效云盘。
    -   支持为Worker节点挂载一个数据盘，支持SSD云盘、高效云盘和普通云盘。
    1.  若您选择新增实例，则需要选择 Worker 节点的实例规格，需要创建的 Worker 节点的数量，以及系统盘规格。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339039_zh-CN.png)

    2.  若您选择添加已有实例，则需要预先在此地域下创建 ECS 云服务器。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743339040_zh-CN.png)

11. 显示当前支持的Docker版本和Kubernetes版本，您可查看对应版本，并根据需要进行选择。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155807433345329_zh-CN.png)

12. 配置登录方式。
    -   设置密钥。

        您需要在创建集群的时候选择密钥对登录方式，单击**新建密钥对**，跳转到ECS云服务器控制台，创建密钥对，参见[创建 SSH 密钥对](../../../../intl.zh-CN/安全/SSH密钥对/使用SSH密钥对.md#)。密钥对创建完毕后，设置该密钥对作为登录集群的凭据。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349041_zh-CN.png)

    -   设置密码。
        -   **登录密码**：设置节点的登录密码。
        -   **确认密码**：确认设置的节点登录密码。
13. 设置**Pod网络 CIDR** 和**Service CIDR**。

    **说明：** 该选项仅在选择**使用已有**VPC时出现。

    您需要指定**Pod 网络 CIDR**和**Service CIDR**，两者都不能与 VPC 及 VPC 内已有 Kubernetes 集群使用的网段重复，创建成功后不能修改。而且 Service 地址段也不能和 Pod 地址段重复，有关 kubernetes 网络地址段规划的信息，请参考[VPC下 Kubernetes 的网络地址段规划](intl.zh-CN/最佳实践/集群/VPC下 Kubernetes 的网络地址段规划.md#)。

14. 设置是否为专有网络配置 SNAT 网关。

    **说明：** 若不勾选**为专有网络配置 SNAT**，您需要自行配置NAT 网关实现 VPC 安全访问公网环境；或者手动配置 SNAT，否则 VPC 内实例将不能正常访问公网，会导致集群创建失败。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349042_zh-CN.png)

15. 设置是否开放**用公网SLB暴露API SERVER**。

    API Server提供了各类资源对象（Pod，Service等）的增删改查及watch等HTTP Rest接口。

    1.  如果选择开放，会创建一个公网SLB，同时把Master节点的6443端口（对应API Server）暴露出来，用户可以在外网通过kubeconfig连接/操作集群。
    2.  若选择不开放，不会创建公网SLB，用户只能在VPC内部用kubeconfig连接/操作集群。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16644/155807433412073_zh-CN.png)

16. 设置是否开放公网 SSH 登录。

    **说明：** 您需要开放**公网SLB暴露API SERVER**，才能设置公网SSH登录。

    -   选择开放公网 SSH 登录，您可以 SSH 访问集群。
    -   选择不开放公网 SSH 登录，将无法通过 SSH 访问集群，也无法通过 kubectl 连接 集群。如果您需要通过 SSH 访问集群实例，可以手动为 ECS 实例绑定 EIP，并配置安全组规则，开放 SSH（22）端口，具体操作参见[SSH访问Kubernetes集群](intl.zh-CN/用户指南/Kubernetes集群/集群管理/SSH访问Kubernetes集群.md#)。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/155807433412075_zh-CN.png)

17. 设置是否启用云监控插件。

    您可以选择在 ECS 节点上安装云监控插件，从而在云监控控制台查看所创建 ECS 实例的监控信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349044_zh-CN.png)

18. 设置是否启用日志服务，您可使用已有Project或新建一个Project。

    勾选**使用日志服务**，会在集群中自动配置日志服务插件。创建应用时，您可通过简单配置，快速使用日志服务，详情参见[使用日志服务进行Kubernetes日志采集](intl.zh-CN/用户指南/Kubernetes集群/日志管理/使用日志服务进行Kubernetes日志采集.md#)。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349244_zh-CN.png)

19. 设置启用的网络插件，支持Flannel和Terway网络插件，具体可参考[如何选择Kubernetes集群网络插件：Terway和Flannel](../../../../intl.zh-CN/常见问题/如何选择Kubernetes集群网络插件：Terway和Flannel.md#)。

    -   Flannel：简单稳定的社区的Flannel CNI插件。但功能偏简单，支持的特性少，例如：不支持基于Kubernetes标准的Network Policy。
    -   Terway：阿里云容器服务自研的网络插件，支持将阿里云的弹性网卡分配给容器，支持Kubernetes的Network Policy来定义容器间的访问策略，支持对单个容器做带宽的限流。
    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/40726/155807433421269_zh-CN.png)

20. 设置是否启用Master 实例保护。

    **说明：** 为防止通过控制台或 API 误删除释放Master节点，默认启用Master实例保护。

21. 为集群绑定标签。

    输入键和对应的值，单击**添加**。

    **说明：** 

    -   键是必需的，而 值 是可选的，可以不填写。
    -   键 不能是 aliyun、http:// 、https:// 开头的字符串，不区分大小写，最多 64 个字符。
    -   值 不能是 http:// 或 https://，可以为空，不区分大小写，最多 128 个字符。
    -   同一个资源，标签键不能重复，相同标签键（Key）的标签会被覆盖。
    -   如果一个资源已经绑定了 20 个标签，已有标签和新建标签会失效，您需要解绑部分标签后才能再绑定新的标签。
22. 是否启用高级选项。
    1.  设置节点 Pod 数量，是指单个节点可运行 Pod 数量的上限，建议保持默认值。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349045_zh-CN.png)

    2.  设置kube-proxy代理模式，支持iptables和IPVS两种模式。

        -   iptables：成熟稳定的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用iptables规则配置，但性能一般，受规模影响较大，适用于集群存在少量的service。
        -   IPVS：高性能的kube-proxy代理模式，Kubernetes service的服务发现和负载均衡使用Linux ipvs模块进行配置，适用于集群存在大量的service，对负载均衡有高性能要求的场景。
        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/149635/155807433441576_zh-CN.png)

    3.  设置节点服务端口的范围。

        可选端口范围：30000~65535

    4.  设置是否使用**自定义集群CA**。如果勾选自定义集群 CA，可以将 CA 证书添加到 kubernetes 集群中，加强服务端和客户端之间信息交互的安全性。

        ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349046_zh-CN.png)

23. 单击**创建集群**，启动部署。

**说明：** 一个包含多节点的 Kubernetes 集群的创建时间一般需要十几分钟。

## 查看集群部署结果 {#section_xhh_hvv_ydb .section}

集群创建成功后，您可以在容器服务管理控制台的 Kubernetes 集群列表页面查看所创建的集群。

![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349047_zh-CN.png)

-   您可以单击操作列的**查看日志**，进入集群日志信息页面查看集群的日志信息。 您也可以在集群日志信息页面中，单击**资源栈事件**查看更详细的信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349048_zh-CN.png)

-   在集群列表中，找到刚创建的集群，单击操作列中的**管理**，查看集群的基本信息和连接信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349049_zh-CN.png)

    **其中：** 

    -   **API Server 公网连接端点**：Kubernetes 的 API server 对公网提供服务的地址和端口，可以通过此服务在用户终端使用 kubectl 等工具管理集群。
    -   **API Server 内网连接端点**：Kubernetes 的 API server 对集群内部提供服务的地址和端口。此 IP 为负载均衡的地址，后端有 3 台 Master 提供服务。
    -   **Master 节点 SSH 连接地址**：可以直接通过 SSH 登录到 Master 节点，以便对集群进行日常维护。
    -   **服务访问域名**：为集群中的服务提供测试用的访问域名。服务访问域名后缀是`<cluster_id>.<region_id>.alicontainer.com`。
    例如，您可以通过 SSH 登录到 Master 节点，执行`kubectl get node`查看集群的节点信息。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/16639/15580743349050_zh-CN.png)

    可以发现，一共有 4 个节点，包括 3 个 Master 节点和我们在参数设置步骤填写的 1 个 Worker 节点。


