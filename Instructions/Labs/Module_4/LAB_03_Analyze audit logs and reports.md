

# 模块 4：分析审核日志和报告

## 练习 1：SQL 数据库审核入门

### 任务 0：实验设置

1.  打开 **PowerShell**，然后运行以下命令以部署实验数据库。

     ```powershell
    开始 "https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoftLearning%2FAZ-500-Azure-Security%2Fmaster%2FAllfiles%2FLabs%2FMod4_Lab03%2Fazuredeploy.json" 
     ```

1.  在**“资源组”**下单击新建，并使用默认名 "**Mod4Lab3**"

1.  你可以使用默认填充的 SQL Server 名称

1.  请单击**“购买”** 

### 任务 1 - 为你的数据库设置审核

1.  **导航**到**“资源组”**

1.  **选择**你在上面创建的资源组（如果选择与说明相同的名称，则为 "**Mod3Lab3**"）

1.  **单击**你的**“SQL Server”**名称

1.  在左窗格上单击审核

1.  将审核**从关闭****更改**到**“打开”**。
警告
**注**：现在，你可以选择希望写入审核日志的位置。


1.  **选择**全部**3 个选项**，**“存储”**、**“Log analytics”**和**“事件中心”**。

1.  在**“存储”**下，请单击**“配置”**。

1.  单击**“订阅”**并选择你的订阅

1.  单击**“存储帐户”**

1.  在“创建存储帐户”下输入唯一名称（**例如Mod4Lab3YOURNAME**）

1.  单击**“确定”**。

1.  验证后，再次单击**“确定”**

1.  在 Log analytics 下，单击“配置”

1.  **单击**“新建工作区”

1.  输入以下设置

     |Log Analytics 工作区|订阅|资源组 | 位置| 定价层|
     |-----------------------|------------|---------------|---------|   -------------
     |Mod4Lab3YOURNAME|你的订阅|你在实验设置中创建的 RG|   美国东部 | 每 GB (2018)|

1.  **单击确定**。
警告
**注**：设置事件中心需要额外的步骤，因为 Azure 门户不允许从该位置创建事件中心


1.  要设置**“事件中心”**进行配置，在**“门户”**顶部单击 **Azure Cloud Shell**。

1.  **输入**下列 **Powerhshell 命令**。

     ```powershell
    New-AzEventHubNamespace -ResourceGroupName Mod4Lab3  -NamespaceName     Mod3Lab4 -Location eastus
     ```

     ```powershell
    New-AzEventHub -ResourceGroupName Mod4Lab3 -NamespaceName Mod3Lab4  -EventHubName Mod3Lab4 -MessageRetentionInDays 3
     ```

1.  这些命令完成后，单击“事件中心”下的“配置”

1.  **选择**下列信息

     | 订阅|中心命名空间|中心名| 中心策略名|
     |-------------|-------------|--------|----------------|
     |你的订阅| Mod3Lab4|mod3lab4|RootManageSharedAccessKey|


1.  **单击“确定”**

1.  你现在可以在**“审核设置”**页面上单击**“保存”**

    **结果**：现在，你已为 SQL Db 启用审核 


1.  要访问日志，请返回**“资源组”**，**“SQL 数据库”**和**“服务器”**保留在这里

1.  **选择**你以前创建的 **Mod3Lab3** Log analytics 工作区

1.  **单击**“日志”

1.  在查询空间中输入以下代码，然后**单击“运行”**。

     ```cli
    Event | where Source  == "MSSQLSERVER" 
     ```

1.  你将看不到任何结果，请阅读以下警告
警告
**注**：因为我们已经在带有测试数据的新数据库上设置了日志，所以可以查看最小日志，以显示示例中的日志显示方式，我们可以使用示例 log analytics 网站（其中包含示例数据）进行查看。


### 任务 2 - 分析审核日志和报告

1.  在新 Web 浏览器标签中访问 **`https://portal.loganalytics.io/demo`**

1.  在查询空间中输入以下代码，然后**点击运行**。

     ```cli
    Event | where Source  == "MSSQLSERVER" 
     ```

1.  在这里，你可以展开一些示例审核日志，以查看它们在实时系统中的外观

1.  在查询空间中输入以下代码，然后**单击“运行”**。

     ```cli
    Event 
    | where EventLevelName == "Error" 
    | where TimeGenerated > ago(1d) 
    | where Source != "HealthService" 
    | where Source != "Microsoft-Windows-DistributedCOM" 
    | summarize count() by Source
     ```

1.  **单击图表**

1.  在这里，你可以查看如何将日志数据显示为图表数据

1.  **请单击****“堆积列”**下拉列表并选择**“饼图”**

1.  在这里你能以不同图表看到相同的数据

1.  在窗口的右上角，你可以**单击“导出”**，将数据导出为 **CSV**。

 Log analytics 所使用的查询语言称为 Kusto 查询语言，该语言的完整文档可在此处找到 **`https://docs.microsoft.com/zh-cn/azure/kusto/query/`**



**“结果”**：现在，你已经完成了 SQL 数据库上的日志设置，并针对 log analytics 工作区运行查询以查看将要生成的审核日志


