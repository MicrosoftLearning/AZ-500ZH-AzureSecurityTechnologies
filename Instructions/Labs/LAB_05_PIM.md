---
lab:
    title: '05 - Azure AD Privileged Identity Management'
    module: '第 01 单元 - 管理标识和访问权限'
---

# 实验室 05：Azure AD Privileged Identity Management
# 学生实验室手册

## 实验室场景

你需要创建一个概念证明，使用 Azure Privileged Identity Management (PIM) 来实现实时管理，并控制可以执行特权操作的用户数量。具体要求是：

- 创建 aaduser2 Azure AD 用户到安全管理员角色的永久分配。 
- 将 aaduser2 Azure AD 用户配置为符合担任计费管理员和全局读取者角色的条件。
- 将全局读取者角色的激活配置为需要批准 aaduser3 Azure AD 用户
- 配置对全局读取者角色的访问评审并查看审核功能。

> 对于本实验室中的所有资源，我们使用 **“美国东部”** 区域。请与讲师确认这是课堂上所使用的区域。 

> 在继续之前，请确保已完成“实验室 04：MFA、条件访问和 AAD 标识保护。你将需要 Azure AD 租户、AdatumLab500-04 以及 aaduser1、aaduser2 和 aaduser3 用户帐户。

## 实验室目标

在本实验室中，你将完成以下练习：

- 练习 1：配置 PIM 用户和角色。
- 练习 2：无论与否得到批准都要激活 PIM 角色。
- 练习 3：创建访问评审并审查 PIM 审核功能。

### 练习 1 - 配置 PIM 用户和角色

#### 预计用时：15 分钟

在该练习中，你将完成以下任务：

- 任务 1：使用户符合角色的条件。
- 任务 2：配置角色以要求审批才能激活和添加符合条件的成员。
- 任务 3：将用户永久分配至角色。 

#### 任务 1：使用户符合角色的条件

在此任务中，你将使一个用户符合 Azure AD 目录角色的条件。

1. 登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**： 确保已登录 **AdatumLab500-04** Azure AD 租户。你可以使用 **“目录 + 订阅”** 筛选器在 Azure AD 租户之间切换。务必以具有全局管理员角色的用户身份登录。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure AD Privileged Identity Management”**，然后按 **Enter** 键。

1. 在 **“Azure AD Privileged Identity Management”** 边栏选项卡的 **“管理”** 部分，单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“角色”**。

1. 在 **“AdatumLab500-04 \| 角色”** 边栏选项卡上，单击 **“+ 添加分配”**。

1. 在 **“添加分配”** 边栏选项卡的 **“选择角色”** 下拉列表中，选择 **“计费管理员”**。

1. 单击 **“未选中成员”** 链接，在 **“选择成员”** 边栏选项卡上，单击 **“aaduser2”**，然后单击 **“选择”**。

1. 回到 **“添加分配”** 边栏选项卡，单击 **“下一步”**。 

1. 确保将 **“分配类型”** 设置为 **“符合条件”**，然后单击 **“分配”**。
 
1. 回到 **“AdatumLab500-04 \| 角色”** 边栏选项卡，在 **“管理”** 部分中，单击 **“分配”**。

1. 回到 **“AdatumLab500-04 \| 分配”** 边栏选项卡上，注意 **“符合条件的分配”**、 **“活动分配”** 和 **“过期分配”** 选项卡。

1. 验证 **“符合条件的分配”** 选项卡上的 **aaduser2** 是否显示为 **“计费管理员”**。 

    >**备注**： 登录期间，aaduser2 符合使用计费管理员角色的条件。 

#### 任务 2：配置角色以要求审批才能激活和添加符合条件的成员

1. 在 Azure 门户中，导航回 **“Azure AD Privileged Identity Management”** 边栏选项卡并单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“角色”**。

1. 在 **“AdatumLab500-04 \| 角色”** 边栏选项卡上，单击 **“全局读取者”** 角色条目。 

1. 在 **“全局读取者 \| 分配”** 边栏选项卡上，单击边栏选项卡工具栏中的 **“设置”** 图标，并查看该角色的配置设置，包括 Azure 多重身份验证要求。

1. 单击 **“编辑”**。

1. 在 **“激活”** 选项卡上，启用 **“需要审批才能激活”** 复选框。

1. 单击 **“选择审批者”**，在 **“选择成员”** 边栏选项卡上，单击 **aaduser3**，然后单击 **“选择”**。

1. 单击 **“下一步：分配”**。

1. 清除 **“允许永久符合条件分配”** 复选框，将所有其他设置保留为默认值。

1. 单击 **“下一步: 通知”**。

1. 在 **“编辑角色设置 - 全局读取者”** 边栏选项卡上，查看设置并单击 **“更新”**。

    >**备注**： 现在，任何尝试使用全局读取者角色的人都需要 aaduser3 的审批。 

1. 在 **“全局读取者 \| 分配”** 边栏选项卡上，单击 **“+ 添加分配”**。

1. 在 **“添加分配”** 边栏选项卡上，单击 **“未选中成员”**，在 **“选择成员”** 边栏选项卡上，单击 **“aaduser2”**，然后单击 **“选择”**。

1. 单击 **“下一步”**。 

1. 确保 **“分配类型”** 为 **“符合条件”**，并查看符合条件的持续时间设置。

1. 单击 **“分配”**。

    >**备注**： 用户 aaduser2 符合全局读取者角色的条件。 
 
#### 任务 3：将用户永久分配至角色。

1. 在 Azure 门户中，导航回 **“Azure AD Privileged Identity Management”** 边栏选项卡并单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“角色”**。

1. 在 **“AdatumLab500-04 \| 角色”** 边栏选项卡上，单击 **“+ 添加分配”**。

1. 在 **“添加分配”** 边栏选项卡的 **“选择角色”** 下拉列表中，选择 **“安全管理员”**。

1. 在 **“添加分配”** 边栏选项卡上，单击 **“未选中成员”**，在 **“选择成员”** 边栏选项卡上，单击 **“aaduser2”**，然后单击 **“选择”**。

1. 单击 **“下一步”**。 

1. 查看 **“分配类型”** 设置，并单击 **“分配”**。

    >**备注**： 用户 aaduser2 现在永久符合安全管理员角色的条件。
	
### 练习 2 - 在经过审批和未经审批的情况下激活 PIM 角色

#### 预计用时：15 分钟

在该练习中，你将完成以下任务：

- 任务 1：激活不需要审批的角色。 
- 任务 2：激活需要审批的角色。 

#### 任务 1：激活不需要审批的角色。

在此任务中，将激活一个不需要审批的角色。

1. 打开 InPrivate 浏览器窗口。

1. 在 InPrivate 浏览器窗口中，导航到 Azure 门户并使用 **aaduser2** 用户帐户登录。

    >**备注**： 若要登录，需提供 **aaduser2** 用户帐户的完全限定名称，包括你在本实验室前面记录的 Azure AD 租户 DNS 域名。该用户名的格式为 aaduser2@`<your_tenant_name>`.onmicrosoft.com，其中 ` <your_tenant_name>` 是占位符，表示唯一的 Azure AD 租户名称。 

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure AD Privileged Identity Management”**，然后按 **Enter** 键。

1. 在 **“Azure AD Privileged Identity Management”** 边栏选项卡的 **“任务”** 部分，单击 **“我的角色”**。

1. 应该会看到 **aaduser2** 的三个 **“合格角色”**： **“全局阅读者”**、 **“安全管理员”** 和 **“计费管理员”**。 

1. 在显示 **“计费管理员”** 角色条目的行中，单击 **“激活”**。

1. 根据需要单击警告 **“需要附加验证。 单击以继续”**，并按照说明验证你的身份。

1. 在 **“激活 - 计费管理员”** 边栏选项卡的 **“原因”** 文本框中，键入提供激活理由的文本，然后单击 **“激活”**。

    >**备注**： 在 PIM 中激活角色时，最多可能需要 10 分钟才能使激活生效。 
	
    >**备注**： 该角色不需要审批，因此你可以注销并重新登录以开始使用激活的角色。 

1. 注销并使用 **aaduser2** 用户帐户重新登录到 Azure 门户。

1. 导航回 **“Azure AD Privileged Identity Management”** 边栏选项卡，然后在 **“任务”** 部分单击 **“我的角色”**。

1. 在 **“我的角色 \| Azure AD 角色”** 边栏选项卡上，切换到 **“活动分配”** 选项卡。注意 **“计费管理员”** 角色是 **“已激活”** 状态。

    >**备注**： 角色激活后，当达到 **“结束时间”** （符合条件的持续时间）下的时间限制时，它将自动停用。

    >**备注**： 如果提早完成管理员任务，可以手动停用角色。

1.  在 **“活动分配”** 列表中，在表示“计费管理员”角色的行中，单击 **“停用”** 链接。

1.  在 **“停用 - 计费管理员”** 边栏选项卡上，再次单击 **“停用”** 以进行确认。


#### 任务 2：激活需要审批的角色。 

在此任务中，将激活需要审批的角色。

1. 在 InPrivate 浏览器窗口中，以用户 **aaduser2** 登录 Azure 门户，然后导航回 **“Privileged Identity Management \| 快速入门”** 边栏选项卡。 

1. 在 **“Privileged Identity Management \| 快速入门”** cũng đơc边栏选项卡的 **“任务”** 部分中，单击 **“我的角色”**。

1. 在 **“我的角色 \| Azure AD 角色”** 边栏选项卡上，在 **“符合条件的分配”** 列表中显示 **“全局读取者”** 角色的行中，单击 **“激活”**。 

1. 在 **“激活 - 全局读取者”** 边栏选项卡的 **“原因”** 文本框中，键入提供激活理由的文本，然后单击 **“激活”**。

1. 单击 Azure 门户工具栏中的**通知**图标，然后查看告知你请求正在等待审批的通知。

    >**备注**： 作为特权角色管理员，你可以随时查看和取消请求。 

1. 在 **“我的角色 \| Azure AD 角色”** 边栏选项卡上，找到 **“安全管理员”** 角色，然后单击 **“激活”**。 

1. 单击警告 **“需要附加验证。 单击以继续”**。 

1. 按照说明验证你的身份。

    >**备注**： 只需在每个会话中执行身份验证一次。 

1. 回到 Azure 门户界面后，在 **“激活 - 安全管理员”** 边栏选项卡的 **“原因”** 文本框中，键入提供激活理由的文本，然后单击 **“激活”**。

    >**备注**： 自动审批过程应该会完成。

1. 回到 **“我的角色 \| Azure AD 角色”** 边栏选项卡，单击 **“活动分配”** 选项卡，请注意， **“活动分配”** 列表包括 **“安全管理员”** 角色，但不包括 **“全局读取者”** 角色。

    >**备注**： 现在将审批“全局读取者”角色。

1. 以 **aaduser2** 的身份从 Azure 门户注销。

1. 以 **aaduser3** 的身份登录 Azure 门户。

    >**备注**： 如果在使用任何一个用户帐户进行身份验证时遇到问题，可以使用你的用户帐户重置其密码或重新配置其登录选项以登录 Azure AD 租户。

1. 在 Azure 门户中，导航到 **“Azure AD Privileged Identity Management”**。

1. 在 **“Privileged Identity Management \| 快速入门”** 边栏选项卡的 **“任务”** 部分，单击 **“审批请求”**。

1. 在 **“审批请求 \| Azure AD 角色”** 边栏选项卡的 **“请求激活角色”** 部分中，选择表示由 **aaduser2** 向 **“全局读取者”** 角色发出的角色激活请求的条目的复选框。

1. 单击 **“批准”**。在 **“审批请求”** 边栏选项卡上的 **“理由”** 文本框中键入激活原因，记下开始和结束时间，然后单击 **“确认”**。 

    >**备注**： 你还可以选择拒绝请求。

1. 以 **aaduser3** 的身份从 Azure 门户注销。

1. 以 **aaduser2** 的身份登录 Azure 门户

1. 在 Azure 门户中，导航到 **“Azure AD Privileged Identity Management”**。

1. 在 **“Privileged Identity Management \| 快速入门”** 边栏选项卡的 **“任务”** 部分中，单击 **“我的角色”**。

1. 在 **“我的角色 \| Azure AD 角色”** 边栏选项卡上，单击 **“活动分配”** 选项卡，并验证“全局阅读者”角色现在是否处于活动状态。

    >**备注**： 可能必须刷新页面才能查看活动分配的更新列表。

1. 注销并关闭 InPrivate 浏览器窗口。

> 结果：你已经练习过在经过审批和未经审批的情况下激活 PIM 角色。 

### 练习 3 - 创建访问评审并查看 PIM 审核功能

#### 预计用时：10 分钟

在该练习中，你将完成以下任务：

- 任务 1：在 PIM 中为 Azure AD 目录角色配置安全警报
- 任务 2：查看 PIM 警报、摘要信息和详细的审核信息

#### 任务 1：在 PIM 中为 Azure AD 目录角色配置安全警报

在此任务中，将减少与“过期”角色分配相关的风险。为此，将通过创建 PIM 访问评审来确保分配的角色仍然有效。具体来说，将审查全局读取者角色。 

1. 使用你的帐户登录 Azure 门户 **`https://portal.azure.com/`**。

    >**备注**： 确保已登录 **AdatumLab500-4b** Azure AD 租户。你可以使用 **“目录 + 订阅”** 筛选器在 Azure AD 租户之间切换。务必以具有全局管理员角色的用户身份登录。

1. 在 Azure 门户页面顶部的 **“搜索资源、服务和文档”** 文本框中，键入 **“Azure AD Privileged Identity Management”**，然后按 **Enter** 键。

1. 导航到 **“Azure AD Privileged Identity Management”** 边栏选项卡。 

1. 在 **“Privileged Identity Management \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“访问评审”**。

1. 在 **“AdatumLab500-04 \| 访问评审”** 边栏选项卡上，单击 **“新建”**：

1. 在 **“创建访问评审”** 边栏选项卡上，指定以下设置（将其他设置保留为默认值）： 

   |设置|值|
   |---|---|
   |评审名称|**全局阅读者评审**|
   |开始日期|当天日期| 
   |频率|**一次性**|
   |结束日期|本月末|
   |审查角色成员身份|**全局读取者**|
   |评审者|**所选用户**|
   |选择审阅者|你的帐户|

1. 在 **“创建访问评审”** 边栏选项卡上，单击 **“开始”**：
 
    >**备注**： 该评审大约需要一分钟才能部署并显示在 **“AdatumLab500-04 \| 访问评审”** 边栏选项卡。可能需要刷新网页。评审状态为 **“活动”**。 

1. 在 **“AdatumLab500-04 \| 访问评审”** 边栏选项卡的 **“全局管理员评审”** 标题下，单击 **“全局读取者”** 条目。 

1. 在 **“全局读取者评审”** 边栏选项卡上，检查 **“概述”** 页面，并注意 **“进度”** 图显示了 **“未评审”** 类别中的单个用户。 

1. 在 **“全局读取者评审”** 边栏选项卡的 **“管理”** 部分，单击 **“结果”**。请注意，aaduser2 被列为有权访问此角色。

1. 单击 **aaduser2** 查看详细的审核日志，其中包含代表涉及该用户的 PIM 活动的条目。

1. 导航回 **“AdatumLab500-04 \| 访问评审”** 边栏选项卡。

1. 在 **“AdatumLab500-04 \| 访问评审”** 边栏选项卡的 **“任务”** 部分中，单击 **“评审访问权限”**，然后单击 **“全局读取者评审”** 条目。 

1. 在 **“全局读取者评审”** 边栏选项卡上，单击 **aaduser2** 条目。 

1. 在 **“原因”** 文本框中输入审批理由，然后单击 **“批准”** 以维持当前角色的成员资格，或单击 **“拒绝”** 撤销该资格。 

1. 导航回 **“Azure AD Privileged Identity Management”** 边栏选项卡，然后在 **“管理”** 部分中单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡的 **“管理”** 部分，单击 **“访问评审”**。

1. 选择代表 **“全局读取者”** 评审的条目。请注意 **“进度”** 图已更新以显示你的评审。 

#### 任务 2：查看 PIM 警报、摘要信息和详细的审核信息。 

在此任务中，你将查看 PIM 警报、摘要信息和详细的审核信息。 

1. 导航回 **“Azure AD Privileged Identity Management”** 边栏选项卡，然后在 **“管理”** 部分中单击 **“Azure AD 角色”**。

1. 在 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡上，在 **“管理”** 部分，单击 **“警报”**，然后单击 **“设置”**。

1. 在 **“警报设置”** 边栏选项卡上，查看预配置的警报和风险级别。单击任何一个以获取更多详细信息。 

1. 返回到 **“AdatumLab500-04 \| 快速入门”** 边栏选项卡，并单击 **“概述”**。 

1. 在 **“AdatumLab500-04 \| 概述”** 边栏选项卡上，查看有关角色激活、PIM 活动、警报和角色分配的摘要信息。

1. 在 **“AdatumLab500-04 \| 概述”** 边栏选项卡上，在 **“活动”** 部分，单击 **“资源审核”**。 

    >**备注**： 审核历史记录可用于过去 30 天内所有特权角色分配和激活。

1. 注意，你可以检索详细信息，包括**时间**、**请求者**、**操作**、**资源名称**、**范围**和**主题**。 

> 结果：你已配置了访问评审并检查了审核信息。 

**清理资源**

> 请记得删除任何新创建而不会再使用的 Azure 资源。删除未使用的资源，确保不产生意外成本。 

1. 在 Azure 门户中，将 **“目录 + 订阅”** 筛选器设置为与你在其中部署 **az500-04-vm1** Azure VM 的 Azure 订阅相关联的 Azure AD 租户。

1. 在 Azure 门户中，通过单击 Azure 门户右上角的第一个图标打开 Cloud Shell。如果出现提示，请单击 **“PowerShell”** 和 **“创建存储”**。

1. 确保在“Cloud Shell”窗格左上角的下拉菜单中选中 **“PowerShell”**。

1. 在“Cloud Shell”窗格中的 PowerShell 会话中，运行以下命令删除在此实验室中创建的资源组：
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

1.  关闭 **Cloud Shell** 窗格。 

1. 返回 Azure 门户，使用 **“目录 + 订阅”** 筛选器切换到 **AdatumLab500-04** Azure Active Directory 租户。

1. 导航到 **“Azure Active Directory Premium P2 - 许可的用户”** 边栏选项卡，选择分配了许可证的用户帐户，单击 **“删除许可证”**，然后在提示确认时，选择 **“是”**。

1. 在 Azure 门户中，导航到 **“用户”-“所有用户”** 边栏选项卡，单击代表 **“aaduser1”** 用户帐户的条目，在 **“aaduser1”-“个人资料”** 边栏选项卡上，单击 **“删除”**，然后在提示确认时，选择 **“是”**。

1. 重复相同的步骤以删除你创建的其余用户帐户。

1. 导航到 Azure AD 租户的 **“AdatumLab500-04”-“概述”** 边栏选项卡，单击 **“删除租户”**，在 **“删除目录 'AdatumLab500-04'”** 边栏选项卡上，单击 **“获取删除 Azure 资源的权限”** 链接，在 Azure Active Directory 的 **“属性”** 边栏选项卡上，将 **“Azure 资源的访问管理”** 设置为 **“是”**，然后单击 **“保存”**。

1. 从 Azure 门户注销，然后重新登录。 

1. 导航回 **“删除目录 ‘AdatumLab500-04’”** 边栏选项卡，然后单击 **“删除”**。

> 有关此任务的更多信息，请参阅 [https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/zh-cn/azure/active-directory/users-groups-roles/directory-delete-howto)
