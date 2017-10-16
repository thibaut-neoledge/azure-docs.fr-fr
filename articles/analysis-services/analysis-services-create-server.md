---
title: "Création d’un serveur Analysis Services dans Azure | Microsoft Docs"
description: "Apprenez à créer une instance de serveur Analysis Services dans Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 7f560216-8a9a-4d06-852e-48cf24deab19
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/15/2017
ms.author: owend
ms.openlocfilehash: 95b367e7cd74405088190c1fe19cf92990759d90
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-analysis-services-server-in-azure-portal"></a>Création d’un serveur Azure Analysis Services dans le portail Azure
Cet article vous guide lors du processus de création d’une ressource de serveur Analysis Services dans votre abonnement Azure.

## <a name="before-you-begin"></a>Avant de commencer
Pour effectuer ce démarrage rapide, les éléments suivants sont requis :

* **Abonnement Azure** : visitez [version d’évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
* **Azure Active Directory** : votre abonnement doit être associé à un locataire Azure Active Directory. Vous devez également être connecté à Azure avec un compte dans cette instance d’Azure Active Directory. Les comptes Microsoft ne sont pas pris en charge. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).
* **Groupe de ressources** : utilisez un groupe de ressources existant ou [créez-en un](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> La création d’un serveur peut donner lieu à un nouveau service facturable. Pour en savoir plus, consultez [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="to-create-a-server-in-azure-portal"></a>Pour créer un serveur dans le portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).  
2. Cliquez sur **+ Nouveau** > **Données + analyse** > **Analysis Services**.
3. Dans le panneau **Analysis Services**, renseignez les champs requis, puis appuyez sur **Créer**.
   
    ![Créer un serveur](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nom du serveur** : tapez un nom unique utilisé pour se référencer au serveur.
   * **Abonnement** : sélectionnez l’abonnement de facturation de ce serveur.
   * **Groupe de ressources** : ces conteneurs sont conçus pour vous aider à gérer un ensemble de ressources Azure. Pour en savoir plus, consultez les [groupes de ressources](../azure-resource-manager/resource-group-overview.md).
   * **Emplacement** : cet emplacement du centre de données Azure héberge le serveur. Choisissez l’emplacement le plus proche de votre plus grande base d’utilisateurs.
   * **Niveau de tarification** : sélectionnez un niveau de tarification. Les modèles tabulaires sont pris en charge jusqu’à 400 Go. Pour en savoir plus, voir [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
4. Cliquez sur **Create**.

La création prend généralement moins d’une minute, souvent quelques secondes seulement. Si vous avez sélectionné **Add to Portal** (Ajouter au portail), accédez au portail pour voir votre nouveau serveur. Ou, accédez à **More services (Plus de services)** > **Analysis Services** pour voir si votre serveur est prêt.

 ![Tableau de bord](./media/analysis-services-create-server/aas-create-server-dashboard.png)


## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé votre serveur, vous pouvez y [déployer un modèle](analysis-services-deploy.md) via SSDT ou SSMS.

Si un modèle déployé sur votre serveur se connecte à des sources de données locales, vous devez installer une [passerelle de données locale](analysis-services-gateway.md) sur un ordinateur de votre réseau.

