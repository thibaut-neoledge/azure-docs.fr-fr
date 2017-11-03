---
title: Analyse et diagnostic des conteneurs Azure Service Fabric | Microsoft Docs
description: "Découvrez comment surveiller et diagnostiquer les conteneurs orchestrés sur Microsoft Azure Service Fabric avec la solution de conteneurs d’OMS."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 319ee2c0f7492389bc1767aa2669dd273f8cfa1b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Surveiller les conteneurs Windows Server avec OMS

## <a name="oms-containers-solution"></a>Solution de conteneurs d’OMS

OMS (Operations Management Suite) Log Analytics propose une solution de conteneurs qui permet de surveiller les conteneurs. Allant de pair avec Service Fabric, cette solution est un outil formidable pour contrôler les déploiements de conteneurs orchestrés sur Service Fabric. Voici un exemple simple de l’aspect du tableau de bord dans la solution :

![Tableau de bord OMS de base](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Les différents types de journaux pouvant être interrogés dans l’outil OMS Log Analytics sont également récupérés et peuvent être utilisés pour visualiser les métriques ou les événements générés. Les types de journaux collectés sont :

1. ContainerInventory : affiche des informations sur les images, le nom et l’emplacement du conteneur
2. ContainerImageInventory : informations sur les images déployées, ID ou tailles compris
3. ContainerLog : journaux d’erreurs spécifiques, journaux de docker (stdout, etc.) et autres entrées
4. ContainerServiceLog : les commandes de démon docker qui ont été exécutées
5. Performances : les compteurs de performances, dont l’utilisation par le conteneur du processeur, de la mémoire, du E/S de disque, du trafic réseau et de mesures personnalisées à partir des machines hôtes

Cet article décrit les étapes requises pour configurer la surveillance de conteneur pour votre cluster. Pour en savoir plus sur la solution de conteneurs d’OMS, consultez leur [documentation](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Configuration d’un cluster Service Fabric

Créez un cluster dans Azure à l’aide du modèle Azure Resource Manager qui se trouve [ici](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows). Veillez à ajouter un nom d’espace de travail OMS unique. Ce modèle déploie aussi par défaut un cluster dans la version préliminaire de Service Fabric (v255.255), ce qui signifie qu’il ne peut pas être utilisé en production et ne peut pas être mis à niveau vers une autre version de Service Fabric. Si vous décidez d’utiliser ce modèle pour une utilisation à long terme ou de production, modifiez la version sur un numéro de version stable.

Une fois le cluster configuré, vérifiez que vous avez installé le certificat approprié, et assurez-vous que vous êtes en mesure de vous connecter au cluster.

Vérifiez que votre groupe de ressources est configuré correctement en consultant le [portail](https://portal.azure.com/) et en recherchant le déploiement. Le groupe de ressources doit contenir toutes les ressources Service Fabric et doit également contenir une solution Log Analytics ainsi qu’une solution Service Fabric.

Pour modifier un cluster Service Fabric existant :
* Vérifiez que les diagnostics sont activés (dans le cas contraire, activez-les en [mettant à jour le groupe de machines virtuelles identiques](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Ajoutez un espace de travail OMS en créant une solution « Service Fabric Analytics » via Azure Marketplace
* Modifiez les sources de données de la solution Service Fabric pour collecter les données des tables appropriées du stockage Azure (configurées par WAD) dans le groupe de ressources dans lequel le cluster se trouve
* Ajoutez l’agent en tant [qu’extension du groupe de machines virtuelles identiques](/powershell/module/azurerm.compute/add-azurermvmssextension) via PowerShell ou en mettant à jour le groupe de machines virtuelles identiques (même lien que ci-dessus, pour modifier le modèle Resource Manager)

## <a name="2-deploy-a-container"></a>2. Déploiement d’un conteneur

Une fois que le cluster est prêt et que vous avez confirmé que vous pouvez y accéder, déployez un conteneur. Si vous choisissez d’utiliser la version préliminaire telle que définie par le modèle, vous pouvez également explorer la nouvelle fonctionnalité Docker Compose de Service Fabric. N’oubliez pas que la première fois qu’une image de conteneur est déployée sur un cluster, plusieurs minutes peuvent être nécessaires pour télécharger l’image en fonction de sa taille.

## <a name="3-add-the-containers-solution"></a>3. Ajout de la solution Conteneurs

Dans le portail Azure, créez une ressource Conteneurs (sous la catégorie Surveillance et gestion) via Azure Marketplace. 

![Ajout de la solution Conteneurs](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

Lors de l’étape de création, un espace de travail OMS est demandé. Sélectionnez celui qui a été créé avec le déploiement ci-dessus. Cette étape ajoute une solution Conteneurs à votre espace de travail OMS, et est automatiquement détectée par l’agent d’OMS déployé par le modèle. L’agent commence à collecter des données sur les processus des conteneurs dans le cluster, et après environ 10 à 15 minutes, vous devriez voir la solution s’actualiser avec des données comme dans l’image du tableau de bord ci-dessus.

## <a name="4-next-steps"></a>4. Étapes suivantes

OMS offre divers outils dans l’espace de travail pour le rendre plus utile pour vous. Explorez les options suivantes pour personnaliser la solution selon vos besoins :
- Familiarisez-vous avec les fonctionnalités de [requêtes et recherches dans les journaux](../log-analytics/log-analytics-log-searches.md) offertes dans le cadre de Log Analytics
- Configurez l’agent OMS pour récupérer des compteurs de performance spécifiques (accédez à la page d’accueil de l’espace de travail > Paramètres > Données > Compteurs de performances Windows)
- Configurer OMS pour configurer des règles [d’alerte automatisée](../log-analytics/log-analytics-alerts.md) pour vous aider lors de détections et diagnostics