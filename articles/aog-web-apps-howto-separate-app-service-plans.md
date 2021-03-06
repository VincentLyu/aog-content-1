---
title: 如何将现有的 Azure Web 应用分离到不同的应用服务计划
description: 通过管理门户及 PowerShell 将 Azure Web 应用分离到不同的应用服务计划
services: app-service-web
documentationCenter: ''
author: maysmiling
manager: ''
editor: ''
tags: Azure,Web 应用,应用服务计划,PowerShell,Portal

ms.service: app-service-web
wacn.topic: aog
ms.topic: article
ms.author: v-amazha
ms.date: 12/132016
wacn.date: 07/18/2017
---

# 如何将现有的 Azure Web 应用分离到不同的应用服务计划 #

用户的多个 Web 应用可能使用同一个应用服务计划，那么这些 Web 应用会共享同一个应用服务计划下的资源，同时缩放应用服务计划将会影响到所有的 Web 应用。当用户想将某个 Web 应用分离出来进行独立管理时，可以考虑将 Web 应用迁移到另一个应用服务计划中，本文详细介绍了如何迁移 web 应用到新的应用服务计划中的方法：

> [!NOTE]
> 1. 进行 Web 应用迁移时，在要迁移的应用计划列表中只能看到 Web 应用在初期创建时所在的资源组下的应用计划。即使 Web 应用已经做了迁移资源组的操作，也还是只能看到在原来的资源下的应用计划列表。
> 2. 在涉及到需要从一个资源组一并迁移应用计划到另一个资源组时，不能只迁移应用计划。而是要求所在资源组下所有的其他应用服务资源一起迁移。且要求目标资源组没有任何应用服务资源。
> 3. 如果不一并迁移应用服务计划，则可以只将 Web 应用迁移到其他资源组。


### 方法一 ###

通过 [Azure 门户](https://portal.azure.cn)(新 portal)进行更改:

1. 登录 Azure 门户（新 portal）。
2. 创建一个新的应用服务计划。
3. 选择要进行分离的 web 应用，在弹出的选项中点击 “更改应用服务计划”。
4. 在列表中选择之前创建好的应用服务计划进行更改。

![portal-app-service-plans](./media/aog-web-apps-howto-separate-app-service-plans/azure-portal-app-service-plans.png)

### 方法二 ###

通过 PowerShell 命令将已有的 Web 应用迁移到新的应用服务计划：

1. 运行以下命令登录中国版 Azure :

    ```
    Login-AzureRmAccount -EnvironmentName "AzureChinaCloud"
    ```

    ![powershell-login](./media/aog-web-apps-howto-separate-app-service-plans/powershell-login.png)

2. 运行以下命令创建新的应用服务计划；

    ```
    New-AzureRmAppServicePlan -Location ChinaEast -Name <YourAppServicePlanName> -ResourceGroupName <YourResouceGroupName> -Tier Free
    ```

    > [!NOTE]
	> Location 参数可以选择 ChinaEast 或者 ChinaNorth。

    命令运行成功后返回以下结果，请将 Id 字段对应信息复制出来以备后续使用。

    ![powershell-app-service-plan-id](./media/aog-web-apps-howto-separate-app-service-plans/powershell-app-service-plan-id.png)

3. 运行以下命令将网站迁移到新的应用服务计划中;

    ```
    Set-AzureRmResource -ResourceName <YourWebSiteName> -ResourceType Microsoft.Web/sites -Properties @{"serverFarmId"="/subscriptions/<SubscirptionID>/resourceGroups/<ResourceGroupName1>/providers/Microsoft.Web/serverfarms/TestAppServicePlan"} -ResourceGroupName <ResourceGroupName2>
    ```

    > [!NOTE]
    > 1. 将 `Properties` 参数中的 `serverFarmID` 的值替换成步骤 2 返回的 `Id` 值；
	> 2. 将参数`<ResourceGroupName1>`替换成应用服务计划所在的资源组名称，将参数`<ResourceGroupName2>`替换成 Web 应用所在的资源组名称。

4. 登录 Azure Portal 查看 Web 应用是否已经迁移到新的应用服务计划中。

## 参考链接

- [将资源移到新资源组或订阅中](https://docs.azure.cn/zh-cn/azure-resource-manager/resource-group-move-resources#app-service-limitations)

- [迁移 Web 应用到新的应用服务计划的相关限制和说明](https://docs.azure.cn/zh-cn/articles/web-mobile/aog-web-apps-migrate-asp-limit)

- [Azure Web 应用重新配置应用服务计划时无法选择新创建的应用服务计划](https://docs.azure.cn/zh-cn/articles/web-mobile/aog-web-apps-qa-portal-change-app-service-plans)