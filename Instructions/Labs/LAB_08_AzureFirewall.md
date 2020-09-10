---
lab:
    title: '08 - Azure 防火墙'
    module: 'Module 02 - Implement Platform Protection'
---

# 实验室 8：Azure 防火墙
# 学生实验室手册

## 实验场景

已要求你安装 Azure 防火墙。帮助你组织控制进出站网络访问，而这是整个网络安全计划的重要组成部分。具体来说，你想创建和测试以下基础结构组件：

- 具有工作负载子网和跳转主机子网的虚拟网络。
- 一个虚拟机代表对应的子网 
- 确保来自工作负载子网的所有出站工作负载流量的自定义路由必须使用防火墙。
- 仅允许 www.bing.com 出站流量的防火墙应用程序规则。 
- 允许外部 DNS 服务器查找的防火墙网络规则。

> 对于本实验室中的所有资源，我们使用的是 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1 ：部署和测试 Azure 防火墙

## 实验室文件：

- **\\Allfiles\\Labs\\08\\template.json**

### 练习 1 ：部署和测试 Azure 防火墙

### 预计用时：40 分钟

> 对于本实验室中的所有资源，我们正在使用 **东部（美国）** 地区。与你的教师确认这是你上课时使用的区域。 

在该练习中，你将完成以下任务：

- 任务 1：使用模板部署实验室环境。 
- 任务 2：部署 Azure 防火墙。
- 任务 3：创建默认路由。
- 任务 4：配置应用程序规则。
- 任务 5：配置网络规则。 
- 任务 6：配置 DNS 服务器。
- 任务 7：测试防火墙。 

#### 任务 1：使用模板部署实验室环境。 

在此任务中将检查并部署实验室环境。 

在本任务中，你将使用 ARM 模板创建一个虚拟机。该虚拟机将在本实验室的上一个练习中使用。 

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 在 Azure 门户中，在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“部署自定义模板”**，然后按 **Enter** 键。

1. 在 **“自定义部署”** 边栏选项卡中，单击 **“在编辑器中构建自己的模板”** 选项。

1. 在 **“编辑模板”** 边栏选项卡，单击 **“加载文件”**，找到 **\\Allfiles\\Labs\\08\\template.json** 文件，并单击 **“打开”**。

    >**备注**：查看模板的内容，注意它部署了一个托管 Windows Server 2019 数据中心的 Azure VM。

1. 在 **“编辑模板”** 边栏选项卡上，单击 **“保存”**。

1. 在 **“自定义部署”** 边栏选项卡上，请确保已配置以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |订阅|在本次实验室中你将使用的 Azure 订阅名|
   |资源组|单击 **“新建”**，键入名字 **“AZ500LAB08”**|
   |位置|**（美国）美国东部**|

    >**备注**：若要标识可在其中配置 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

1. 选中 **“我同意上述条款和条件”** 复选框，然后单击 **“购买”**。

    >**备注**：等待部署完成。该操作需约 2 分钟。 

#### 任务 2：部署 Azure 防火墙

在此任务中，你需要在虚拟网络中部署 Azure 防火墙 。 

1. 在 Azure 门户，Azure 门户页面顶部的 **“搜索资源，服务和文档”** 文本框中，键入 **“Firewalls”** 然后按 **Enter**键。

1. 在 **“防火墙”** 边栏选项卡上，单击 **“+ 添加”**。

1. 在 **“创建防火墙”** 边栏选项卡上，找到 **“基本信息”** 选项卡，请指定以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |资源组|**AZ500LAB08**|
   |名称|**测试 - FW01**|
   |区域|**（美国）美国东部**|
   |选择虚拟网络|单击 **“利用现有”** 选项，然后在下拉列表中选择 **"Test-FW-VN"**|
   |公共 IP 地址|单击 **“添加新的”**，然后输入名称 **“TEST-FW-PIP”**，然后点击 **“确定”**|

1. 单击 **“审阅+创建”**，然后单击 **“创建”**。 

    >**注意**：等待部署完成。该操作需约 5 分钟。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **Resource groups**，然后按 **Enter**键。

1. 在 **“资源组”** 边栏选项卡上，在资源组列表中，单击 **“AZ500LAB08”** 条目。

    >**注意**：在 **“AZ500LAB08”** 资源组边栏选项卡上，查看资源列表。你可以按 **“类型”** 排序 。

1. 在资源列表中，单击代表 **“Test-FW01”** 防火墙的条目。

1. 在 **“Test-FW01”** 边栏选项卡上，确定分配给防火墙的 **“专用 IP”** 地址。 

    >**注意**：你将需要在下一个任务中使用此信息。


#### 任务 3：创建一个默认路由

在此任务中，你将为 **“Workload-SN”** 子网创建默认路由。此路由将配置通过防火墙的出站流量。

1. 在 Azure 门户中，在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“路由表”**，然后按 **Enter**键。

1. 在 **“路由表”** 边栏选项卡上，单击 **“+ 添加”**。

1. 在 **“创建路由表”** 边栏选项卡上，指定以下设置：

   |设置|数值|
   |---|---|
   |名称|**防火墙-路由**|
   |资源组|**AZ500LAB08**|

1. 单击 **“创建”** 并等待预配完成。 

1. 在 **“路由表”** 边栏选项卡上，单击 **“刷新”**，然后在路由表列表中，单击 **“防火墙路由”** 条目。

1. 在 **“防火墙路由”** 边栏选项卡上，在 **“设置”** 部分单击 **“子网”**，然后在 **“防火墙路由 \| 子网”** 边栏选项卡上，单击 **“+ 关联”**。

1. 在 **“关联子网”** 边栏选项卡上，指定以下设置：

   |设置|数值|
   |---|---|
   |虚拟网络|**Test-FW-VN**|
   |子网|**工作负荷 - SN**|

    >**注意**：确保对该路由选择了 **“Workload-SN”** 子网，否则你的防火墙将无法正常运行。

1. 单击 **“确定”** 将防火墙与虚拟网络子网关联。 

1. 回到 **“防火墙路由”** 边栏选项卡，在 **“设置”** 部分，单击 **“路由”** 然后点击 **“添加”**。 

1. 在 **“添加路由”** 边栏选项卡中，指定以下设置：  

   |设置|数值|
   |---|---|
   |路由名称|**FW-DG**|
   |地址前缀|**0.0.0.0/0**
   |下一跃点类型|**虚拟设备**|
   |下一跃点地址|你在上一个任务中标识的防火墙专用 IP 地址|

    >**注意**：Azure 防火墙实际上是一项托管服务，但是在这种情况下虚拟设备可以运行。
	
1.  单击 **“确定”** 以添加路由。 


#### 任务 4：配置应用程序规则

在此任务中，你将创建一个应用程序规则，允许出站访问 `www.bing.com`。

1. 在 Azure 门户中，导航回 **“Test-FW01”** 边栏选项卡。

1. 在 **“Test-FW01”** 边栏选项卡的 **“设置”** 部分，单击 **“规则”**。

1. 在 **“Test-FW01 \| 规则”** 边栏选项卡中，单击 **“应用程序规则集合”** 选项卡，然后单击 **“+ 添加应用程序规则集合”**。

1. 在 **“添加应用程序规则集合”** 边栏选项卡中，指定以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |名称|**APP-Coll01**|
   |优先级|**200**|
   |操作|**允许**|

1. 在 **“添加应用程序规则集合”** 边栏选项卡上，在 **“目标FQDN”** 部分创建新的条目，其具有以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |名称|**AllowGH**|
   |源类型|**IP 地址**|
   |源|**10.0.2.0/24**|
   |协议端口|**http:80, https:443**|
   |目标 FQDN| **www.bing.com**|

1. 单击 **“添加”**，添加基于目标 FQDN 的应用程序规则。

    >**注意**：Azure 防火墙包含默认情况下允许基础架构 FQDN 的内置规则集合。这些 FQDN 特定于平台而设置，不能将其用于其他目的。 

#### 任务 5：配置网络规则

在此任务中，你将创建一个网络规则，允许出站访问端口 53 (DNS) 上的两个 IP 地址。

1. 在 Azure 门户中，导航到 **“Test-FW01”** 边栏选项卡。\| **“规则”** 边栏选项卡。

1. 在 **“Test-FW01 \| 规则”** 边栏选项卡，单击 **“网络规则集合”** 标签，然后单击 **“+ 添加网络规则集合”**。

1. 在 **“添加网络规则集合”** 边栏选项卡上，指定以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |名称|**Net-Coll01**|
   |优先级|**200**|
   |操作|**允许**|

1. 在 **“添加网络规则集合”** 边栏选项卡，在 **“IP 地址”** 部分，按照以下设置（保留其他默认值）创建新的进入：

   |设置|数值|
   |---|---|
   |名称|**AllowDNS**|
   |协议|**UDP**|
   |源类型|**IP 地址**|
   |源地址|**10.0.2.0/24**|
   |目标类型|**IP 地址**|
   |目标地址|**209.244.0.3，209.244.0.4**|
   |目标端口|**53**|

1. 单击 **“添加”** 以添加网络规则。

    >**注意**：在这种情况下使用的目标地址是已知的公共 DNS 服务器。 

#### 任务 6：配置虚拟机 DNS 服务器

在此任务中，你将为虚拟机配置主要和辅助 DNS 地址。这不是防火墙要求。 

1. 在 Azure 门户中，导航回 **AZ500LAB08-RG** 资源组。

1. 在 **“AZ500LAB08”** 边栏选项卡上，在资源列表中，单击 **“Srv-Work”** 虚拟机。

1. 在 **“Srv-Work”** 边栏选项卡的 **“设置”** 部分，单击 **“网络”**。

1. 在 **“Srv-Work \| 网络”** 边栏选项卡上，单击 **“网络接口”** 条目旁边的链接。

1. 在网络接口边栏选项卡上，在 **“设置”** 部分，单击 **“DNS 服务器”**，选择 **“自定义”** 选项，添加网络规则中引用的两个 DNS 服务器：**209.244.0.3** 和 **209.244.0.4**，然后单击 **“保存”** 以保存更改。

1. 返回 **“Srv-Work”** 虚拟机页面。

    >**注意**：等待更新完成。

    >**注意**：更新网络接口的 DNS 服务器将自动重新启动该接口所连接的虚拟机，如果适用，还会重新启动相同可用性集中的其他虚拟机。

#### 任务 7：测试防火墙

在此任务中，你将测试防火墙，确认其是否按预期工作。

1. 在 Azure 门户中，导航回 **AZ500LAB08-RG** 资源组。

1. 在 **“AZ500LAB08-RG”** 边栏选项卡的资源列表中，单击 **“Srv-Jump”** 虚拟机

1. 在 **“Srv-Jump”** 边栏选项卡，单击 **“连接”** 然后在下拉菜单中，单击 **“RDP”**。 

1. 单击 **“下载RDP文件”** 并使用它，通过远程桌面，连接到 **“Srv-Jump”** Azure VM。当提示进行身份验证时，请提供以下凭据：

   |设置|数值|
   |---|---|
   |用户名|**localadmin**|
   |密码|**Pa55w.rd1234**|

    >**备注**：在**Srv-Jump** Azure VM 远程桌面会话中，执行以下步骤。 

    >**备注**：你将连接到 **Srv-Work**虚拟机。这样做是为了让我们可以测试访问 bing.com 网站的能力。  

1. 在 **Srv-Jump**的远程桌面会话中，右键单击 **“开始”**，在右键菜单中，单击 **“运行”**，在 **“运行”** 对话框，运行以下命令连接到 **Srv-Work**。 

    ```
    mstsc /v:Srv-Work
    ```

1. 当提示要身份验证时，请提供以下凭据：

   |设置|数值|
   |---|---|
   |用户名|**localadmin**|
   |密码|**Pa55w.rd1234**|

    >**注意**：等待建立远程桌面会话并加载服务器管理器界面。

1. 在 **“Srv-Work”** 远程桌面会话的 **“服务器管理器”** 中，单击 **“本地服务器”**，然后单击 **“IE 增强安全配置”**。

1. 在 **“Internet Explorer 增强的安全配置”** 对话框中，将两个选项都设置为 **“关闭”**，然后单击 **“确定”**。

1. 在 **“Srv-Work”** 远程桌面会话中，启动 Internet Explorer 并浏览到 **`https://www.bing.com`**。 

    >**注意**：该网站应成功显示。防火墙允许你访问。

1. 浏览到 **`http://www.microsoft.com/`**

    >**注意**：在浏览器页面中，你应该收到一条消息，其内容类似于以下内容： `从 10.0.2.4:xxxxx 到 microsoft.com:80 的 HTTP 请求。操作：拒绝。没有匹配的规则。Proceeding with default action.` 这是正常的，因为防火墙阻止了对该网站的访问。 

1. 终止两个远程桌面会话。

> 结果：你已经成功配置和测试了 Azure 防火墙。

**清理资源**

> 记得删除不再使用的任何新建 Azure 资源。删除未使用的资源，确保不产生意外成本。 

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。  如果出现提示，请单击 **“PowerShell”** 和 **“创建存储”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB08" -Force -AsJob
    ```
1. 关闭 **“Cloud Shell”** 窗格。 