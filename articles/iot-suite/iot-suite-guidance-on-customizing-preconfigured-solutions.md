---
title: "Personnalisation des solutions préconfigurées | Microsoft Docs"
description: "Fournit des conseils sur la personnalisation des solutions préconfigurées Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: .net
author: aguilaaj
manager: timlt
editor: 
ms.assetid: 4653ae53-4110-4a10-bd6c-7dc034c293a8
ms.service: iot-suite
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 45fd461defc00c5dc018496b85b8bf85614f03dd
ms.openlocfilehash: 0037b9e28b20c9a85f810cba45aa5b4cbcf6ab6b


---
# <a name="customize-a-preconfigured-solution"></a>Personnaliser une solution préconfigurée
Les solutions préconfigurées fournies avec Azure IoT Suite présentent les services de la suite qui collaborent pour fournir une solution de bout en bout. À partir de ce point de départ, il existe différents endroits où vous pouvez étendre et personnaliser la solution pour des scénarios spécifiques. Les sections suivantes décrivent ces points de personnalisation courants.

## <a name="finding-the-source-code"></a>Recherche du code source
Le code source pour les solutions préconfigurées est disponible sur GitHub dans les dépôts suivants :

* Surveillance à distance : [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
* Maintenance prédictive : [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Le code source pour les solutions préconfigurées est fourni dans le but d’illustrer les modèles et pratiques utilisés pour implémenter la fonctionnalité de bout en bout d’une solution IoT utilisant Azure IoT Suite. Pour plus d’informations sur la création et le déploiement des solutions, consultez les dépôts GitHub.

## <a name="changing-the-preconfigured-rules"></a>Modification des règles préconfigurées
La solution de surveillance à distance inclut trois tâches [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) destinées à implémenter la logique d’informations, de règles et de télémétrie de l’appareil affichée pour la solution.

Les trois tâches Stream Analytics et leur syntaxe sont décrites en détail dans [Procédure pas à pas pour la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md). 

Vous pouvez modifier ces tâches directement pour en modifier la logique, ou ajouter une logique spécifique à votre scénario. Pour rechercher les tâches Stream Analytics, procédez comme suit :

1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez au groupe de ressources portant le même nom que votre solution IoT. 
3. Sélectionnez la tâche Azure Stream Analytics à modifier. 
4. Arrêtez la tâche en sélectionnant **Arrêter**dans le jeu de commandes. 
5. Modifiez les entrées, la requête et les sorties.
   
    Une simple modification consiste à changer la requête pour la tâche **Règles** afin d’utiliser le signe **« < »** au lieu du signe **« > »**. Le portail de solution affiche toujours **« > »** quand vous modifiez une règle, mais le comportement est inversé en raison de la modification de la tâche sous-jacente.
6. Démarrage du travail

> [!NOTE]
> Le tableau de bord de surveillance à distance dépend de données spécifiques. Ainsi, modifier les tâches peut provoquer l’échec du tableau de bord.
> 
> 

## <a name="adding-your-own-rules"></a>Ajout de vos propres règles
En plus de modifier les tâches Azure Stream Analytics préconfigurées, vous pouvez utiliser le portail Azure pour ajouter de nouvelles tâches ou ajouter de nouvelles requêtes aux tâches existantes.

## <a name="customizing-devices"></a>Personnalisation des appareils
L’une des activités d’extension les plus courantes consiste à utiliser des appareils spécifiques à votre scénario. Il existe plusieurs méthodes pour travailler avec des appareils, Notamment, la modification d’un appareil simulé pour qu’il corresponde à votre scénario ou l’utilisation du [Kit SDK d’appareils IoT][IoT Device SDK] pour connecter votre appareil physique à la solution.

Pour obtenir un guide étape par étape sur l’ajout d’appareils à la solution préconfigurée de surveillance à distance, consultez les rubriques [IoT Suite - Connexion d’appareils](iot-suite-connecting-devices.md) et [Kit de développement logiciel (SDK) C - exemple de surveillance à distance](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer/samples/remote_monitoring). Ces appareils sont conçus pour fonctionner avec la solution préconfigurée de surveillance à distance.

### <a name="creating-your-own-simulated-device"></a>Création de votre propre appareil simulé
Le code source de la solution de surveillance à distance (référencé ci-dessus) contient un simulateur .NET. C’est lui qui est configuré dans le cadre de la solution et qui peut être modifié pour envoyer des données de télémétrie ou des métadonnées différentes, ou pour répondre à des commandes différentes.

Le simulateur préconfiguré dans la solution préconfigurée de surveillance à distance est un appareil de refroidissement qui émet une télémétrie de température et d’humidité. Vous pouvez modifier le simulateur dans le projet [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) après avoir dupliqué le référentiel GitHub.

### <a name="available-locations-for-simulated-devices"></a>Emplacements disponibles pour les appareils simulés
L’ensemble des emplacements par défaut se trouve à Seattle/Redmond, Washington, États-Unis. Vous pouvez modifier ces emplacements dans [SampleDeviceFactory.cs][lnk-sample-device-factory].

### <a name="building-and-using-your-own-physical-device"></a>Création et utilisation de votre propre appareil (physique)
Les [Kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types d’appareils (langages et systèmes d’exploitation) à des solutions IoT.

## <a name="modifying-dashboard-limits"></a>Modification des limites de tableau de bord
### <a name="number-of-devices-displayed-in-dashboard-dropdown"></a>Nombre d’appareils affichés dans la liste déroulante du tableau de bord
Par défaut, la valeur est 200. Vous pouvez modifier cette valeur dans [DashboardController.cs][lnk-dashboard-controller].

### <a name="number-of-pins-to-display-in-bing-map-control"></a>Nombre d’épingles à afficher dans le contrôle de carte Bing
Par défaut, la valeur est 200. Vous pouvez modifier cette valeur dans [TelemetryApiController.cs][lnk-telemetry-api-controller-01].

### <a name="time-period-of-telemetry-graph"></a>Période de temps du graphique de télémétrie
La valeur par défaut est 10 minutes. Vous pouvez modifier cette valeur dans [TelemetryApiController.cs][lnk-telemetry-api-controller-02].

## <a name="manually-setting-up-application-roles"></a>Configuration manuelle des rôles d’application
La procédure suivante décrit comment ajouter les rôles d’application **Admin** et **ReadOnly** à une solution préconfigurée. Les solutions préconfigurées provisionnées à partir du site azureiotsuite.com incluent les rôles **Admin** et **ReadOnly**.

Les membres du rôle **ReadOnly** peuvent afficher le tableau de bord et la liste des appareils, mais ils ne sont pas autorisés à ajouter des appareils, modifier les attributs de l’appareil ou envoyer des commandes.  Les membres du rôle **Admin** ont un accès complet à toutes les fonctionnalités de la solution.

1. Connectez-vous au [Portail Azure Classic][lnk-classic-portal].
2. Sélectionnez **Active Directory**.
3. Cliquez sur le nom du client AAD que vous avez utilisé lorsque vous avez provisionné votre solution.
4. Cliquez sur **Applications**.
5. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfigurée. Si vous ne voyez pas votre application dans la liste, sélectionnez **Applications que ma société possède** dans le menu déroulant **Afficher** et cliquez sur la coche.
6. En bas de la page, cliquez sur **Gérer le manifeste** et **Télécharger le manifeste**.
7. Cela permet de télécharger un fichier .json sur votre ordinateur local.  Ouvrez ce fichier pour le modifier dans un éditeur de texte de votre choix.
8. Sur la troisième ligne du fichier .json, vous trouverez :
   
   ```
   "appRoles" : [],
   ```
   Remplacez ce contenu par ce qui suit :
   
   ```
   "appRoles": [
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Administrator access to the application",
   "displayName": "Admin",
   "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
   "isEnabled": true,
   "value": "Admin"
   },
   {
   "allowedMemberTypes": [
   "User"
   ],
   "description": "Read only access to device information",
   "displayName": "Read Only",
   "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
   "isEnabled": true,
   "value": "ReadOnly"
   } ],
   ```
9. Enregistrez le fichier .json mis à jour (vous pouvez remplacer le fichier existant).
10. Au bas de la page du portail de gestion Azure, sélectionnez **Gérer le manifeste** puis **Télécharger le manifeste** pour télécharger le fichier .json que vous avez enregistré à l’étape précédente.
11. Vous avez maintenant ajouté les rôles **Admin** et **ReadOnly** à votre application.
12. Pour affecter un de ces rôles à un utilisateur de votre répertoire, consultez [Autorisations sur le site azureiotsuite.com][lnk-permissions].

## <a name="feedback"></a>Commentaires
Vous avez une personnalisation que vous aimeriez voir couverte dans ce document ? Veuillez ajouter des suggestions de fonctionnalités à [User Voice](https://feedback.azure.com/forums/321918-azure-iot)ou commenter cet article ci-dessous. 

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les options permettant de personnaliser les solutions préconfigurées, consultez :

* [Connecter Logic App à la solution préconfigurée de surveillance à distance Azure IoT Suite][lnk-logicapp]
* [Utilisation de la télémétrie dynamique avec la solution préconfigurée de surveillance à distance][lnk-dynamic]
* [Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance][lnk-devinfo]

[lnk-logicapp]: iot-suite-logic-apps-tutorial.md
[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[IoT Device SDK]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-dashboard-controller]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/Controllers/DashboardController.cs#L27
[lnk-telemetry-api-controller-01]: https://github.com/Azure/azure-iot-remote-monitoring/blob/3fd43b8a9f7e0f2774d73f3569439063705cebe4/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L27
[lnk-telemetry-api-controller-02]: https://github.com/Azure/azure-iot-remote-monitoring/blob/e7003339f73e21d3930f71ceba1e74fb5c0d9ea0/DeviceAdministration/Web/WebApiControllers/TelemetryApiController.cs#L25 
[lnk-sample-device-factory]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Common/Factory/SampleDeviceFactory.cs#L40
[lnk-classic-portal]: https://manage.windowsazure.com



<!--HONumber=Dec16_HO3-->


