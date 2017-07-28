---
title: FAQ sur IoT Azure Suite | Microsoft Docs
description: "Forum Aux Questions (FAQ) relatives à IoT Suite"
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: corywink
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 9f850175be843b29432c4803a150ddb2ec79780d
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="frequently-asked-questions-for-iot-suite"></a>Forum Aux Questions (FAQ) relatives à IoT Suite

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Où trouver le code source des solutions préconfigurées ?

Le code source est stocké dans les référentiels GitHub suivants :
* [Solution préconfigurée de surveillance à distance][lnk-remote-monitoring-github]
* [Solution préconfigurée de maintenance prédictive][lnk-predictive-maintenance-github]

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Comment procéder à une mise à jour vers la dernière version de la solution préconfigurée de surveillance à distance qui utilise les fonctionnalités de gestion d’appareils IoT Hub ?

* Si vous déployez une solution préconfigurée à partir du site https://www.azureiotsuite.com/, elle déploie toujours une nouvelle instance de la version la plus récente de la solution.
* Si vous déployez une solution préconfigurée à l’aide de la ligne de commande, vous pouvez mettre à jour un déploiement existant avec le nouveau code. Consultez la page [Cloud deployment (Déploiement cloud)][lnk-cloud-deployment] dans le [référentiel][lnk-remote-monitoring-github] GitHub.

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Comment ajouter la prise en charge d’une nouvelle méthode de périphérique à la solution préconfigurée de surveillance à distance ?

Consultez la section [Add support for a new method to the simulator (Ajouter la prise en charge d’une nouvelle méthode au simulateur)][lnk-add-method] de l’article [Personnaliser une solution préconfigurée][lnk-customize].

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>L’appareil simulé ignore mes modifications de propriété souhaitées. Pourquoi ?
Dans la solution préconfigurée de surveillance à distance, le code d’appareil simulé utilise uniquement les propriétés souhaitées **Desired.Config.TemperatureMeanValue** et **Desired.Config.TelemetryInterval** pour mettre à jour les propriétés signalées. Toutes les autres demandes de modification de propriétés souhaitées sont ignorées.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Mon appareil n’apparaît pas dans la liste des appareils du tableau de bord de la solution. Pourquoi ?

La liste des appareils du tableau de bord de la solution utilise une requête pour retourner la liste des appareils. Pour le moment, une requête ne peut pas retourner plus de 10 000 appareils. Essayez de rendre les critères de recherche de votre requête plus restrictifs.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Quelle est la différence entre la suppression d’un groupe de ressources dans le portail Azure et un clic sur l’option supprimer d’une solution préconfigurée dans azureiotsuite.com ?

* Si vous supprimez la solution préconfigurée dans [azureiotsuite.com][lnk-azureiotsuite], vous supprimez toutes les ressources qui ont été configurées lors de la création de la solution préconfigurée. Si vous avez ajouté des ressources supplémentaires au groupe de ressources, elles sont également supprimées. 
* Si vous supprimez le groupe de ressources sur le [portail Azure][lnk-azure-portal], vous supprimez uniquement les ressources de ce groupe de ressources. Vous devez également supprimer l’application Azure Active Directory associée à la solution préconfigurée sur le [Portail Azure Classic][lnk-classic-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’IoT Hub puis-je configurer dans un abonnement ?

Par défaut, vous pouvez configurer [10 instances IoT Hub par abonnement][link-azuresublimits]. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite. Par conséquent, étant donné que chaque solution préconfigurée approvisionne un nouvel IoT Hub, vous ne pouvez configurer que 10 solutions préconfigurées au maximum dans un abonnement. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Combien d’instances d’Azure Cosmos DB puis-je configurer dans un abonnement ?

Cinquante. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 50 instances de Cosmos DB par abonnement. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Combien d’API Bing Maps gratuites puis-je configurer dans un abonnement ?

Deux. Vous pouvez créer uniquement deux cartes Bing - Transactions internes - Niveau 1 pour les plans d’entreprise dans un abonnement Azure. La solution de surveillance à distance est configurée par défaut avec le plan Transactions internes - Niveau 1. Par conséquent, vous pouvez configurer au maximum deux solutions de surveillance à distance préconfigurées sans modification.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>J’ai mis en place le déploiement d’une solution de surveillance à distance avec une carte statique. Comment faire pour ajouter une carte Bing interactive ?

1. Obtenez votre QueryKey Bing Maps API pour Entreprise sur le [portail Azure][lnk-azure-portal] : 
   
   1. Accédez au groupe de ressources contenant Bing Maps API pour Entreprise dans le [portail Azure][lnk-azure-portal].
   2. Cliquez sur **Tous les paramètres**, puis sur **Gestion des clés**. 
   3. Vous voyez deux clés : **MasterKey** et **QueryKey**. Copiez la valeur de **QueryKey**.
      
      > [!NOTE]
      > Vous n’avez aucun compte Bing Maps API pour Entreprise ? Créez-le sur le [portail Azure][lnk-azure-portal] en cliquant sur +Nouveau, en recherchant Bing Maps API pour Entreprise et en suivant la procédure.
      > 
      > 
2. Déroulez le code le plus récent dans [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Effectuez un déploiement local ou dans le cloud selon les instructions de déploiement par ligne de commande dans le dossier /docs/ du référentiel. 
4. Une fois le déploiement local ou cloud effectué, recherchez dans votre dossier racine le fichier *.user.config créé. Ouvrez ce fichier dans un éditeur de texte. 
5. Modifiez la ligne suivante en y incluant la valeur que vous avez copiée de votre clé **QueryKey** : 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Puis-je créer une solution préconfigurée si je dispose de Microsoft Azure pour DreamSpark ?

Actuellement, il est impossible de créer une solution préconfigurée avec un compte [Microsoft Azure pour DreamSpark][lnk-dreamspark]. Vous pouvez toutefois créer en quelques minutes un [compte d’évaluation Azure gratuit][lnk-30daytrial], que vous pouvez utiliser pour créer une solution préconfigurée.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Puis-je créer une solution préconfigurée si j’ai un abonnement du fournisseur de solutions Cloud (CSP) ?

Actuellement, vous ne pouvez pas créer de solution préconfigurée avec un abonnement de fournisseur de solutions Cloud (CSP). Vous pouvez toutefois créer en quelques minutes un [compte d’évaluation Azure gratuit][lnk-30daytrial], que vous pouvez utiliser pour créer une solution préconfigurée.

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment supprimer un client AAS ?

Consultez le billet de blog d’Eric Golpe, [Procédure pas à pas pour la suppression d’un client Azure AD][lnk-delete-aad-tennant].

### <a name="what-is-opc-ua"></a>Qu’est-ce que l’UA OPC ?

L’architecture unifiée (UA) OPC, sortie en 2008, est une norme d’interopérabilité orientée service et ne dépendant pas d’une plateforme qui est utilisée par divers appareils et systèmes industriels tels que des capteurs, PLC et PC du secteur. L’UA OPC inclut toutes les fonctionnalités des spécifications OPC classiques dans une seule infrastructure extensible avec sécurité intégrée. La fondation OPC Foundation est à l’origine de cette norme. La fondation [OPC Foundation][lnk-opc-foundation] est une organisation à but non lucratif dont les plus de 440 membres partagent l’objectif de fournir l’infrastructure, les spécifications, la technologie et les processus nécessaires pour faciliter l’interopérabilité fiable et sécurisée entre plusieurs fournisseurs et plateformes à l’aide de spécifications OPC.

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-preconfigured-solution"></a>Pourquoi le choix de Microsoft s’est-il porté sur l’UA OPC en matière de solution préconfigurée d’usine connectée ?

Microsoft a choisi l’UA OPC, car il s’agit d’une norme ouverte, non propriétaire, ne dépendant pas d’une plateforme, reconnue par le secteur et éprouvée. Elle est exigée pour les solutions d’architecture de référence Industrie 4.0 (RAMI4.0) assurant l’interopérabilité entre un large ensemble de processus de fabrication et les équipements. Microsoft constate une demande de la part des clients pour créer des solutions Industrie 4.0. La prise en charge de l’UA OPC permet de supprimer la barrière empêchant les clients d’atteindre leurs objectifs et leur apporte une valeur commerciale instantanée.

### <a name="next-steps"></a>Étapes suivantes

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-overview]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

[lnk-opc-foundation]: http://opcfoundation.org/
[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance

