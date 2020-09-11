---
lab:
    title: '10 - 密钥保管库（通过设置 Always Encrypted 来实现安全数据）'
    module: '模块 03 - 安全数据和应用程序'
---

# 实验室 10：密钥保管库（通过设置 Always Encrypted 来实现安全数据）
# 学生实验室手册

## 实验场景

你被要求创建一个概念证明应用程序，该应用程序将利用 Azure SQL 数据库对 Always Encrypted 功能的支持。在这种情况下使用的所有机密和密钥应存储在密钥保管库中。应在 Azure Active Directory (Azure AD) 中注册该应用程序，以增强其安全状况。为了实现这些目标，概念证明应包括：

- 创建 Azure 密钥保管库并在保管库中存储密钥和机密。
- 创建一个 SQL 数据库并使用 Always Encrypted 加密数据库表中的列内容。

> 对于本实验室中的所有资源，我们使用的是 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将成功完成以下练习：

- 练习 1：使用密钥和机密配置密钥保管库
- 练习 2：创建应用程序来演示如何使用密钥保管库进行加密

## 实验室文件：

- **Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**
- **Allfiles\\Labs\\10\\az-500-10_azuredeploy.parameters.json**
- **Allfiles\\Labs\\10\\program.cs**
 
### 练习 1：使用密钥和机密配置密钥保管库

### 预计用时：60 分钟

> 对于本实验室中的所有资源，我们正在使用 **东部（美国）** 地区。与你的教师确认这是你上课时使用的区域。 

在该练习中，你将完成以下任务：

- 任务 1：使用 Visual Studio 2019 和 SQL Server Management Studio 部署和配置 Azure VM
- 任务 2：创建和配置密钥保管库
- 任务 3：将密钥添加到密钥保管库
- 任务 4：向密钥保管库添加机密

#### 任务 1：（可选先决条件）使用 Visual Studio 2019 和 SQL Server Management Studio 部署和配置 Azure VM
    
>**注意**：如果使用同时安装了 Visual Studio 2019 和 SQL Server Management Studio 的实验室提供程序，则可以跳过此步骤并直接前往任务 2。

在此任务中，你将部署 Azure VM，连接它，并下载和安装 Visual Studio 2019 和 SQL Server Management Studio (SSMS)。

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 在 Azure 门户中，在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“部署自定义模板”**，然后按 **Enter** 键。

1. 在 **“自定义部署”** 边栏选项卡中，单击 **“在编辑器中构建自己的模板”** 选项。

1. 在 **“编辑模板”** 边栏选项卡，单击 **“加载文件”**，找到 **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** 文件并单击 **“打开”**。

1. 在 **“编辑模板”** 边栏选项卡上，单击 **“保存”**。

1. 返回 **“自定义部署”** 边栏选项卡，单击 **“编辑参数”**。

1. 在 **“编辑参数”** 边栏选项卡，单击 **“加载文件”**，找到 **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.parameters.json** 文件并单击 **“打开”**。

1. 在 **“编辑模板”** 边栏选项卡上，单击 **“保存”**。

1. 在 **“自定义部署”** 边栏选项卡上，请确保已配置以下设置（将其他设置保留为默认值）：

   |设置|数值|
   |---|---|
   |订阅|在本次实验室中你将使用的 Azure 订阅名|
   |资源组|单击 **“新建”** 并键入名称 **“AZ500LAB10”**|
   |地点|**（美国）美国东部**|
   |VM 大小|**“Standard_D2s_v3”**|
   |VM 名称|**az500-10-vm1**|
   |管理员用户名|**Student**|
   |管理员密码|**Pa55w.rd1234**|
   |虚拟网络名称|**az500-10-vnet1**|

    >**注意**：若要标识可在其中配置 Azure VM 的 Azure 区域，请参阅 [**https://azure.microsoft.com/zh-cn/regions/offers/**](https://azure.microsoft.com/zh-cn/regions/offers/)

1. 单击 **“查看 + 创建”**，然后单击 **“创建”**。

    >**注意**：等待部署完成。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Virtual machines”** ，然后按  **Enter**键。

1. 在 **“虚拟机”** 边栏选项卡上，单击 **“az500-10-vm1”** 条目。 

1. 在 **“az500-10-vm1”** 边栏选项卡，单击 **“连接”**，然后在下拉菜单中单击 **“RDP”**。 

1. 请单击 **“下载 RDP 文件”**，并通过远程桌面将其连接到 **az500-10-vm1** Azure VM。当提示进行身份验证时，请提供以下凭据：

   |设置|数值|
   |---|---|
   |用户名|**Student**|
   |密码|**Pa55w.rd1234**|

    >**注意**：等待加载远程桌面会话和**服务器管理器**。  

    >**注意**：本实验室中的其余步骤在 **az500-10-vm1** Azure VM 的远程桌面会话中执行。 

1. 在 **az500-10-vm1** Azure VM 的远程桌面会话中的 **“服务器管理器”** 中，单击 **“本地服务器”**，然后再单击 **“IE 增强的安全配置”**。

1. 在 **“Internet Explorer 增强的安全配置”** 对话框中，将两个选项都设置为 **“关闭”**，然后单击 **“确定”**。

1. 启动 **Internet Explorer**，然后浏览到[下载 SQL Server Management Studio](https://docs.microsoft.com/zh-cn/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver15) 页面。

1. 下载并启动 SQL Server Management Studio 安装程序。  

    >**注意**：继续下一步，而无需等待 SQL Server Management Studio 安装完成。 

1. 启动 **Internet Explorer**，然后浏览到 [Visual Studio下载](https://visualstudio.microsoft.com/downloads/)页面。

1. 启动 Visual Studio 2019 社区版的下载和安装。出现提示时，在 **“Visual Studio 安装程序”** 窗口中，单击 **“继续”**。

1. 出现提示时，在 **“工作负载”** 窗口中的 **“台式计算机机和移动设备”** 部分，选择 **“.NET桌面开发”** 复选框，然后点击 **“\安装”**。

    >**注意**：继续下一步，无需等待 Visual Studio 2019 安装完成。 

> 结果：已经部署并启动了将在本实验室的第二个练习中使用的 Azure VM **az500-10-vm1** 配置。

#### 任务 2：创建并配置密钥保管库。

在此任务中，将创建一个实验室资源组和一个密钥保管库。还将配置密钥保管库权限。

1. 在连接到 **az500-10-vm1** Azure VM 的远程桌面会话中，登录到 Azure 门户 **`https://portal.azure.com/`**。

    >**注意**：使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。如果出现提示，请选择 **“PowerShell”** 和 **“创建储存”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令在资源组 **AZ500LAB10** 中创建密钥保管库 。密钥保管库名称必须唯一。记住已选择的名称。整个实验室都需要它。  

1. 如果跳过了任务 1，请运行以下命令来创建资源组，否则请前往下一个任务

    ```powershell
    New-AzResourceGroup -Name 'AZ500LAB10' -Location eastus
    ```

1. 创建密钥保管库

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)
    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location 'eastus'
    ```

    >**注意**：其输出显示保管库名称和保管库 URI。保管库 URI 的格式为 `https://<vault_name>.vault.azure.net/`

1. 关闭“Cloud Shell”窗格。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **Resource groups**，然后按 **Enter**键。

1. 在 **“资源组”** 边栏选项卡，在资源组列表中，单击 **“AZ500LAB10”** 条目。

1. 在 **“AZ500LAB10-RG”** 边栏选项卡上，单击表示新创建密钥保管库的条目。 

1. 在密钥保管库的边栏选项卡上， 在 **“设置”** 部分，单击 **“访问策略”**，然后单击 **“添加访问策略”**。

1. 在 **“添加访问策略”** 边栏选项卡上，请指定以下设置（将所有其他设置保留为默认值）： 

    |设置|数值|
    |----|----|
    |从模板配置（可选）|**密钥、机密和证书管理**|
    |密钥权限|单击 **“全选”** 会导致 **“已选 16 个”** 权限|
    |机密权限|单击 **“全选”** 会导致总计 **“已选 8 个”** 权限|
    |证书权限|单击 **“全选”** 会导致总计 **“已选 16 个”** 权限|
    |选择主体|单击 **“未选择”**，在 **“主体”** 边栏选项卡，选择你的用户帐户，然后单击 **“选择”**|

1. 回到 **“添加访问策略”** 边栏选项卡，单击 **“添加”** 以添加访问策略，然后回到密钥保管库的“访问策略”边栏选项卡，单击 **“保存”** 以保存你的更改。 

#### 任务 3：向密钥保管库添加密钥

在此任务中，你将向密钥保管库添加密钥并查看有关密钥的信息。 

1. 在 Azure 门户中，在“Cloud Shell”窗格打开 PowerShell 会话。

1. 确保在“Cloud Shell”窗格左上方的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以将软件保护的密钥添加到密钥保管库： 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**注意**：密钥的名称为 MyLabKey

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以验证密钥是否已创建：

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以显示密钥标识符：

    ```powershell
    $key.key.kid
    ```

1. 最小化“Cloud Shell”窗格。 

1. 回到 Azure 门户，在密钥保管库的边栏选项卡上，在 **“设置”** 部分，单击 **“密钥”**。

1. 在密钥列表中，单击 **“MyLabKey”** 条目，然后，在 **“MyLabKey”** 边栏选项卡上，单击代表密钥当前版本的条目。

    >**备注**：检查有关你创建的密钥的信息。

    >**备注**：你可以使用密钥标识符来引用任何密钥。如要获取最新版本，请参考 `https://<key_vault_name>.vault.azure.net/keys/MyLabKey` 或通过以下方式获取具体版本：`https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### 任务 4：向密钥保管库添加机密

1. 还原 Cloud Shell 窗格。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令创建具有安全字符串值的变量：

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

1.  在“Cloud Shell ”窗格的 PowerShell 会话中，运行以下命令将机密添加到保管库：

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**注意**：机密名称为 SQLPassword。 

1.  在 Cloud Shell 窗格的 PowerShell 会话中，运行以下命令验证是否已创建机密。

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

1. 最小化“Cloud Shell”窗格。 

1. 在 Azure 门户中，导航回密钥保管库边栏选项卡，并且在 **“设置”** 部分，单击 **“机密”**。

1. 在机密列表中，单击 **"SQL 密码"** 条目，然后在 **"SQL 密码"** 边栏选项卡上，单击代表机密当前版本的条目。

    >**备注**：检查创建的机密信息。

    >**备注**：要获取最新版本的机密，请参考 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` 或参考 `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>` 获取特定版本


### 练习 2：创建应用程序来演示如何使用密钥保管库进行加密

### 预计用时：60 分钟

在该练习中，你将完成以下任务：

- 任务 1：启用客户端应用程序访问 Azure SQL 数据库服务。 
- 任务 2：创建一个可使应用程序访问密钥保管库的策略
- 任务 3：创建 Azure SQL 数据库。
- 任务 4：在 SQL 数据库中创建一个表，然后选择要加密的数据列。
- 任务 5：构建一个控制台应用程序以使用加密列

#### 任务 1：启用客户端应用程序访问 Azure SQL 数据库服务。 

在此任务中，可启用客户端应用程序访问 Azure SQL 数据库服务。此操作可以通过设置所需的身份验证并获取对应用程序进行身份验证所需的应用程序 ID 和密钥来完成。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“应用注册”** 然后按 **“Enter”** 键。

1. 在 **“应用注册”** 选项边栏卡，单击 **“新注册”**。 

1. 在 **“注册应用程序”** 边栏选项卡，指定以下设置（所有其他设置保留为默认值）：

    |设置|数值|
    |----|----|
    |名称|**sqlApp**|
    |重定向的 URI（可选）|**Web** 和 **https://sqlapp**|

1. 在 **“注册应用程序”** 边栏选项卡上，单击 **“注册”**。 

    >**注意**：注册完成后，浏览器将自动将你重定向到 **“sqlApp”** 边栏选项卡。 

1. 在 **“sqlApp”** 边栏选项卡上，找到 **“应用程序(客户端) ID”** 的值。 

    >**注意**：记录此值。你将在下一个任务中需要它。

1. 在 **“sqlApp”** 边栏选项卡的 **“管理”** 部分，单击**证书和机密**。

1. 在 **“sqlApp”** \| 在 **“证书和机密”** 边栏选项卡上，单击 **“新客户端密码”**。

1. 在 **“添加客户端机密”** 窗格中，指定以下设置：

    |设置|数值|
    |----|----|
    |描述|**Key1**|
    |到期|**1 年**|
	
1. 单击 **“添加”** 更新应用程序的凭据。

1. 在 **“sqlApp”** \| 在 **“证书和机密”** 边栏选项卡，识别 **“Key1”** 的值。

    >**注意**：记录此值。你将在下一个任务中需要它。 

    >**备注**：请确保在离开边栏选项卡之**前**复制该值。此时，将不再可能获取其明文值。


#### 任务 2：创建一个可使应用程序访问密钥保管库的策略

在此任务中，将授予新注册应用对密钥保管库中存储的机密访问权限。

1. 在 Azure 门户中，在“Cloud Shell”窗格打开 PowerShell 会话。

1. 确保在“Cloud Shell”窗格左上方的下拉菜单中选中 **“PowerShell”**。

1. 在 “Cloud Shell” 窗格中的 PowerShell 会话中，运行以下命令创建存储上一个任务中记录的**应用程序（客户端）ID**的变量（用 **Application (client) ID**的值替换 `<Azure_AD_Application_ID>` 占位符）：
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
1. 在 Cloud Shell 窗格中的 PowerShell 会话中，运行以下命令创建存储密钥保管库名称的变量。
	```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令将密钥保管库的权限授予在上一个任务中注册的应用程序：

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

1. 关闭“Cloud Shell”窗格。 

#### 任务 3：创建 Azure SQL 数据库

在此任务中，将创建一个 Azure SQL 数据库并标识其 ADO.NET 连接字符串。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“SQL 数据库”**，然后按 **Enter** 键。

1. 在 **“SQL 数据库”** 边栏选项卡中，单击 **“添加”**。

1. 在 **“创建 SQL 数据库”** 边栏选项卡的 **“基本信息”** 选项卡，指定以下设置（其他设置保留为默认值）：

    |设置|数值|
    |---|--- |
    |订阅|在本次实验室中你将使用的 Azure 订阅名|
    |资源组|**AZ500LAB10**|
    |数据库名称|**医疗**|

1. 在服**务器**下拉列表下方，单击 **“新建”**， 并在 **“新建服务器”** 边栏选项卡，指定以下设置并单击 **“确定”**（其他设置保留默认值）：

    |设置|数值|
    |---|---|
    |服务器名称|任何有效的全局唯一名称|
    |服务器管理员登录|**Student**|
    |密码|**Pa55w.rd1234**|
    |位置|**（美国）美国东部**|

1. 单击 **“审阅+创建”**，然后单击 **“创建”**。 

    >**备注**：等待创建 SQL 区域。预配大约需要 2 分钟。 

1. 在 Azure 门户中，导航回 **“SQL 数据库”** 边栏选项卡，然后在 SQL 数据库列表中，单击 **“medical”** 条目。

1. 在 SQL数据库边栏选项卡的 **“设置”** 部分，单击 **“连接字符串”**。 

    >**注意**：该接口包括 ADO.NET、JDBC、ODBC、PHP 和 Go 的连接字符串。 
   
1. 记录 **ADO.NET 连接字符串**。

    >**备注**：使用连接字符串时，请确保将 `{your_password}` 占位符替换为 **Pa55w.rd1234**。


#### 任务 4：在 SQL 数据库中创建一个表，然后选择要加密的数据列

>**备注**：在继续执行此任务之前，请确保在本实验室开始时启动的 SQL Server Management Studio 的安装已完成。

在此任务中，将使用 SQL Management Studio 连接到 SQL 数据库并创建一个表。然后，使用 Azure Key Vault 中自动生成的密钥对两个数据列进行加密。 

1. 在 Azure 门户的 **“medical”** SQL 数据库边栏选项卡上，单击 **“概述”**，标识 **“服务器名称”**，然后单击 **“设置服务器防火墙”**。  

    >**备注**：记录服务器名称。此任务稍后需要服务器名称。

1. 在 **“防火墙设置”** 边栏选项卡上，单击 **“+ 添加客户端 IP”**，然后单击 **“保存”**，再单击 **“确定”**。

    >**备注**：此操作将修改服务器防火墙设置，从而允许从与当前使用的计算机关联的公用 IP 地址连接到 medical 数据库。 

1. 单击 **“开始”**， 在 **“开始”** 菜单中，展开 **“Microsoft SQL Server Tools 18”** 文件夹，然后单击 **“Micosoft SQL Server Management Studio”** 菜单项。

1. 在 **“连接到服务器”** 对话框中，指定以下设置： 

    |设置|数值|
    |---|---|
    |服务器类型| **数据库引擎**|
    |服务器名称|此任务之前标识的服务器名称|
    |身份验证：**SQL Server 身份验证**
    |登录|**Student**|
    |密码|**Pa55w.rd1234**|

1. 在 **“连接到服务器”** 对话框中，单击 **“连接”**。

1. 在 **“SQL Server Management Studio”** 控制台，在 **“对象浏览器”** 窗格，展开 **“数据库”** 文件夹。

1. 在 **“对象资源管理器”** 窗格，右键单击 **“医疗”** 数据库，然后单击 **“新查询”**。

1. 将以下代码粘贴到查询窗口中，然后单击 **“执行”**。这将创建一**个患者**表。

     ```sql
     CREATE TABLE [dbo].[Patients](
		[PatientId] [int] IDENTITY(1,1),
		[SSN] [char](11) NOT NULL,
		[FirstName] [nvarchar](50) NULL,
		[LastName] [nvarchar](50) NULL,
		[MiddleName] [nvarchar](50) NULL,
		[StreetAddress] [nvarchar](50) NULL,
		[City] [nvarchar](50) NULL,
		[ZipCode] [char](5) NULL,
		[State] [char](2) NULL,
		[BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
1. 成功创建表后，在 **“对象浏览器”** 窗格中，展开 **“medical”** 数据库节点、 **“表”** 节点，右键单击 **“dbo.Patients”** 节点，然后单击 **“加密列”**。 

    >**备注**：这将启动 **Always Encrypted** 向导显示。 

1. 在 **“简介”** 页上，单击 **“下一步”**。

1. 在 **“列选择”** 页面上，选择 **“SSN”** 和 **“生日”** 列，将 **“SSN”** 的 **“加密类型”** 设置为 **“确定性的”**，将 **“生日”** 列的“加密类型”设置为 **“随机的”**，然后单击 **“下一步”**。

1. 在 **“主密钥配置”** 页面上，选择 **“Azure 密钥保管库”**，单击 **“登录”**，当出现提示时，请使用本实验室前面部分中用于配置 Azure 密钥保管库实例的用户帐户进行身份验证，请确保该密钥保管库显示在 **“选择一个 Azure 密钥保管库”** 下拉列表，然后单击 **“下一步”**。

1. 在 **“运行设置”** 页面，单击 **“下一步”**。
	
1. 在 **“摘要”** 页面上，单击 **“完成”** 以继续进行加密。出现提示时，使用你之前在本实验室中用于配置 Azure 密钥保管库实例的相同用户帐户再次登录。

1. 加密过程完成后，在 **“结果”** 页面上，单击 **“关闭”**。

1. 在 **“SQL Server Management Studio”** 控制台，在 **“对象资源管理器”** 窗格中的 **“医疗”** 节点下，展开 **“安全”** 和 **“始终加密的密钥”** 子节点。 

    >**备注**：**始终加密的密钥**子节点包含**列主密钥**和**列加密密钥**子文件夹。


#### 任务 5：构建一个控制台应用程序以使用加密列

>**备注**：在继续执行此任务之前，请确保已完成在本实验室开始时启动的 Visual Studio 2019 安装。

接着，使用 Visual Studio 创建一个控制台应用程序，以将数据加载到加密列中，然后使用访问密钥保管库中的密钥的连接字符串安全地访问该数据。

1. 切换到显示 Visual Studio 2019 欢迎消息的窗口，单击 **“不是现在，也许以后”** 链接，然后单击 **“启动 Visual Studio”**。

1. 在 **“开始”** 页面上，单击 **“创建新项目”**。 

1. 在项目模板列表中，搜索 **“控制台应用 (.NET Framework)”**，在结果列表中，单击 **“C#”** 对应的 **“控制台应用 (.NET Framework)”**，然后单击 **“下一步”**。

1. 在 **“配置新项目”** 页面上，指定以下设置（将其他设置保留为默认值）：

    |设置|数值|
    |---|---|
    |项目名称|**OpsEncrypt**|
    |解决方案名称|**OpsEncrypt**|
    |框架|**NET Framework 4.7.2.**|

1. 在 Visual Studio 控制台中，单击 **“工具类”** 菜单，在下拉菜单中，单击 **“NuGet 程序包管理器”**，然后在级联菜单中单击 **“程序包管理器控制台”**。

1. 在 **“程序包管理器控制台”** 窗格，运行以下命令安装所需的 **NuGet** 程序包：

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

1. 导航到 **\\Allfiles\\Labs\\10\\program.cs**，在记事本中将其打开，然后将其内容复制到剪贴板中。

1. 切换到 Visual Studio 控制台，在 **“解决方案浏览器”** 窗口，单击 **“Program.cs”** 并将其内容替换为你复制到剪贴板中的代码。

1. 在 Visual Studio 窗口中，**“Program.cs”** 窗格中的第 15 行，用你之前在本实验室中记录的 Azure SQL 数据库 **“ADO.NET”** 连接字符串替换 `<connection string noted earlier>` 占位符。

1. 在 Visual Studio 窗口中，**“Program.cs”** 窗格中的第 16 行，将 `<client id noted earlier>` 占位符替换为你之前在实验室中记录的已注册应用的 **“应用程序（客户端）ID”** 值。 

1. 在 Visual Studio 窗口中，**“Program.cs”** 窗格中的第 17 行，将 `<key value noted earlier>` 占位符替换为你之前在实验室中记录的已注册应用的 **Key1**值。 

1. 在 Visual Studio 控制台中，单击 **“开始”** 按钮以启动控制台应用程序的构建并开启它。

1. 该应用程序将启动“命令提示符”窗口。当提示你输入密码时，键入 **“Pa55w.rd1234”** 以连接到 Azure SQL 数据库。 

1. 让控制台应用保持运行，然后切换到 **“SQL Management Studio”** 控制台。 

1. 在 **“对象浏览器”** 窗格中，右键单击“医学数据库”，然后在右键菜单中单击 **“新建查询”**。

1. 在查询窗口中运行以下查询，验证从控制台应用加载到数据库中的数据是否已加密。

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

1. 返回到控制台应用程序，在这里会提示输入有效 SSN。这将查询数据的加密列。在“命令提示符”处，键入以下内容，然后按 Enter 键：

    ```cmd
    999-99-0003
    ```

    >**备注**：验证查询返回的数据未加密。

1. 要终止控制台应用，请按 Enter 键

**清理资源**

> 记得删除不再使用的任何新建 Azure 资源。删除未使用的资源，确保不产生意外成本。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。  

1. 在“Cloud Shell”窗格的左上下拉菜单中，选择 **“PowerShell”** 并在提示时单击 **“确认”**。

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以删除你在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

1.  关闭 **“Cloud Shell”** 窗格。 
