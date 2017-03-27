---
title: "Créer des batteries de serveurs SharePoint dans Azure | Microsoft Docs"
description: "Créez rapidement une nouvelle batterie de serveurs SharePoint 2013 ou SharePoint 2016 dans Azure à l’aide de la Place de marché du Portail Azure."
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 89b124da-019d-4179-86dd-ad418d05a4f2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 9e8eb54913b1e7e3f40ac2c9b80b0818eee3a35f
ms.lasthandoff: 03/18/2017


---
# <a name="create-sharepoint-server-farms-using-the-azure-portal-marketplace"></a>Créer des batteries de serveurs SharePoint à l’aide de la Place de marché du Portail Azure

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="sharepoint-2013-farms"></a>Batteries de serveurs SharePoint 2013
Avec Microsoft Azure Marketplace du portail Azure, vous pouvez créer rapidement des batteries de serveurs SharePoint Server 2013 préconfigurées. Cela peut vous faire gagner un temps précieux lorsque vous avez besoin d'une batterie de serveurs SharePoint de base ou à haute disponibilité pour un environnement de test/développement ou si vous envisagez l'adoption de SharePoint Server 2013 comme solution de collaboration pour votre entreprise.

> [!NOTE]
> L'élément **Batterie de serveurs SharePoint** dans Azure Marketplace du portail Azure a été supprimé. Il a été remplacé par les éléments **Batterie de serveurs SharePoint 2013 non hautement disponible** et **Batterie de serveurs SharePoint 2013 hautement disponible**.
>
>

La batterie de serveurs SharePoint de base se compose de trois machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/Non-HAFarm.png)

Vous pouvez utiliser cette configuration de batterie comme configuration simplifiée pour le développement d'applications SharePoint ou pour votre évaluation initiale de SharePoint 2013.

Pour créer la batterie de serveurs SharePoint de base (trois serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-nonha/).
2. Cliquez sur **Déployer**.
3. Dans le volet **Batterie de serveurs SharePoint 2013 non hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des étapes du panneau de **création d’une batterie de serveurs SharePoint 2013 non hautement disponible**, puis cliquez sur **Créer**.

La batterie de serveurs SharePoint à haute disponibilité se compose de neuf machines virtuelles dans cette configuration.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/HAFarm.png)

Vous pouvez utiliser cette configuration de batterie pour tester des charges de clients plus élevées, la haute disponibilité du site SharePoint externe et des groupes de disponibilité SQL Server AlwaysOn pour une batterie SharePoint. Vous pouvez également l'utiliser pour le développement d'applications SharePoint dans un environnement à haute disponibilité.

Pour créer la batterie de serveurs SharePoint à haute disponibilité (neuf serveurs) :

1. Cliquez [ici](https://azure.microsoft.com/marketplace/partners/sharepoint2013/sharepoint2013farmsharepoint2013-ha/).
2. Cliquez sur **Déployer**.
3. Dans le panneau **Batterie de serveurs SharePoint 2013 hautement disponible**, cliquez sur **Créer**.
4. Spécifiez les paramètres des 7 étapes du panneau **de création d’une batterie de serveurs SharePoint 2013 hautement disponible**, puis cliquez sur **Créer**.

> [!NOTE]
> Vous ne pouvez pas créer la **batterie de serveurs SharePoint 2013 non hautement disponible** ou la **batterie de serveurs SharePoint 2013 hautement disponible** avec un essai gratuit d’Azure.
>
>

Le portail Azure crée ces deux batteries de serveurs dans un réseau virtuel sur le cloud uniquement avec une présence Web accessible sur Internet. Il n’existe pas de connexion VPN de site à site ou ExpressRoute en amont vers le réseau de votre organisation.

> [!NOTE]
> Lorsque vous créez des batteries de serveurs de base ou hautement disponibles à l’aide du portail, vous ne pouvez pas spécifier un groupe de ressources existant. Pour contourner cette limitation, créez ces batteries de serveurs avec Azure PowerShell. Pour plus d’informations, consultez la page [Créer des batteries de serveurs de développement/test SharePoint 2013 avec le portail Azure](https://technet.microsoft.com/library/mt743093.aspx#powershell).
>
>

## <a name="sharepoint-2016-farms"></a>Batteries de serveurs SharePoint 2016
Consultez [cette article](https://technet.microsoft.com/library/mt723354.aspx) pour obtenir des instructions sur la création de la batterie à un seul serveur SharePoint Server 2016 suivante.

![sharepointfarm](./media/virtual-machines-windows-sharepoint-farm/SP2016Farm.png)

## <a name="managing-the-sharepoint-farms"></a>Gestion des batteries de serveurs SharePoint
Vous pouvez gérer les serveurs de ces batteries par le biais de connexions Bureau à distance. Pour plus d'informations, consultez [Connexion à la machine virtuelle](virtual-machines-windows-quick-create-portal.md#connect-to-virtual-machine).

Sur le site d'administration centrale de SharePoint, vous pouvez configurer Mes sites, des applications SharePoint, ainsi que d'autres fonctionnalités. Pour plus d'informations, consultez la page sur la [configuration de SharePoint](http://technet.microsoft.com/library/ee836142.aspx).

## <a name="next-steps"></a>Étapes suivantes
* Découvrez d’autres [configurations de SharePoint](https://technet.microsoft.com/library/dn635309.aspx) dans les services d’infrastructure Azure.

