<properties
    pageTitle="Azure CLI 脚本示例 - 将 Web 应用连接到 SQL 数据库 | Azure"
    description="Azure CLI 脚本示例 - 将 Web 应用连接到 SQL 数据库"
    services="appservice"
    documentationcenter="appservice"
    author="syntaxc4"
    manager="erikre"
    editor=""
    tags="azure-service-management"
    translationtype="Human Translation" />
<tags
    ms.assetid="7c2efdd0-f553-4038-a77a-e953021b3f77"
    ms.service="app-service"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="web"
    ms.date="03/20/2017"
    wacn.date="04/24/2017"
    ms.author="cfowler"
    ms.sourcegitcommit="a114d832e9c5320e9a109c9020fcaa2f2fdd43a9"
    ms.openlocfilehash="44e4b373e4f7f7fbcd710de65567b25dd00200b7"
    ms.lasthandoff="04/14/2017" />

# <a name="connect-a-web-app-to-a-sql-database"></a>将 Web 应用连接到 SQL 数据库

在此方案中，你将了解如何创建 Azure SQL 数据库和 Azure Web 应用。 然后，将使用应用设置将 SQL 数据库链接到 Web 应用。

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

[AZURE.INCLUDE [azure-cli-2-azurechinacloud-environment-parameter](../../includes/azure-cli-2-azurechinacloud-environment-parameter.md)]

## <a name="sample-script"></a>示例脚本

    #/bin/bash

    # Variables
    appName="webappwithSQL$random"
    serverName="webappwithsql$random"
    location="ChinaNorth"
    startip="0.0.0.0"
    endip="0.0.0.0"
    username="<replace-with-username>"
    sqlServerPassword="<replace-with-password>"

    # Create a Resource Group 
    az group create --name myResourceGroup --location $location

    # Create an App Service Plan
    az appservice plan create --name WebAppWithSQLPlan --resource-group myResourceGroup --location $location

    # Create a Web App
    az appservice web create --name $appName --plan WebAppWithSQLPlan --resource-group myResourceGroup

    # Create a SQL Server
    az sql server create --name $serverName --resource-group myResourceGroup --location $location --admin-user $username --admin-password $sqlServerPassword

    # Configure Firewall for Azure Access
    az sql server firewall-rule create --resource-group myResourceGroup --server $serverName --name AllowYourIp --start-ip-address $startip --end-ip-address $endip

    # Create Database on Server
    az sql db create --resource-group myResourceGroup --server $serverName --name MySampleDatabase --service-objective S0

    # Assign the connection string to an App Setting in the Web App
    az appservice web config appsettings update --settings "SQLSRV_CONNSTR=Server=tcp:$serverName.database.chinacloudapi.cn;Database=MySampleDatabase;User ID=$username@$serverName;Password=$sqlServerPassword;Trusted_Connection=False;Encrypt=True;" --name $appName --resource-group myResourceGroup

[AZURE.INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令创建资源组、Web 应用、SQL 数据库和所有相关资源。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [az group create](https://docs.microsoft.com/zh-cn/cli/azure/group#create) | 创建用于存储所有资源的资源组。 |
| [az appservice plan create](https://docs.microsoft.com/zh-cn/cli/azure/appservice/plan#create) | 创建应用服务计划。 这与 Azure Web 应用的服务器场类似。 |
| [az appservice web create](https://docs.microsoft.com/zh-cn/cli/azure/webapp#create) | 创建应用服务计划中的 Azure Web 应用。 |
| [az sql server create](https://docs.microsoft.com/zh-cn/cli/azure/sql/server#create) | 创建 SQL 数据库服务器。  |
| [az sql db create](https://docs.microsoft.com/zh-cn/cli/azure/sql/db#create) | 使用 SQL 数据库服务器创建新的数据库。 |
| [az appservice web config appsetings update](https://docs.microsoft.com/zh-cn/cli/azure/webapp/config/appsettings#update) | 创建或更新 Azure Web 应用的应用设置。 应用设置将作为应用的环境变量公开。 |

## <a name="next-steps"></a>后续步骤

有关 Azure CLI 的详细信息，请参阅 [Azure CLI 文档](https://docs.microsoft.com/zh-cn/cli/azure/overview)。

可以在 [Azure 应用服务文档](/documentation/articles/app-service-cli-samples/)中找到其他应用服务 CLI 脚本示例。