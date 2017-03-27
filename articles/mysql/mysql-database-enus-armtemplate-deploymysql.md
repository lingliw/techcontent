<properties linkid="" urlDisplayName="" pageTitle="Use Azure Resource Manager Templates to Deploy MySQL Paas – Azure Cloud" metaKeywords="Azure Cloud, technical documentation, documents and resources, MySQL, database, Resource Manager, ARM, ARM Template, Azure MySQL, MySQL PaaS, Azure MySQL PaaS, Azure MySQL Service, Azure RDS" description="This article describes how to use Azure Resource Manager Templates (ARM Templates) to rapidly deploy MySQL PaaS and other popular apps." metaCanonical="" services="MySQL" documentationCenter="Services" title="" authors="" solutions="" manager="" editor="" />

<tags ms.service="mysql" ms.date="03/14/2017" wacn.date="03/14/2017" wacn.lang="cn" />

> [AZURE.LANGUAGE]
- [Chinese](/documentation/articles/mysql-database-armtemplate-deploymysql/)

#Use Azure Resource Manager Templates to eploy MySQL Paas

This article describes how to use Azure Resource Manager Templates (ARM Templates) to rapidly deploy MySQL PaaS and other popular apps.

##Understanding Azure Resource Manager Templates

A Resource Manager Template is a JavaScript Object Notation (JSON) file that defines one or several resources that are to be deployed to resource groups. It also defines the dependencies between the resources being deployed. Using templates allows you to repeatedly deploy resources in a consistent manner. For more information about Resource Manager and Resource Manager Templates, please read [Azure Resource Manager Overview](https://docs.microsoft.com/zh-cn/azure/azure-resource-manager/resource-group-overview).

##Use Resource Manager Templates to Deploy MySQL Paas

This section describes how to use PowerShell with ARM Templates to deploy MySQL PaaS on Azure. Your template may be a local file or an external file that is accessed via a URI. If the template resides in a storage account, you can restrict access to the template and provide a Shared Access Signature (SAS) token during the deployment process.

You can use the following PowerShell commands to get started quickly with your deployment (using Azure PowerShell 1.0.0+ as an example):

	New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "chinaeast"
	
	New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\example.json

These commands will create a resource group and deploy the template to the resource group. The template file is a local file. If this operation is successful, you will obtain all the required deployed resources.

A fairly complete Resource Manager Template in JSON format is provided below. You can modify this template to meet your specific requirements.

	{
	  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "mysqlServerName": {
	      "type": "string",
	      "metadata": {
	        "description": "MySql 服务器的名字. 最大长度为64个字母数字字符. 请通过 @mysqlServerName.mysqldb.chinacloudapi.cn访问MySql数据库. 管理配置MySql数据库请访问 https://manage.windowsazure.cn"
	      }
	    },
	    "mysqlServerSku": {
	      "type": "string",
	      "defaultValue": "MS2",
	      "allowedValues": [
	        "MS1", "MS2", "MS3", "MS4", "MS5", "MS6"
	      ],
	      "metadata": {
	        "description": "MySql 服务器的性能版本，价格详情请访问 https://www.azure.cn/pricing/details/mysql/"
	      }
	    },
	    "mysqlServerVersion": {
	      "type": "string",
	      "allowedValues": [
	        "5.6", "5.7", "5.5"
	       ],
	      "defaultValue": "5.6",
	      "metadata": {
	        "description": "MySQL服务器版本. "
	      }
	    },
	    "mysqlUserName": {
	      "type": "string",
	      "metadata": {
	        "description": "MySql 服务器登录名。登录名格式为 ‘MySQL服务器名%登录名’，长度小于16个字符。用户名和密码规范请参考： http://dev.mysql.com/doc/refman/5.6/en/user-names.html"
	      }
	    },
	    "mysqlPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "MySql 服务器登录密码。长度必须大于8个字符，小于64个字符。用户名和密码规范请参考： http://dev.mysql.com/doc/refman/5.6/en/user-names.html"
	      }
	    },
	    "mysqldatabaseName": {
	      "type": "string",
	      "metadata": {
	        "description": "数据库名字。必须小于64个字符，其它命名规范请参考: http://dev.mysql.com/doc/refman/5.6/en/identifiers.html"
	      }
	    }
	  },
	
	  "resources": [   
	    {
	      "type": "Microsoft.MySql/servers",
	      "name": "[parameters('mysqlServerName')]",
	      "apiVersion": "2015-09-01",
	      "location": "[resourceGroup().location]",
	      "tags": {
	        "displayName": "[parameters('mysqlServerName')]"
	      },
	      "sku": { "name": "[parameters('mysqlServerSku')]" },
	      "properties": {
	        "version": "[parameters('mysqlServerVersion')]",
	        "AllowAzureServices": true
	      },
	      "resources": [
	        {
	          "name": "[parameters('mysqlUserName')]",
	          "type": "users",
	          "apiVersion": "2015-09-01",
	          "dependsOn": [
	            "[concat('Microsoft.MySql/servers/', parameters('mysqlServerName'))]"
	          ],
	          "properties": {
	            "password": "[parameters('mysqlPassword')]"
	          }
	        },
	        {
	          "name": "[parameters('mysqldatabaseName')]",
	          "type": "databases",
	          "tags": {
	            "displayName": "[parameters('mysqldatabaseName')]"
	          },
	          "apiVersion": "2015-09-01",
	          "dependsOn": [
	            "[concat('Microsoft.MySql/servers/', parameters('mysqlServerName'))]"
	          ],
	          "properties": {
	           "Charset": "utf8",
	           "Collation": "utf8_general_ci"
              },
	          "resources": [
	            {
	              "name": "[parameters('mysqlUserName')]",
	              "type": "privileges",
	              "apiVersion": "2015-09-01",
	              "dependsOn": [
	                "[concat('Microsoft.MySql/servers/', parameters('mysqlServerName'), '/users/', parameters('mysqlUserName'))]",
	                "[concat('Microsoft.MySql/servers/', parameters('mysqlServerName'), '/databases/', parameters('mysqldatabaseName'))]"
	              ],
	              "properties": {
	                "level": "ReadWrite"
	              }
	            }
	          ]
	        }
	      ]
	    }
	  ]
	}

##Publish an ARM Template on Azure Marketplace

You can publish a customized ARM Template on Azure Marketplace to enable end users to implement one-click deployment for all cloud services. Please see the Azure Marketplace Publisher Guide for detailed instructions on how to publish [ARM Templates on Azure Marketplace](https://market.azure.cn/Documentation/article/publishguide/).

Azure Marketplace is a mirror image warehouse that is used by third-party independent software vendors (ISVs) to publish mirror image-type software, and which enables paying Azure users to create new virtual machine instances and download and use the third-party images that they need. This function helps companies looking for innovative cloud-based solutions to get in direct contact with Azure ISV partners that develop innovative solutions.

Azure Marketplace makes it easier to find, obtain, and use apps built and thoroughly tested on the Azure platform, allowing you to be certain that the app can be trusted and runs smoothly on Azure.

Azure Marketplace brings new business channels and users to ISVs, while also greatly simplifying the promotion, management, and deployment of software. We strongly recommend that you use mirror images to deploy the relevant apps. With just a few clicks, you can rapidly obtain system environments or software identical to the mirror image. This means that you don’t need to worry about infrastructure and environment configuration, since you can conveniently create ready-to-use runtime environments for every platform.

##Star Products on Azure Marketplace

![](./media/mysql-database-armtemplate-deploymysql/fuwang.png)

###Servinet LNMP Cluster w/ MySQL PaaS-1.0

The LNMP provided by Shanghai Servinet relies on the latest ARM architecture and provides full cluster deployment (default dual-server deployment). All the virtual machines have been added to high-availability groups and are automatically assigned to different Nginx 1.10.1. The framework is suitable for use with classic Nginx front-end/back-end separation (Nginx active-passive separation).

[More Information](https://market.azure.cn/Vhd/Show?vhdId=12005&version=14150)

[Immediate Deployment](https://market.azure.cn/VM/Launch?vhdId=12005&version=14150) ([requires Azure Marketplace login](https://market.azure.cn/Sign/Login?url=%2fVhd%2fShow%3fvhdId%3d12005%26version%3d14150))

![](./media/mysql-database-armtemplate-deploymysql/wordpress.png)

###WordPress-4.6.1 (Web app + MySQL PaaS)

WordPress is a very widely used CMS. This app was created with an Azure Resource Manager Template. By using this ARM Template, you can quickly create web apps and MySQL databases, and deploy WordPress website source code. Subsequent maintenance on websites and databases can be performed via the Management Portal for data security, high availability, and more convenient management.

[More Information](https://market.azure.cn/Vhd/Show?vhdId=12006&version=14125)

[Immediate Deployment](https://market.azure.cn/VM/Launch?vhdId=12006&version=14125) ([requires Azure Marketplace login](https://market.azure.cn/Sign/Login?url=%2fVhd%2fShow%3fvhdId%3d12006%26version%3d14125))

![](./media/mysql-database-armtemplate-deploymysql/zabbix.png)

###Zabbix 2.2.2 (Ubuntu 14.04 LTS/OpenLogic 7.2)

Zabbix (http://www.zabbix.com) is a free, enterprise class, open-source network monitoring tool that can effectively monitor the status of Windows, Linux, and Unix servers, switches, routers, and other network devices, as well as devices such as printers.

[More Information](https://market.azure.cn/Vhd/Show?vhdId=12009&version=14123)

[Immediate Deployment](https://market.azure.cn/VM/Launch?vhdId=12009&version=14123) ([requires Azure Marketplace login](https://market.azure.cn/Sign/Login?url=%2fVhd%2fShow%3fvhdId%3d12009%26version%3d14123))

<!---HONumber=AcomDC_0315_2017_MySql-->