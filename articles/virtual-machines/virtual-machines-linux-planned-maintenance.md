<properties
    pageTitle="Azure 中 Liunx VM 的计划内维护 | Azure"
    description="了解什么是 Azure 计划内维护以及它如何影响正在 Azure 中运行的 Windows 虚拟机。"
    services="virtual-machines-linux"
    documentationcenter=""
    author=""
    manager="timlt"
    editor=""
    tags="azure-resource-manager,azure-service-management" />
<tags
    ms.assetid=""
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="03/27/2017"
    wacn.date="05/15/2017"
    ms.author=""
    ms.translationtype="Human Translation"
    ms.sourcegitcommit="457fc748a9a2d66d7a2906b988e127b09ee11e18"
    ms.openlocfilehash="7753e3e88ec09fdc0b83c307db9da85c2e6d7648"
    ms.contentlocale="zh-cn"
    ms.lasthandoff="05/05/2017" />

# <a name="planned-maintenance-for-linux-virtual-machines"></a>Linux 虚拟机的计划维护 

Azure 在全球范围内定期执行更新，以提高虚拟机所基于的主机基础结构的可靠性、性能及安全性。 此类更新包括修补主机环境（OS、虚拟机监控程序以及主机上部署的各种代理）中的软件组件、升级网络组件以及硬件解除授权等多项内容。

大多数此类更新在执行时不会影响托管的虚拟机或云服务。

但是，也会存在更新对托管的虚拟机产生影响的情况：

-   使用就地 VM 迁移的 VM 保留维护会描述一种类型的更新，其中虚拟机在维护期间不会被重启。

-   VM 重启维护，维护时需要重启或重新部署到托管虚拟机。

请注意，本页介绍 Azure 如何执行计划内维护。 有关非计划事件（故障）的详细信息，请参阅[管理虚拟机的可用性](/documentation/articles/virtual-machines-windows-manage-availability/)。

<!--Update_Description: wording update-->