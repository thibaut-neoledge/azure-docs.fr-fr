---
title: "Déployer sur Azure Analysis Services | Microsoft Docs"
description: "Découvrez comment déployer un modèle tabulaire sur un serveur Azure Analysis Services."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/20/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 18d64f1ad4ef0dd41ae9302d08e02e94d1c608f5


---
# <a name="deploy-to-azure-analysis-services"></a>Déployer sur Azure Analysis Services
Une fois que vous avez créé un serveur dans votre abonnement Azure, vous êtes prêt à déployer une base de données de modèle tabulaire sur celui-ci. Vous pouvez utiliser SQL Server Data Tools (SSDT) pour créer et déployer un projet de modèle tabulaire sur lequel vous travaillez. Ou bien, vous pouvez utiliser SQL Server Management Studio (SSMS) pour déployer une base de données de modèle tabulaire existante à partir d’une instance Analysis Services.

## <a name="before-you-begin"></a>Avant de commencer
Pour commencer, vous avez besoin des éléments suivants :

* **Serveur Analysis Services** dans Azure. Pour en savoir plus, voir [Créer un serveur Analysis Services dans Azure](analysis-services-create-server.md).
* **Projet de modèle tabulaire** dans SSDT ou un modèle tabulaire existant au niveau de compatibilité 1200 sur une instance Analysis Services. Vous ne l’avez jamais fait ? Essayez le [didacticiel Adventure Works](https://msdn.microsoft.com/library/hh231691.aspx).
* **Passerelle locale** : si une ou plusieurs sources de données sont locales dans le réseau de votre entreprise, vous devez installer une [Passerelle de données locale](analysis-services-gateway.md). La passerelle est nécessaire pour que votre serveur se connecte dans le cloud à vos sources de données locales pour traiter et actualiser les données du modèle.

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>Pour déployer un modèle tabulaire à partir de SSDT
Pour déployer à partir de SSDT, veillez à utiliser la [dernière version](https://msdn.microsoft.com/library/mt204009.aspx), mise à jour le 30 septembre, 2016 ou version ultérieure.

> [!TIP]
> Avant de déployer, assurez-vous que vous pouvez traiter les données de vos tables. Dans SSDT, cliquez sur **Modèle** > **Traiter** > **Traiter tout**. Si le traitement échoue, le déploiement échouera également.
> 
> 

1. Avant de déployer, vous devez obtenir le nom du serveur. Dans **Portail Azure** > Serveur > **Présentation** > **Nom du serveur**, copiez le nom du serveur.
   
    ![Obtenir le nom du serveur dans Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. Dans SSDT > **Explorateur de solutions**, cliquez avec le bouton droit sur le projet > **Propriétés**. Ensuite, dans **Déploiement** > **Serveur**, collez le nom du serveur.   
   
    ![Coller le nom du serveur dans la propriété du serveur de déploiement](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur **Propriétés**, puis cliquez sur **Déployer**. Vous serez peut-être invité à vous connecter à Azure.
   
    ![Déployer sur un serveur](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    L’état du déploiement s’affiche dans la fenêtre Sortie et dans Déployer.
   
    ![état du déploiement](./media/analysis-services-deploy/aas-deploy-status.png)

C’est tout !

## <a name="to-deploy-using-xmla-script"></a>Pour déployer à l’aide du script XMLA
1. Dans SSMS, cliquez avec le bouton droit sur la base de données de modèle tabulaire que vous souhaitez déployer, cliquez sur **Script** > **Base de données de script en tant que** > **CRÉER vers**, puis choisissez un emplacement.
2. Exécutez la requête sur l’instance de serveur sur laquelle vous souhaitez déployer. Si vous déployez sur le même serveur, vous devez au moins modifier la propriété **nom** dans le script XMLA.  

## <a name="but-something-went-wrong"></a>Une erreur s’est toutefois produite
Si le déploiement échoue lors du déploiement de métadonnées, il est probable que SSDT n’ait pas pu se connecter à votre serveur. Vérifiez que vous pouvez vous connecter à votre serveur à l’aide de SSMS. Vérifiez ensuite que la propriété Serveur de déploiement du projet est correcte.

Si le déploiement échoue sur une table, il est probable que votre serveur n’ait pas pu se connecter à une source de données. Si votre source de données est locale dans le réseau de votre entreprise, veillez à installer une [Passerelle de données locale](analysis-services-gateway.md).

## <a name="next-steps"></a>Étapes suivantes
Maintenant que votre modèle tabulaire est déployé sur votre serveur, vous êtes prêt à vous connecter à celui-ci. Vous pouvez vous [connecter à celui-ci avec SSMS](analysis-services-manage.md) pour le gérer. Vous pouvez également vous [connecter à celui-ci à l’aide d’un outil client](analysis-services-connect.md) tel que Power BI, Power BI Desktop ou Excel, et commencer à créer des rapports.




<!--HONumber=Nov16_HO3-->


