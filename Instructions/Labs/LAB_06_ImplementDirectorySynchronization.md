---
lab:
    title: '06 - 实现目录同步'
    module: '第 01 单元 - 管理标识和访问权限'
---

# 实验室 06：实现目录同步
# 学生实验室手册

## 实验室场景

你需要创建一个概念证明，以演示如何将本地 Active Directory 域服务 (AD DS) 环境与 Azure Active Directory (Azure AD) 租户集成。具体来说，你需要：

- 通过部署托管 AD DS 域控制器的 Azure VM 来实现单域 AD DS 林
- 创建和配置 Azure AD 租户
- 将 AD DS 林与 Azure AD 租户同步

> 对于本实验室中的所有资源，我们使用 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：部署托管 Active Directory 域控制器的 Azure VM
- 练习 2：创建和配置 Azure Active Directory 租户
- 练习 3：将 Active Directory 林与 Azure Active Directory 租户同步

### 练习 1：部署托管 Active Directory 域控制器的 Azure VM

### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：识别 Azure VM 部署的可用 DNS 名称
- 任务 2：使用 ARM 模板部署托管 Active Directory 域控制器的 Azure VM

#### 任务 1：识别 Azure VM 部署的可用 DNS 名称

在此任务中，你将为 Azure VM 部署标识 DNS 名称。 

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**： 使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。如果出现提示，请单击 **“PowerShell”** 和 **“创建存储”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令标识可用于本练习下一个任务中的 Azure VM 部署的可用 DNS 名称：

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**备注**： 将 `<custom-label>` 占位符替换为可能是全局唯一的有效 DNS 名称。将 `<location>` 占位符替换为要将 Azure VM 部署到其中的区域的名称，该 Azure VM 将托管在本实验室中使用的 Active Directory 域控制器。

    >**备注**： 若要标识可在其中预配 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

1. 验证命令是否返回 **True**。如果没有，请使用不同的 `<custom-label>` 值重新运行相同的命令，直到命令返回为 **True** 为止。

1. 记录返回正确结果的 `<custom-label>` 值。在下一个任务中需要使用它。

1. 关闭 Cloud Shell。

#### 任务 2：使用 ARM 模板部署托管 Active Directory 域控制器的 Azure VM

在此任务中要部署托管 Active Directory 域控制器的 Azure VM

1. 在同一浏览器窗口中打开另一个浏览器选项卡，然后导航到 [https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain)。 

1. 在 **“新建 Windows VM 并新建 AD 林、域和 DC”** 页面上，单击 **“部署到 Azure”**。这将自动将浏览器重定向到 Azure 门户中的 **“使用新的 AD 林创建 Azure VM”** 边栏选项卡。

1. 在 **“使用新的 AD 林创建 Azure VM”** 边栏选项卡中，单击 **“编辑参数”**。

1. 在 **“编辑参数”** 边栏选项卡中，单击 **“加载文件”**，在 **“打开”** 对话框中，单击 **“\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json”**，单击 **“打开”**，然后单击 **“保存”**。 

1. 在 **“使用新的 AD 林创建 Azure VM”** 边栏选项卡中，指定以下设置（将其他设置保留为默认值）：

   |设置|值|
   |---|---|
   |订阅|Azure 订阅的名称|
   |资源组|单击 **“新建”** 并键入名称 **AZ500LAB06**|
   |区域|你在上一个任务中标识的 Azure 区域|
   |管理员用户名|**Student**|
   |管理员密码|**Pa55w.rd1234**|
   |域名|**adatum.com**|
   |DNS 前缀|你在上一个任务中识别的 DNS 主机名|
   |VM 大小|**Standard_D2s_v3**|

1. 在 **“使用新的 AD 林创建 Azure VM”** 边栏选项卡中，单击 **“查看 + 创建”**，然后单击 **“创建”**。

    >**备注**： 不要等待部署完成，而是继续执行下一个练习。该部署可能需要 15 分钟左右。在本实验室的第三个练习中，你将使用此任务中部署的虚拟机。

> 结果：完成此练习后，你已经启动了 Azure VM 的部署，该 Azure VM 将使用 Azure 资源管理器模板来托管 Active Directory 域控制器


### 练习 2：创建和配置 Azure Active Directory 租户 

### 预计用时：20 分钟

在该练习中，你将完成以下任务：

- 任务 1：创建 Azure Active Directory (AD) 租户
- 任务 2：将自定义 DNS 名称添加到新的 Azure AD 租户
- 任务 3：使用全局管理员角色创建 Azure AD 用户

#### 任务 1：创建 Azure Active Directory (AD) 租户

在此任务中，你将新建一个在此实验室中使用的新 Azure AD 租户。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure Active Directory”**，然后按 **“Enter”** 键。

1. 在显示当前 Azure AD 租户的 **“概述”** 边栏选项卡上，单击 **“+ 创建租户”**。

1. 在 **“创建目录”** 边栏选项卡的 **“基础”** 选项卡上，确保选中 **“Azure Active Directory”** 选项，然后单击 **“下一步： 配置 >”**。

1. 在 **“创建目录”** 边栏选项卡的 **“配置”** 选项卡上，指定以下设置：

   |设置|值|
   |---|---|
   |组织名称|**AdatumSync**|
   |初始域名|由字母和数字组合组成的唯一名称|
   |国家或地区|**美国**|

    >**备注**： 记录初始域名。稍后将在本实验室用到它。

    >**备注**： **“初始域名”** 文本框中的绿色复选标记将指示你键入的域名是否有效且唯一。（记录初始域名以供以后使用）。

1. 单击 **“查看 + 创建”**，然后单击 **“创建”**。

    >**备注**： 等待新租户完成创建。使用 **“通知”** 图标监视部署状态。 

#### 任务 2：将自定义 DNS 名称添加到新的 Azure AD 租户

在此任务中，你将自定义 DNS 名称添加到新的 Azure AD 租户。 

1. 在 Azure 门户的工具栏中，单击位于 Cloud Shell 图标的右侧的 **“目录 + 订阅”** 图标。 

1. 在 **“目录 + 订阅”** 边栏选项卡中，单击新建的租户 **“AdatumSync”**。

    >**备注**： 如果 **AdatumSync** 条目未出现在 **“目录 +订阅”** 筛选器列表中，则可能需刷新浏览器窗口。

1. 在 **“AdatumSync”** 边栏选项卡中，在 **“管理”** 部分，单击 **“自定义域名”**。

1. 在 **“AdatumSync \| 自定义域名”** 边栏选项卡上，单击 **“+ 添加自定义域”**。

1. 在 **“自定义域名”** 边栏选项卡的 **“自定义域名”** 文本框中，键入 **“adatum.com”**，然后单击 **“添加域”**。

1. 在 **“adatum.com”** 边栏选项卡上，查看执行 Azure AD 域名验证所需的信息。

    >**备注**： 由于你没有 **adatum.com** DNS 域名，因此你将无法完成验证过程。这不会阻止你将 **adatum.com** AD DS 域与 Azure AD 租户同步。为此，你将使用 Azure AD 租户的初始 DNS 名称（其名称以 **onmicrosoft.com** 后缀结尾），该名称在上一个任务中已标识。但是，请记住，Active Directory 域的 DNS 域名和 Azure AD 租户的 DNS 名称因此将有所不同。这意味着，Adatum 用户在登录 AD DS 域和登录 Azure AD 租户时需要使用不同的名称。

#### 任务 3：使用全局管理员角色创建 Azure AD 用户

在此任务中，你将添加一个新的 Azure AD 用户并将其分配给全局管理员角色。 

1. 在 **“AdatumSync”** Azure AD 租户边栏选项卡中，在 **“管理”** 部分，单击 **“用户”**。

1. 在 **“用户 \| 全部用户”** 边栏选项卡上，单击 **“+ 新建用户”**。 

1. 在 **“新建用户”** 边栏选项卡上，确保选择 **“创建用户”** 选项，并指定以下设置（保留所有其他设置的默认值），然后单击 **“创建”**：

   |设置|值|
   |---|---|
   |用户名|**syncadmin**|
   |名称|**syncadmin**|
   |密码|确保选中选项 **“自动生成密码”**，然后单击 **显示密码**|
   |组|**已选择 0 个组**|
   |角色|单击 **“用户”**，然后单击 **“全局管理员”**，然后单击**选择**|
   |使用位置|**美国**|  

    >**备注**： 记录完整用户名。你可以通过单击显示域名的下拉列表右侧的 **“复制到剪贴板”** 按钮来复制其值。 

    >**备注**： 记录用户密码。你将在本实验室后面需要它。 

    >**备注**： 要实现 Azure AD Connect，需要具有全局管理员角色的 Azure AD 用户。

1. 打开 InPrivate 浏览器窗口。

1. 导航到 Azure 门户，并使用 **syncadmin** 用户帐户登录。出现提示时，将你之前在此任务中记录的密码更改为 **Pa55w.rd1234**。

    >**备注**： 要登录，你需要提供 **syncadmin** 用户帐户完全限定的名称，包括你此前在此任务中记录的 Azure AD 租户 DNS 域名。该用户名的格式为 syncadmin@`<your_tenant_name>`.onmicrosoft.com，其中 `<your_tenant_name>` 是占位符，代表唯一的 Azure AD 租户名称。 

1. 以 **syncadmin** 用户名注销并关闭 InPrivate 浏览器窗口。

> **结果**： 完成此练习后，你已创建 Azure AD 租户，为新的 Azure AD 租户添加了自定义 DNS 名称，并创建了具有全局管理员角色的 Azure AD 用户。


### 练习 3：将 Active Directory 林与 Azure Active Directory 租户同步

### 预计用时：20 分钟

在该练习中，你将完成以下任务：

- 任务 1：准备 AD DS 以进行目录同步
- 任务 2：安装 Azure AD Connect
- 任务 3：验证目录同步

#### 任务 1：准备 AD DS 以进行目录同步

在此任务中，你将连接到运行 AD DS 域控制器的 Azure VM 并创建目录同步帐户。 

   > 开始本任务之前，请确保在本实验室第一个练习中开始的模板部署已经完成。

1. 在 Azure 门户中，将 **“目录 + 订阅”** 筛选器设置为与在本实验室第一个练习中部署了 Azure VM 的 Azure 订阅关联的 Azure AD 租户。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“虚拟机”**，然后按 **Enter** 键。

1. 在 **“虚拟机”** 边栏选项卡中，单击 **“adVM”** 条目。 

1. 在 **“adVM”** 边栏选项卡中，单击 **“连接”**，然后在下拉菜单中单击 **“RDP”**。 

1. 在 **“IP 地址”** 参数中，选择 **“负载均衡器公共 IP 地址”**，然后单击 **“下载 RDP 文件”** 并使用它通过远程桌面连接到 **adVM** Azure VM。当系统提示你进行验证时，请提供以下凭据：

   |设置|值|
   |---|---|
   |用户名|**Student**|
   |密码|**Pa55w.rd1234**|

    >**备注**： 等待加载远程桌面会话和**服务器管理器**。  

    >**备注**： 在远程桌面会话中，对 **adVM** Azure VM 执行以下步骤。 

1. 在 **“服务器管理器”** 中，单击 **“本地服务器”**，然后单击 **“IE 增强型安全配置”**。

1. 在 **“Internet Explorer 增强的安全配置”** 对话框中，将两个选项都设置为 **“关闭”**，然后单击 **“确定”**。

1. 在 **“服务器管理器”** 中，单击 **“工具”**，然后在下拉菜单中单击 **“Active Directory 管理中心”**。

1. 在 **“Active Directory 管理中心”**，单击 **“adatum（本地）”**，在 **“任务”** 窗格中，单击 **“新建”**，然后在级联菜单中单击 **“组织单位”**。

1. 在 **“创建组织单位”** 窗口，在 **“名称”** 文本框中，键入 **“ToSync”**，然后单击 **“确定”**。

1. 双击新建的 **ToSync** 组织单位，使其内容显示在 Active Directory 管理中心控制台的“详细信息”窗格中。 

1. 在 **“任务”** 窗格中，在 **“ToSync”** 部分，单击 **“新建”**，然后在级联菜单中单击 **“用户”**。

1. 在 **“创建用户”** 窗口，使用以下设置新建用户帐户（将其他用户保留其现有值），然后单击 **“确定”**：

   |设置|值|
   |---|---|
   |全名|**aduser1**|
   |用户 UPN 登录|**aduser1**|
   |用户 SamAccountName 登录|**aduser1**|
   |密码|**Pa55w.rd1234**|
   |其他密码选项|**密码永不过期**|

#### 任务 2：安装 Azure AD Connect

在此任务中，你将在虚拟机上安装 AD Connect。 

1. 在与 **adVM** 的远程桌面会话中，启动 Internet Explorer，导航至 [Azure 门户](https://portal.azure.com)，然后使用你在上一个练习中创建的 **syncadmin** 用户帐户登录。出现提示时，指定你记录的完整用户名以及 **“Pa55w.rd1234”** 密码。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure Active Directory”**，然后按 **“Enter”** 键。

1. 在 Azure 门户中，在 **“AdatumSync \| 概述”** 边栏选项卡中，单击 **“Azure AD Connect”**。

1. 在 **“AdatumSync \| Azure AD Connect”** 边栏选项卡中，单击 **“下载 Azure AD Connect”** 链接。你将被重定向到 **“Microsoft Azure Active Directory Connect”** 下载页面。

1. 在 **“Microsoft Azure Active Directory Connect”** 下载页面，单击 **“下载”**。

1. 出现提示时，单击 **“运行”** 以启动 **“Microsoft Azure Active Directory Connect”** 向导。

1. 在 **Microsoft Azure Active Directory Connect** 向导中的 **“欢迎使用 Azure AD Connect”** 页面，单击复选框 **“我同意许可条款和隐私声明”**，然后单击 **“继续”**。

1. 在 **Microsoft Azure Active Directory Connect** 向导中的 **“快速设置”** 页面中，单击 **“自定义”** 选项。

1. 在 **“安装所需组件”** 页面上，取消选择所有可选配置选项，然后单击 **“安装”**。

1. 在 **“用户登录”** 页面上，确保仅启用 **“密码哈希同步”**，然后单击 **“下一步”**。

1. 在 **“连接到 Azure AD”** 页面上，请使用你在上一个练习中创建的 **syncadmin** 用户帐户凭据进行身份验证，然后单击 **“下一步”**。 

1. 在 **“连接目录”** 页面上，单击 **“adatum.com”** 林条目右侧的 **“添加目录”** 按钮。

1. 在 **“AD 林帐户”** 窗口，请确保已选择 **“新建 AD 帐户”** 选项，指定以下凭据，然后单击 **“确定”**：

   |设置|值|
   |---|---|
   |用户名|**ADATUM\\Student**|
   |密码|**Pa55w.rd1234**|

1. 返回到 **“连接目录”** 页面，确保 **adatum.com** 条目显示为已配置的目录，然后单击 **“下一步”**

1. 在 **“Azure AD 登录配置”** 页面上，请注意以下警告： **如果 UPN 后缀与已验证域名不匹配，则用户将无法使用本地凭据登录 Azure AD**，请启用复选框 **继续，不将所有 UPN 后缀与已验证域匹配**，然后单击 **“下一步”**。

    >**备注**： 如前所述，这很正常，因为你无法验证自定义 Azure AD DNS 域 **adatum.com**。

1. 在 **“域和 OU 筛选”** 页面上，单击选项 **“同步所选域和 OU”**，清除所有复选框，仅单击 **ToSync** OU 旁边的复选框，然后单击 **“下一步”**。

1. 在 **“唯一标识用户”** 页面上，接受默认设置，然后单击 **“下一步”**。

1. 在 **“筛选用户和设备”** 页面上，接受默认设置，然后单击 **“下一步”**。

1. 在 **“可选功能”** 页面上，接受默认设置，然后单击 **“下一步”**。

1. 在 **“已准备好进行配置”** 页面上，确保 **“配置完成后启动同步过程”** 复选框已选择，然后单击 **“安装”**。

    >**备注**： 安装需要约 2 分钟。

1. 查看 **“配置完成”** 页面上的信息，并单击 **“退出”** 关闭 **Microsoft Azure Active Directory Connect** 窗口。


#### 任务 3：验证目录同步

在此任务中，你将验证目录同步工作是否正常。 

1. 在与 **adVM** 的远程桌面会话中，在显示 Azure 门户的 Internet Explorer 窗口中，导航到 Adatum Lab Azure AD 租户的 **“用户 - 所有用户”** 边栏选项卡。

1. 在 **“用户 \| 所有用户”** 边栏选项卡上，请注意，用户对象列表包括 **aduser1** 帐户。 

1. 选择 **aduser1** 帐户，在 **“个人资料”>“标识”** 部分，请注意， **“来源”** 属性设置为 **“Windows Server AD”**。

    >**备注**： 可能需要等待几分钟，然后选择 **“刷新”**，以便显示 **“aduser1”** 用户帐户。

1. 在 **“用户 \| 所有用户”** 边栏选项卡上，选择 **“aduser1”** 条目。

1. 在 **“aduser1 \| 个人资料”** 边栏选项卡上，在 **“职位信息”** 部分，请注意 **“部门”** 属性未设置。

1. 在与 **adVM** 的远程桌面会话中，切换到 **Active Directory 管理中心**，在 **ToSync** OU 的对象列表中选择 **“aduser1”** 条目，然后在 **“任务”** 窗格中的 **“ToSync”** 部分，选择 **“属性”**。

1. 在 **“aduser1”** 窗口中的 **“组织”** 部分，在 **“部门”** 文本框中键入 **“销售”**，然后选择 **“确定”**。

1. 在与 **adVM** 的远程桌面会话中，启动 **“Windows PowerShell”**。

1. 从 **“管理员: Windows PowerShell”** 控制台中，运行以下命令，以启动 Azure AD Connect 增量同步：

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

1. 切换到显示 **“aduser1 \| 个人资料”** 边栏选项卡的 Internet Explorer 窗口，刷新页面，注意 **“部门”** 属性设置为 **“销售”**。

    >**备注**： 可能需要等待一分钟，如 **“部门”** 属性仍未设置请再次刷新页面。

> **结果**： 完成本练习后，你已准备好 AD DS 进行目录同步，已安装 Azure AD Connect，并已验证目录同步。


**清理资源**

>**备注**： 首先禁用 Azure AD 同步

1. 在与 **adVM** 的远程桌面会话中，以管理员身份启动 Windows PowerShell。

1. 在 Windows PowerShell 控制台中，通过运行以下命令安装 MsOnline PowerShell 模块（系统提示时，在“需要继续使用 NuGet 提供程序”对话框中，键入 **“是”**，然后按 Enter。）

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

1. 在 Windows PowerShell 控制台中，运行以下命令连接到 AdatumSync Azure AD 租户（系统提示时，请使用 **syncadmin** 凭据登录）：

    ```powershell
    Connect-MsolService
    ```

1. 在 Windows PowerShell 控制台中，通过运行以下命令禁用 Azure AD Connect 同步：

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

1. 在 Windows PowerShell 控制台中，通过运行以下命令验证操作是否成功：

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```

    >**备注**： 结果应该为 `False`。如果不是这样，请等待一分钟，然后重新运行该命令。

    >**备注**： 接下来，删除 Azure 资源

1. 在 Azure 门户中，将 **“目录 + 订阅”** 筛选器设置为与你在其中部署 **adVM** Azure VM 的 Azure 订阅相关联的 Azure AD 租户。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 

1. 在“Cloud Shell”窗格左上角的下拉菜单中，选择 **“PowerShell”**，然后在出现提示时，单击 **“确认”**。 

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
1. 关闭 **Cloud Shell** 窗格。

    >**备注**： 最后，删除 Azure AD 租户

1. 返回 Azure 门户，使用**目录 + 订阅**筛选器切换到 **AdatumSync** Azure Active Directory 租户。

1. 在 Azure 门户中，导航到 **“用户 - 所有用户”** 边栏选项卡，单击代表 **syncadmin** 用户帐户的条目，在 **“syncadmin - 个人资料”** 边栏选项卡中，单击 **“删除”**，然后在提示确认时，单击 **“确定”**。

1. 重复相同的步骤以删除 **aduser1** 用户帐户。

1. 导航到 Azure AD 租户的 **“AdatumSync - 概述”** 边栏选项卡，单击 **“删除租户”**，在 **“删除目录‘AdatumSync’”** 边栏选项卡中，单击 **“获取删除 Azure 资源的权限”** 链接，在 Azure Active Directory 的 **“属性”** 边栏选项卡中，将 **“Azure 资源的访问管理”** 设置为 **“是”**，然后单击 **“保存”**。

1. 从 Azure 门户注销，然后重新登录。 

1. 导航回 **“删除目录 ‘AdatumSync’”** 边栏选项卡，然后单击 **“删除”**。

> 有关此任务的更多信息，请参阅 [https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto)
