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
ms.date: 02/15/2017
ms.author: corywink
translationtype: Human Translation
ms.sourcegitcommit: 623f502a92dc8eb152a9b01c4f6db0640ce57e54
ms.openlocfilehash: ab027e1f21e8c2c33829f833395a6872cb7bcdd8
ms.lasthandoff: 02/27/2017


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
Vous pouvez configurer 10 instances IoT Hub par abonnement. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 10 instances IoT Hub par abonnement, comme indiqué dans l’article concernant les [limites d’abonnement Azure][link-azuresublimits]. Par conséquent, étant donné que chaque solution préconfigurée approvisionne un nouvel IoT Hub, vous ne pouvez configurer que 10 solutions préconfigurées au maximum dans un abonnement. 

### <a name="how-many-documentdb-instances-can-i-provision-in-a-subscription"></a>Combien d’instances de DocumentDB puis-je configurer dans un abonnement ?
Cinquante. Vous pouvez créer un [ticket de support Azure][link-azuresupportticket] pour augmenter cette limite, mais par défaut, vous ne pouvez approvisionner que 50 instances de DocumentDB par abonnement. 

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

### <a name="how-do-i-delete-an-aad-tenant"></a>Comment supprimer un client AAS ?
Consultez le billet de blog d’Eric Golpe, [Procédure pas à pas pour la suppression d’un client Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Étapes suivantes
Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-overview]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

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
