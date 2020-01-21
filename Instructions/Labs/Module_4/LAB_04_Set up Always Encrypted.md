

# 模块 4：通过设置 Always Encrypted 来实施安全数据


**场景**

本模块包括以下任务：

 - Azure 机密计算
 - Azure Azure 密钥保管库


## 练习 1：Azure 密钥保管库简介


**场景**

在本实验中，你将开始使用 Azure 密钥保管库在 Azure 中创建加固的容器（保管库），以在 Azure 中存储和管理加密密钥和机密。首先，你将使用 Azure PowerShell。然后，将密码存储为机密，然后可以与 Azure 应用程序一起使用。

### 任务 1：下载 SQL Server Management Studio

1.  要下载此实验所需的最新版本的 SQL Management Studio，请访问以下链接，然后选择下载 SQL Management Studio**`https://docs.microsoft.com/zh-cn/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017`**



### 任务 2：使用 PowerShell 创建密钥保管库


在本练习中，你将使用 PowerShell 创建 Azure 密钥保管库。


1.  通过单击“启动”> **PowerShell**，启动 PowerShell

2.  使用以下命令，使用你的 Azure 订阅帐户向 Azure 进行身份验证。

     ```powershell
    Login-AzureRMAccount
     ```

4.  创建新资源组。 

     ```powershell
    New-AzureRmResourceGroup -Name 'KeyVaultPSRG' -Location 'eastus'
     ```


5.  在资源组中创建一个密钥保管库。**VaultName 必须是唯一的。**

     ```powershell
    New-AzureRmKeyVault -VaultName 'KeyVaultPS' -ResourceGroupName    'KeyVaultPSRG' -Location 'eastus'
     ```

    **注**：此输出显示重要信息：在这种情况下，密钥保管库名为 KeyVaultPS 和保管库 URI： `https://KeyVaultPS.vault.azure.net`



6.  在 Azure 门户中打开 **KeyVaultPSRG** 资源组。

7.  单击“KeyVaultPS”以检查你创建的内容。

### 任务 3：向密钥保管库添加密钥和机密

1.  返回到 PowerShell 窗口。

2.  使用此命令将软件保护的密钥添加到密钥保管库。确保将占位符文本更改为你的保管库名称。

     ```powershell
    $key = Add-AzureKeyVaultKey -VaultName '<YourVaultName>' -Name 'MyLabKey'     -Destination 'Software'
     ```

3.  在 Azure 门户中，返回到**“KeyVaultPS”**。单击左侧导航栏“设置”下的密钥。

4.  单击**“MyLabKey”**

5.  单击当前版本

6.  检查有关你创建的密钥的信息。

    **注**：你始终可以通过使用其 URI 来引用此密钥。要获取最新版本，请参考`https://keyvaultps.vault.azure.net/keys/MyLabKey/`，或者如果需要其为确切版本： `https://keyvaultps.vault.azure.net/keys/MyLabKey/da1a3a1efa5dxxxxxxxxxxxxxd53c5959e`


7.  返回到 PowerShell 窗口。要显示密钥的当前版本，请输入以下命令。

     ```powershell
    $Key.key.kid
     ```


8.  要查看刚刚创建的密钥，可以使用 Get-AzureKeyVaultKey cmdlet。确保将占位符文本更改为你的保管库名称。

     ```powershell
    Get-AzureKeyVaultKey -VaultName '<YourVaultName>'
     ```


### 任务 4：向密钥保管库添加机密

1.  接下来，你将在**“KeyVaultPS”**中添加机密。为此，使用以下代码，添加一个名为**“$ secretvalue”**的变量。

     ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
     ```


2.  接下来，使用此命令将机密添加到保管库。确保将占位符文本更改为你的保管库名称。

     ```powershell
    $secret = Set-AzureKeyVaultSecret -VaultName '<YourVaultName>' -Name      'SQLPassword' -SecretValue $secretvalue
     ```

3.  返回到 Azure 门户，在**“KeyVaultPS”**上单击**“机密”**

4.  单击机密**“SQL 密码”**

5.  单击当前版本

6.  检查你创建的机密

    **注**：你始终可以通过使用其 URI 来引用此密钥。要获取最新版本，请参考`https://keyvaultps.vault.azure.net/secrets/SQLPassword`，或者如果需要其为确切版本： `https://keyvaultps.vault.azure.net/secrets/SQLPassword/c5aada85d3acxxxxxxxxxxe8701efafcf3`


7.  单击**“显示机密值”**按钮-请注意会出现密码。

8.  若要查看机密，请使用 Get-AzureKeyVaultSecret cmdlet。确保将占位符文本更改为你的保管库名称。

     ```powershell
    Get-AzureKeyVaultSecret -VaultName '<YourVaultName>'
     ```

### 任务 5：启用客户端应用程序


将使你的客户端应用程序能够访问 Azure SQL 数据库服务。这可以通过设置所需的身份验证并获取对应用程序进行身份验证所需的应用程序 ID 和密钥来完成。这些步骤将在以下 PowerShell 代码中完成。


1.  打开 Azure 门户，然后导航到 Azure Active Directory。

2.  在左侧导航栏中，单击“管理”下的“应用注册”。

3.  **单击+新应用注册**

1.  提供你应用程序的名称**“sqlApp”**，选择**“Webapp API”**以及登录 URL类型 **`http://sqlapp`**

5.  单击**创建**。

6.  应用注册完成后，如果没有自动出现，请单击**“sqlApp”**。

7.  复制你的应用程序 ID，以备日后使用。

8.  单击**“设置”**

9.  单击**“密钥”**

10.  在“密钥”部分中，输入**“密钥 1”**进行描述。从“到期”下拉列表中选择**“1 年”**。


1.  将密钥值粘贴到“值”框中，然后单击“保存”。关闭边栏选项卡。如果重新打开边栏选项卡，则该值将显示为隐藏。

### 任务 6：添加密钥保管库策略，以允许应用程序访问密钥保管库。

1.  在**“Azure 门户”**中，打开你在实验开始时创建的**“资源组”**

1.  选择**“Azure 密钥保管库”**

1.  单击**“访问策略”**

1.  选择与你的 Azure 订阅相关的帐户

1.  在**“密钥权限”**下拉中选择**“全选”**，突出显示所有权限

1.  选择“确定”

1.  返回到用户列表后，单击**“保存”**

    **重要事项**！你必须单击“保存”，否则将不会提交权限 


8.  在 **Powershell ISE** 中运行以下 Powershell，设置 sqlApp 密钥权限，将占位符文本替换为**“你的帐户详细信息”**

    
     ```powershell
    $subscriptionName = '[Azure_Subscription_Name]'
    $applicationId = '[Azure_AD_Application_ID]'
    $resourceGroupName = '[Resource_Group_with_KeyVault]'
    $location = '[Azure_Region_of_KeyVault]'
    $vaultName = '[KeyVault_Name]' 
     ```
    
     ```powershell
    Login-AzureRmAccount
     ```
    
     ```powershell
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName `-ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
     ```


 
### 任务 7：使用密钥保管库通过 Azure SQL 数据库加密数据


**场景**

在此任务中，你将创建一个空白的 Azure SQL 数据库，使用 SQL Management Studio 连接到该数据库并创建一个表。然后，使用 Azure Key Vault 中自动生成的密钥对两个数据列进行加密。接着，使用 Visual Studio 创建一个控制台应用程序，以将数据加载到“加密列”中，然后使用通过密钥保管库访问密钥的连接字符串安全地访问该数据。


1.  在 Azure 门户中，单击**“创建资源>数据库> SQL 数据库”**

2.  在“SQL 数据库”边栏选项板卡上提供以下详细信息，然后单击“创建”。

      - 数据库称：**医疗**

      - 资源组：（新建） **SQLEncryptRG**

      - 选择源：**空数据库**

      - 服务器**“请求配置设置”**：**创建新服务器**

          - 服务器名称：**[唯一服务器名]**

          - 服务器管理员登录：**demouser**

          - 密码：**Pa55w.rd1234**

          - 位置：**[与 KeyVaultPS 相同的位置]**

          - 然后单击**“选择”**

      - 定价层：S0



1.  完成上述所有配置后，选择**“创建”**

3.  部署完 SQL 数据库后，在 Azure 门户中将其打开以查找并复制连接字符串。
警告
**注**：保存连接字符串以备将来使用时，请确保将 {your_username} 替换为**“demouser”** ，并将 {your_password} 替换为**“Pa55w.rd1234”**。


### 任务 8：在 SQL 数据库中创建表

1.  使用 Azure 门户找到医学数据库所在的服务器名称，然后复制该名称。



2.  在同一边栏选项卡上，单击“设置服务器防火墙”。



3.  下一步单击**“+添加客户端 Ip”，然后单击**，然后单击**“保存”**。



4.  在 LABVM 上，打开 SQL Server Management Studio。使用“连接到服务器”对话框的这些属性连接到服务器。

  - 服务器类型：**数据库引擎**

  - 服务器名称：**[位于数据库概述边栏选项卡上]**

  - 身份验证：**SQL Server 身份验证**

  - 登录：**“demouser”**

  - 密码：**Pa55w.rd1234**


### 任务 9：创建和加密表

1.  在 SQL Management Studio 中，展开**“数据库>右击医学>新建查询”**。

2.  将以下代码粘贴到查询窗口中，然后单击“执行”

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


3.  成功创建表后，**“展开医疗>表>右击 dbo.Patients”**，然后选择**“加密列”**。



4.  单击“下一步”。

5.  在列选择屏幕上检查**“SSN”**和**“生日”**。然后将 SSN 的加密类型设置为**“确定性的”**，将生日设置为**“随机的”**。单击**“下一步”**。



6.  在“选择密钥存储提供者”的“主密钥配置”页面上，单击**“Azure 密钥保管库”**。单击**“登录”**并进行身份验证。选择你的 Azure 密钥保管库。单击**下一步**：



7.  在“运行设置”屏幕上，单击**“下一步”**，然后单击**“完成”**以进行加密。



8.  展开**“医疗>安全>Always Encrypted 密钥”**，并注意，现在已经找到密钥。



### 任务 10：构建一个控制台应用程序以使用加密列

1.  在 LABVM 上打开 Visual Studio 2019 并登录。

2.  单击**“文件>新建>项目”**

3.  下一步选择**“Visual C＃>控制台应用 (.NET Framework)”**，并在位置**“C：”**提供名称**“OpsEncrypt”**，然后单击**“确定”**。


4.  **右击** **“OpsEncrypt”**项目>单击**“属性”**。



5.  将**“目标框架”**更改为**“NET Framework 4.6.2”**，当提示你更改**“目标框架”**时，请单击**“是”**。



6.  **打开** **“NuGet 程序包管理器控制台”**。



7.  通过转到**“工具”** > **“NuGet 程序包管理器”** > **“程序包管理器控制台”**，安装以下**“NuGet”**程序包。

     ```powershell
    Install-Package     Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
     ```

     ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
     ```

1.  打开浏览器并导航到 **`https://aka.gd/az300t03mod5code`**，然后复制代码。

8.  将**“Program.cs”**中的代码替换为你刚刚复制的代码。

9.  在 Main 方法中找到连接字符串设置，并替换为从先前步骤中复制的值。

10.  **单击** **Visual Studio** 中的**“启动按钮”**。


1.  **“控制台应用程序”**将**“建立”**然后启动。首先，它将数据添加到应用程序，然后要求你输入密码。

    - 服务器密码：**Pa55w.rd1234**

1.  **让** **“控制台应用程序保持运行”**，然后转到**“SQL Management Studio”**。右击医疗数据库，然后单击**“新建查询”**。



1.  运行以下查询以查看已加密加载到数据库中的数据。

     ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
     ```


1.  现在，返回要求你执行的控制台应用程序，**输入**一个**有效 SSN**。这将查询加密列中的数据。请注意，使用从密钥保管库中调用的密钥，现在数据未加密，并显示在控制台窗口中。

     ```sql
    999-99-0003
     ```

1.  要**“退出”**，请按下 Enter。


| 警告：在继续之前，你应该删除此实验室教学使用的所有资源。  为此，应在**“Azure 门户”**中，单击**“资源组”**。  选择你创建的任何资源组。  在资源组边栏选项卡上，单击**删除资源组**，输入资源组名称，然后单击**删除**。  对你创建的任何其他资源组重复该过程。**否则可能会导致其他实验室出现问题。** |
| --- |

**结果**：你现在已完成本课程。


