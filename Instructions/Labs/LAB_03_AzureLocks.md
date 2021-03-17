---
lab:
    title: '03 -资源管理器锁'
    module: '第 01 单元 - 管理标识和访问权限'
---

# 实验室 03：资源管理器锁
# 学生实验室手册

## 实验室场景 

你需要创建概念证明，展示如何使用资源锁来防止意外删除或更改。具体而言，需要执行以下操作：

- 创建只读锁

- 创建删除锁

> 对于本实验室中的所有资源，我们使用 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 
 
## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：资源管理器锁

### 练习 1：资源管理器锁

#### 预计用时：20 分钟

在该练习中，你将完成以下任务：

- 任务 1：创建具有存储帐户的资源组。
- 任务 2：在存储帐户上添加一个只读锁。 
- 任务 3：测试只读锁。 
- 任务 4：删除只读锁并创建一个删除锁。
- 任务 5：测试删除锁。

#### 任务 1：创建具有存储帐户的资源组。

在此任务中，你将为实验室创建资源组和存储账户。 

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**： 使用此实验室使用的 Azure 订阅中具有所有者或参与者角色的帐户登录 Azure 门户。

1. 单击 Azure 门户右上角的第一个图标，打开 Cloud Shell。如果出现提示，请选择 **“PowerShell”** 和 **“创建存储”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格的 PowerShell 会话中，运行以下命令以创建资源组（与讲师确认位置参数的值）：

    ```powershell
    New-AzResourceGroup -Name AZ500LAB03 -Location 'EastUS'
    ```

1. 在 Cloud Shell 窗格内的 PowerShell 会话中，运行以下命令以在新创建的资源组中创建存储帐户：
    
    ```powershell
    New-AzStorageAccount -ResourceGroupName AZ500LAB03 -Name (Get-Random -Maximum 999999999999999) -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
    ```

   >**备注**：  等待存储帐户创建。这可能需要几分钟。 

1. 关闭 Cloud Shell 窗格。

#### 任务 2：在存储帐户上添加一个只读锁。 

在此任务中，你将向存储帐户添加只读锁。这将保护资源，使其免遭意外删除或修改。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“资源组”**，然后按 **Enter** 键。

1. 在 **“资源组”** 边栏选项卡，选择 **“AZ500LAB03”** 资源组条目。

1. 在 **“AZ500LAB03”** 资源组边栏选项卡上的资源列表中，选择新的存储帐户。 

1. 在 **“设置”** 部分，单击“锁”图标。

1. 单击 **“+ 添加”** 并指定以下设置：

   |设置|值|
   |---|---|
   |锁名称|**只读锁**|
   |锁类型|**只读**|

1. 单击 **“确定”**。 

   >**备注**：  现在，可以防止意外删除和修改存储帐户。

#### 任务 3：测试只读锁 

1. 在存储帐户边栏选项卡的 **“设置”** 部分，单击 **“配置”**。

1. 设置 **“需要安全转移”** 选项为 **“禁用”**，然后单击 **“保存”**。

1. 应该能够发现一条通知，声明 **“无法更新存储帐户”**。

1. 在 Azure 门户顶部工具栏中，单击 **“通知”** 图标，并查看类似于以下文本的通知： 

	> **“无法更新存储帐户‘xxxxxxxx’。 错误: 范围“xxxxxxxx”无法执行写操作，因为以下范围已锁定: ‘/subscriptions/xxxxx-xxx-xxxx-xxxx-xxxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx’。请删除锁，然后重试”**

1. 退回存储帐户的 **“配置”** 边栏选项卡，然后单击 **“丢弃”**。 

1. 在“存储帐户”边栏选项卡上，选择 **“概述”**，并在 **“概述”** 边栏选项卡，单击 **“删除”**。

1. 在 **“删除存储帐户”** 边栏选项卡上，键入打算删除的存储帐户名称以确认，然后单击 **“删除”**。

1. 查看新生成的通知，该通知类似于以下文本： 

	> **“无法删除存储帐户‘xxxxxxx’。 错误: 范围“xxxxxxx”无法执行删除操作，因为以下范围已锁定: '/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'。请删除锁，然后重试。"**

   >**备注**： 现在，已确认只读锁将阻止意外删除和修改资源。

#### 任务 4：删除只读锁并创建一个删除锁。

在此任务中，从存储帐户中删除只读锁并创建一个删除锁。 

1. 在 Azure 门户中，导航回显示新配置的存储帐户属性的边栏选项卡。

1. 在 **“设置”** 部分，选择 **“锁”**。  

1. 在 **“锁”** 边栏选项卡，单击 **“只读锁”** 条目最右边的 **“删除”** 图标。

1. 单击 **“+ 添加”** 并指定以下设置：

   |设置|值|
   |---|---|
   |锁名称|**删除锁**|
   |锁类型| **删除**|

1. 单击 **“确定”**。 

#### 任务 5：测试删除锁。

在此任务中将测试删除锁。应该能够修改存储帐户，但不能删除。 

1. 在存储帐户边栏选项卡的 **“设置”** 部分，单击 **“配置”**。

1. 设置 **“需要安全转移”** 选项为 **“禁用”**，然后单击 **“保存”**。

   >**备注**：  这次更改应该会成功。

1. 在“存储帐户”边栏选项卡上，选择 **“概述”**，并在 **“概述”** 边栏选项卡，单击 **“删除”**。

1. 在 **“删除存储帐户”** 边栏选项卡上，键入打算删除的存储帐户名称以确认，然后单击 **“删除”**。

1. 查看类似于以下文本的通知： 

	> **“无法删除“xxxxxx”，因为此资源或其父级具有删除锁。必须先删除锁，然后才能删除该资源”**

   >**备注**： 你现在已经验证了 **“删除”** 锁将允许配置更改，但会停止意外删除。

   >**备注**：  通过使用资源锁，你可以实现额外的防护措施，以防止意外或恶意更改和/或删除最重要的资源。具有 **“所有者”** 角色的任何用户都可以删除资源锁，但需要自觉努力。锁是基于角色的访问控制的补充。 

> 结果：在本练习中，你学习了使用资源管理器锁来保护资源免受修改和意外删除。

**清理资源**

> 请记得删除任何新创建而不会再使用的 Azure 资源。删除未使用的资源，确保不产生意外成本。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。如果出现提示，单击 **“重新连接”**。

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以删除删除锁：

    ```powershell
    $storageaccountname = (Get-AzStorageAccount -ResourceGroupName AZ500LAB03).StorageAccountName

    $lockName = (Get-AzResourceLock -ResourceGroupName AZ500LAB03 -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).Name

    Remove-AzResourceLock -LockName $lockName -ResourceName $storageAccountName  -ResourceGroupName AZ500LAB03 -ResourceType Microsoft.Storage/storageAccounts -Force
    ```

1.  在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令以删除资源组：

    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB03" -Force -AsJob
    ```

1.  关闭 **Cloud Shell** 窗格。 
