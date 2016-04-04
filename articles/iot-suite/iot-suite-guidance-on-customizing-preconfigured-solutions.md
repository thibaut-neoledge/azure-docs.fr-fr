<properties
	pageTitle="Personnalisation des solutions préconfigurées | Microsoft Azure"
	description="Fournit des conseils sur la personnalisation des solutions préconfigurées Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# Personnalisation des solutions préconfigurées

Les solutions préconfigurées fournies avec Azure IoT Suite présentent les services de la suite qui collaborent pour fournir une solution de bout en bout. À partir de ce point de départ, il existe différents endroits où vous pouvez étendre et personnaliser la solution pour des scénarios spécifiques. Les sections suivantes décrivent ces points de personnalisation courants.

## Recherche du code source

Le code source pour les solutions préconfigurées est disponible sur GitHub dans les dépôts suivants :

- Surveillance à distance : [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Maintenance prédictive : [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Le code source pour les solutions préconfigurées est fourni dans le but d’illustrer les modèles et pratiques utilisés pour implémenter la fonctionnalité de bout en bout d’une solution IoT utilisant Azure IoT Suite. Pour plus d’informations sur la création et le déploiement des solutions, consultez les dépôts GitHub.

## Gestion des autorisations dans une solution préconfigurée
Le portail de solution relatif à chaque solution préconfigurée est créé en tant que nouvelle application Azure Active Directory. Pour gérer les autorisations pour votre portail de solution (application AAD), procédez comme suit :

1. Ouvrez le [portail Azure Classic.](https://manage.windowsazure.com)
2. Accédez à l’application AAD en sélectionnant **Applications que ma société possède**, puis en cliquant sur la case à cocher.
3. Accédez à **Utilisateurs**, puis assignez des membres de votre locataire Azure Active Directory à un rôle. 

Par défaut, l’application est configurée avec les rôles **Administrateur**, **En lecture seule** et **Implicite en lecture seule**. Le rôle **Implicite en lecture seule** est octroyé aux utilisateurs qui sont membres du locataire Azure Active Directory, mais n’ont pas de rôle assigné. Vous pouvez modifier [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) après avoir dupliqué le dépôt GitHub, puis redéployer votre solution.

## Modification des règles préconfigurées

La solution de surveillance à distance inclut trois tâches [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) pour implémenter la logique d’informations, de règles et de télémétrie de l’appareil affichée pour la solution.

Les trois tâches Stream Analytics et leur syntaxe sont décrites en détail dans [Procédure pas à pas pour la solution préconfigurée de surveillance à distance](iot-suite-remote-monitoring-sample-walkthrough.md).

Vous pouvez modifier ces tâches directement pour en modifier la logique, ou ajouter une logique spécifique à votre scénario. Pour rechercher les tâches Stream Analytics, procédez comme suit :
 
1. Accédez au [portail Azure](https://portal.azure.com).
2. Accédez au groupe de ressources portant le même nom que votre solution IoT. 
3. Sélectionnez la tâche Azure Stream Analytics à modifier. 
4. Arrêtez la tâche en sélectionnant **Arrêter** dans le jeu de commandes. 
5. Modifiez les entrées, la requête et les sorties.

    Une simple modification consiste à changer la requête pour la tâche **Règles** afin d’utiliser le signe **« < »** au lieu du signe **« > »**. Le portail de solution affiche toujours **« > »** quand vous modifiez une règle, mais vous remarquerez que le comportement est inversé en raison de la modification de la tâche sous-jacente.

6. Démarrage du travail

> [AZURE.NOTE] Le tableau de bord de surveillance à distance dépend de données spécifiques. Ainsi, modifier les tâches peut provoquer l’échec du tableau de bord.

## Ajout de vos propres règles

En plus de modifier les tâches Azure Stream Analytics préconfigurées, vous pouvez utiliser le portail Azure pour ajouter de nouvelles tâches ou ajouter de nouvelles requêtes aux tâches existantes.

## Personnalisation des appareils

L’une des activités d’extension les plus courantes consiste à utiliser des appareils spécifiques à votre scénario. Il existe plusieurs méthodes pour travailler avec des appareils, comme la modification d’un appareil simulé pour qu’il corresponde à votre scénario ou l’utilisation du [Kit SDK d’appareils IoT][] pour connecter votre appareil physique à la solution.

Pour obtenir un guide étape par étape sur l’ajout d’appareils à la solution préconfigurée de surveillance à distance, consultez le document suivant : [IoT Suite - Connexion d’appareils](iot-suite-connecting-devices.md)

### Création de votre propre appareil simulé

Le code source de la solution de surveillance à distance (référencé ci-dessus) contient un simulateur .NET. C’est lui qui est configuré dans le cadre de la solution et qui peut être modifié pour envoyer des données de télémétrie ou des métadonnées différentes, ou pour répondre à des commandes différentes.

Le simulateur préconfiguré dans la solution préconfigurée de surveillance à distance est un appareil de refroidissement qui émet une télémétrie de température et d’humidité. Vous pouvez modifier le simulateur dans le projet [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) après avoir dupliqué le dépôt GitHub.

En outre, Azure IoT fournit un [exemple de Kit de développement logiciel C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) conçu pour fonctionner avec la solution préconfigurée de surveillance à distance.

### Création et utilisation de votre propre appareil (physique)

Les [Kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types d’appareils (langages et systèmes d’exploitation) à des solutions IoT.

## Commentaires

Vous avez une personnalisation que vous aimeriez voir couverte dans ce document ? Veuillez ajouter des suggestions de fonctionnalités à [User Voice](https://feedback.azure.com/forums/321918-azure-iot) ou commenter cet article ci-dessous.

## Étapes suivantes

Pour plus d’informations sur les appareils IoT, consultez le [site de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour y trouver des liens et de la documentation.

[Kit SDK d’appareils IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0323_2016-->