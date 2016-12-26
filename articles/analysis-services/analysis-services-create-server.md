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
ms.date: 11/02/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 147c27d12b87379ff02b871ceabd8154e53b6c1a
ms.openlocfilehash: f189e19b5b843d6a7d056c8230bc06d85e568202


---
# <a name="create-an-analysis-services-server"></a>Création d’un serveur Analysis Services
Cet article vous guide dans le processus de création d’une nouvelle ressource de serveur Analysis Services dans votre abonnement Azure.

## <a name="before-you-begin"></a>Avant de commencer
Pour commencer, vous avez besoin des éléments suivants :

* **Abonnement Azure** : visitez [version d’évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
* **Azure Active Directory** : votre abonnement doit être associé à un locataire Azure Active Directory. Vous devez également être connecté à Azure avec un compte dans cette instance d’Azure Active Directory. Les comptes Microsoft ne sont pas pris en charge. Pour plus d’informations, consultez [Authentification utilisateur](analysis-services-overview.md#secure).
* **Groupe de ressources** : utilisez un groupe de ressources existant ou [créez-en un](../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> La création d’un serveur Analysis Services peut donner lieu à un nouveau service facturable. Pour en savoir plus, consultez [Tarification d’Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).
> 
> 

## <a name="create-an-analysis-services-server"></a>Création d’un serveur Analysis Services
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **+ Nouveau** > **Intelligence + analyse** > **Analysis Services**.
3. Dans le panneau **Analysis Services**, renseignez les champs requis, puis appuyez sur **Créer**.
   
    ![Créer un serveur](./media/analysis-services-create-server/aas-create-server-blade.png)
   
   * **Nom du serveur** : tapez un nom unique utilisé pour se référencer au serveur.
   * **Abonnement** : sélectionnez l’abonnement de facturation de ce serveur.
   * **Groupe de ressources** : ce conteneur est conçu pour vous aider à gérer un ensemble de ressources Microsoft Azure. Pour en savoir plus, consultez les [groupes de ressources](../azure-resource-manager/resource-group-overview.md).
   * **Emplacement** : cet emplacement du centre de données Azure héberge le serveur. Choisissez l’emplacement le plus proche de votre plus grande base d’utilisateurs.
   * **Niveau de tarification** : sélectionnez un niveau de tarification. Les modèles tabulaires sont pris en charge jusqu’à 100 Go. Vous pouvez toujours modifier le niveau de tarification ultérieurement.
4. Cliquez sur **Create**.

La création prend généralement moins d’une minute, souvent quelques secondes seulement. Si vous avez sélectionné **Add to Portal** (Ajouter au portail), accédez au portail pour voir votre nouveau serveur. Ou, accédez à **More services (Plus de services)** > **Analysis Services** pour voir si votre serveur est prêt. S’il n’apparaît pas, actualisez la liste.

 ![Tableau de bord](./media/analysis-services-create-server/aas-create-server-dashboard.png)

## <a name="automate-server-creation"></a>Automatiser la création de serveurs
Vous pouvez automatiser l’approvisionnement des serveurs à la volée avec les fichiers du modèle Azure Resource Manager. Pour en savoir plus, regardez cette vidéo utile.

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation/player]
>
>


## <a name="next-steps"></a>Étapes suivantes
Une fois que vous avez créé votre serveur, vous pouvez y [déployer un modèle](analysis-services-deploy.md) à l’aide de SSDT ou avec SSMS.

Si un modèle déployé sur votre serveur se connecte aux sources de données locales, vous devez installer une [passerelle de données locale](analysis-services-gateway.md) sur un ordinateur de votre réseau.




<!--HONumber=Nov16_HO4-->


