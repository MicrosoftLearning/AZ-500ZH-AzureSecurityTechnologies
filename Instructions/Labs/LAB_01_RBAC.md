---
lab:
    title: '01 - 基于角色的访问控制'
    module: '第 01 单元 - 管理标识和访问权限'
---

# 实验室 01：基于角色的访问控制
# 学生实验室手册

## 实验室场景

你需要创建一个显示如何创建 Azure 用户和组的概念证明。此外，如何使用基于角色的访问控制将角色分配给组。具体而言，需要执行以下操作：

- 创建一个高级管理员组，其中包含 Joseph Price 的用户帐户作为该组的成员。
- 创建一个初级管理员组，其中包含 Isabel Garcia 的用户帐户作为该组的成员。
- 创建一个服务台组，其中包含 Dylan Williams 用户帐户作为该组的成员。
- 将虚拟机参与者角色分配给“服务台”组。 

> 对于本实验室中的所有资源，我们使用 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 

## 实验室目标

在本实验室中，你将完成以下练习

- 练习 1：使用用户帐户以组成员 Joseph Price 身份创建高级管理员组（Azure 门户）。 
- 练习2：使用用户帐户 Isabel Garcia 作为组成员 (PowerShell) 创建初级管理员组。
- 练习 3：创建服务台组，以用户 Dylan Williams 作为其组成员 (Azure CLI) 。 
- 练习 4：将虚拟机参与者角色分配给“服务台”组。

### 练习 1：创建高级管理员组，以用户帐户 Joseph Price 作为其组成员。 

#### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：使用 Azure 门户为 Joseph Price 创建一个存储帐户。
- 任务 2：使用 Azure 门户创建高级管理员组，并将 Joseph Price 用户帐户添加到该组。

#### 任务 1：使用 Azure 门户为 Joseph Price 创建一个存储帐户 

在此任务中，你将为 Joseph Price 创建一个用户帐户。 

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**： 使用帐户登录到 Azure 门户，该帐户在你用于此实验室的 Azure 订阅中具有所有者或参与者角色，并且在与该订阅关联的 Azure AD 租户中具有全局管理员角色。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure Active Directory”** 并按 **Enter** 键。

1. 在 Azure Active Directory 租户的 **“概述”** 边栏选项卡中，在 **“管理”** 部分选择 **“用户”**，然后选择 **“+ 新建用户”**。

1. 在 **“新建用户”** 边栏选项卡中，确保选中 **“创建用户”** 选项，并指定以下设置：

   |设置|值|
   |---|---|
   |用户名|**Joseph**|
   |名称|**Joseph Price**|

1. 单击 **“用户名”** 旁边的复制图标以复制完整用户。

1. 确保选中 **“自动生成密码”**，然后选择 **“显示密码”** 复选框以识别自动生成的密码。需要将此密码以及用户名提供给 Joseph。 

1. 单击 **“创建”**。

1. 刷新 **“用户 \| 全部用户”** 边栏选项卡，以验证新用户是否在 Azure AD 租户中创建。

#### 任务 2：使用 Azure 门户创建高级管理员组，并将 Joseph Price 用户帐户添加到该组。

在此任务中，你将创建*高级管理员*组，并将 Joseph Price 用户帐户添加到该组中，然后将其配置为组所有者。

1. 在 Azure 门户中，导航回显示 Azure Active Directory 租户的边栏选项卡。 

1. 在 **“管理”** 部分，单击 **“组”**，然后选择 **“+ 新建组”**。
 
1. 在 **“新建组”** 边栏选项卡中，指定以下设置（将其他设置保留为默认值）：

   |设置|值|
   |---|---|
   |组类型|**安全**|
   |组名|**高级管理员**|
   |成员身份类型|**已分配**|
    
1. 单击 **“未选择所有者”** 链接，在 **“添加所有者”** 边栏选项卡上，选择 **“Joseph Price”**，然后单击 **“选择”**。

1. 单击 **“未选择成员”** 链接，在 **“添加成员”** 边栏选项卡上，选择 **“Joseph Price”**，然后单击 **“选择”**。

1. 返回 **“新建组”** 边栏选项卡，单击 **“创建”**。

> 结果：你使用 Azure 门户创建了一个用户和一个组，并将该用户分配给了该组。 

### 练习2：创建一个初级管理员组，其中包含 Isabel Garcia 的用户帐户作为该组的成员。

#### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：使用 PowerShell 为 Isabel Garcia 创建一个用户帐户。
- 任务 2：使用 PowerShell 创建初级管理员组，并将 Isabel Garcia 用户帐户添加到该组。 

#### 任务 1：使用 PowerShell 为 Isabel Garcia 创建一个用户帐户。

在此任务中，你将使用 PowerShell 为 Isabel Garcia 创建一个用户帐户。

1. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。如果出现提示，请选择 **“PowerShell”** 和 **“创建储存”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

   >**备注**： 要将复制文本粘贴到 Cloud Shell 中，请在窗格窗口中右键单击并选择 **“窗格”**。或者，可以使用 **Shift + Insert** 组合键。

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以创建密码配置文件对象：

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以在配置文件对象中设置密码的值：
    ```powershell
    $passwordProfile.Password = 'Pa55w.rd1234'
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行下列命令以连接到 Azure Active Directory：

    ```powershell
    Connect-AzureAD
    ```
      
1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以识别 Azure AD 租户的名称： 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令为 Isabel Garcia 创建一个用户帐户： 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以列出 Azure AD 用户（Joseph 和 Isabel 帐户应显示在列表中）： 

    ```powershell
    Get-AzureADUser 
    ```

#### 任务 2：使用 PowerShell 创建初级管理员组，并将 Isabel Garcia 用户帐户添加到该组。

在此任务中，你将创建初级管理员组，并使用 PowerShell 将 Isabel Garcia 用户帐户添加到该组。

1. 在“Cloud Shell”窗格内的同一 PowerShell 会话中，运行以下命令以创建名为初级管理员的新安全组：
	
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以列出 Azure AD 租户中的组（该列表应包括高级管理员和初级管理员组）：

    ```powershell
    Get-AzureADGroup
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以获得对 Isabel Garcia 用户帐户的引用：

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令将 Isabel 用户帐户添加到初级管理员组：
	
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

1. 在“Cloud Shell”窗格内的 PowerShell 会话中，运行以下命令以验证初级管理员组是否含有 Isabel 用户帐户：

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> 结果：你使用 PowerShell 创建了一个用户帐户和一个组帐户，并将该用户帐户添加到该组帐户中。 


### 练习 3：创建一个服务台组，其中包含 Dylan Williams 用户帐户作为该组的成员。

#### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：使用 Azure CLI 为 Dylan Williams 创建用户帐户。
- 任务 2：使用 Azure CLI 创建服务台组并在该组织推荐 Dylan 用户帐户。 

#### 任务 1：使用 Azure CLI 为 Dylan Williams 创建用户帐户。

在此任务中要为 Dylan Williams 创建用户帐户。

1. 在“Cloud Shell”窗格左上角的下拉菜单中，选择 **“Bash”**，然后在出现提示时，单击 **“确认”**。 

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以识别 Azure AD 租户的名称：

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以创建 Dylan Williams 用户。使用 *yourdomain*。
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以列出 Azure AD 用户帐户（该列表应包含 Joseph、Isabel 和 Dylan 用户帐户）
	
    ```cli
    az ad user list --output table
    ```

#### 任务 2：使用 Azure CLI 创建服务台组并在该组织推荐 Dylan 用户帐户。 

在此任务中，你将创建服务台组并将 Dylan 分配给该组。 

1. 在“Cloud Shell”窗格内的同一 Bash 会话中，运行以下命令创建名为服务台的新安全组。

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以列出 Azure AD 组（该列表应包含服务台、高级管理员和初级管理员组）：

    ```cli
    az ad group list -o table
    ```

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以获得对 Dylan Williams 用户帐户的引用： 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以获得 Dylan Williams 用户帐户的 ObjectId 属性： 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].objectId' | tr -d '"')
    ```

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以将 Dylan 用户帐户添加到服务台组： 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

1. 在“Cloud Shell”窗格内的 Bash 会话中，运行以下命令以列出服务台组的成员，并验证其是否包含 Dylan 用户帐户：

    ```cli
    az ad group member list --group "Service Desk"
    ```

1. 关闭 Cloud Shell 窗格。

> 结果：使用 Azure CLI，你创建了一个用户帐户和一个组帐户，并将该用户帐户添加到了该组中。 


### 练习 4：将虚拟机参与者角色分配给“服务台”组。

#### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：创建资源组。 
- 任务 2：将服务台虚拟机参与者权限分配给该资源组。  

#### 任务 1：创建资源组

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“资源组”**，然后按 **Enter** 键。

1. 在 **“资源组”** 边栏选项卡上单击 **“+ 新建”**，并指定以下设置：

   |设置|值|
   |---|---|
   |订阅名称|Azure 订阅的名称|
   |资源组名称|**AZ500Lab01**|
   |位置|**美国东部**|

1. 单击 **“查看 + 创建”**，然后单击 **“创建”**。

   >**备注**： 等待资源组以便部署。使用 **“通知”** 图标（右上角）跟踪部署状态的进度。

1. 返回 **“资源组”** 边栏选项卡，刷新页面并验证新资源组是否出现在资源组列表中。


#### 任务 2：分配服务台虚拟机参与者权限。 

1. 在 **“资源组”** 边栏选项卡上，单击 **“AZ500LAB01”** 资源组条目。

1. 在 **“AZ500LAB01”** 边栏选项卡上，单击 **“访问控制(IAM)”**。

1. 在 **“AZ500Lab01 \| 访问控制(IAM)”** 边栏选项卡中单击 **“+ 添加”**，然后在下拉菜单中单击 **“添加角色分配”**。

1. 在 **“添加角色分配”** 边栏选项卡上，指定以下设置：

   |设置|值|
   |---|---|
   |角色|**虚拟机参与者**|
   |分配对以下内容的访问权限|**用户、组或服务主体**|
   |选择|**服务台**|

1. 选择 **“保存”**，创建角色分配。

1. 在 **“访问控制(IAM)”** 边栏选项卡上，选择 **“角色分配”**。

1. 在 **“AZ500Lab01 \| 访问控制(IAM)”** 边栏选项卡，然后在 **“检查访问”** 选项卡的 **“按名称或电子邮件地址搜索”** 文本框中键入 **“Dylan Williams”**。

1. 在搜索结果列表中，选择 Dylan Williams 用户帐户，然后在 **“Dylan Williams 分配 - AZ500Lab01”** 边栏选项卡上，查看新创建的分配工作。

1. 关闭 **“Dylan Williams 分配 - AZ500Lab01”** 边栏选项卡。

1. 重复最后两个相同步骤，检查 **“Joseph Price”** 的访问权限。 

> 结果：已分配并检查了 RBAC 权限。 

**清理资源**

> 请记得删除任何新创建而不会再使用的 Azure 资源。删除未使用的资源，确保不产生意外成本。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。 

1. 在“Cloud Shell”窗格左上角的下拉菜单中，选择 **“PowerShell”**，然后在出现提示时，单击 **“确认”**。 

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

1.  关闭 **Cloud Shell** 窗格。 
