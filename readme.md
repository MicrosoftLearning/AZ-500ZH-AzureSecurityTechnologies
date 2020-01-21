# AZ-500 Azure 安全

- **你是 MCT 吗？** - 了解我们的 [MCT GitHub 用户指南](https://microsoftlearning.github.io/MCT-User-Guide/)
- **需要手动建立实验室教学吗？** - 可通过 [MicrosoftLearning / Docker-Build](https://github.com/MicrosoftLearning/Docker-Build) 存储库查看相关教学说明

> 请务必使用 [MCT 课件论坛](https://www.microsoft.com/zh-cn/learning/mct-central.aspx)，了解课程内容的建议或一般评论。此外，你可以将相关漏洞和课程错误上报至 [课件支持论坛](https://trainingsupport.microsoft.com/zh-cn)。
 
为了支持新变更，我们从 2019 年 11 月 1 日开始引入了一个新的 AZ-500 GitHub 存储库。之后陆续将所有 AZ-500 实验室都已替换为该存储库。

**我们做什么？**

*	我们在 GitHub 上发布实验室教学和实验室文件，以便课程作者和 MCT 之间进行互动。我们希望这将有助于在 Azure 平台发生变化时保持内容为最新版本。

*	这是 AZ-500，Microsoft Azure 安全课程的 GitHub 存储库。 

*	在每个存储库中，“说明”文件夹中都包含了 Markdown 格式的实验室指南。其他情况下，还需要 Allfiles\Labfiles 文件夹中的其他文件来完成实验室。并非每个课程都有相应的实验室文件。 

*	对于每次交付，培训师应通过 GitHub 下载最新文件。培训师还应检查“问题”选项卡，以查看其他 MCT 是否报告了任何错误。  

*	提供实验室时间预估，但培训讲师应根据受众检查实验室时间预估是否准确。

*	要进行实验室操作，你需要互联网连接和 Azure 订阅。有关使用 Cloud Shell 的更多信息，请阅读讲师准备指南。 

**我们如何操作？**

*	如果在教授这些课程时，你确定有需要改进的地方，请使用“问题”选项卡提供反馈。我们将定期创建新文件，整合相关的更改。 


* 首次启动 Azure Cloud Shell 时，系统可能会提示你创建一个 Azure 文件，共享以保留 Cloud Shell 文件。如果是这样，通常可以接受默认值，系统将在自动生成的资源组中创建存储帐户。请注意，如果删除该存储帐户，系统将再次提醒创建。

* 在执行基于模板的部署之前，你需要注册提供程序，用于处理模板中引用的资源类型的置备。使用 Azure 资源管理器模板部署由这些资源提供程序管理的资源时（如果你尚未注册这些资源提供程序），这是一项一次性操作（一个订阅账户）。你可以通过 Azure 门户中的订阅资源提供程序刀片服务器进行注册，也可以使用 Cloud Shell 运行 Register-AzResourceProvider PowerShell cmdlet 或 az 提供程序 Azure CLI 命令来进行注册。

我们希望使用 GitHub 存储库能够为实验室带来协作感，并提高实验室体验的整体质量。 

此致敬意，
*Azure 安全课件团队*