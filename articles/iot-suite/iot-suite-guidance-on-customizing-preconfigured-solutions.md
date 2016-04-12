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

En outre, Azure IoT fournit un [exemple de Kit de développement logiciel C](https://github.com/Azure/azure-iot-sdks/tree/master/c/serializer/samples/remote_monitoring) conçu pour fonctionner avec la solution préconfigurée de surveillance à distance.

### Création et utilisation de votre propre appareil (physique)

Les [Kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types d’appareils (langages et systèmes d’exploitation) à des solutions IoT.

## Configuration manuelle des rôles d’application

La procédure suivante décrit comment ajouter les rôles d’application **Admin** et **ReadOnly** à une solution préconfigurée. Notez que les solutions préconfigurées provisionnées à partir du site azureiotsuite.com incluent les rôles **Admin** et **ReadOnly**.

Les membres du rôle **ReadOnly** peuvent afficher le tableau de bord et la liste des appareils, mais ils ne sont pas autorisés à ajouter des appareils, à modifier les attributs de l’appareil ou à envoyer des commandes. Les membres du rôle **Admin** ont un accès complet à toutes les fonctionnalités de la solution.

1. Connectez-vous au [portail Azure Classic][lnk-classic-portal].

2. Sélectionnez **Active Directory**.

3. Cliquez sur le nom du client AAD que vous avez utilisé lorsque vous avez provisionné votre solution.

4. Cliquez sur **Applications**.

5. Cliquez sur le nom de l’application qui correspond au nom de votre solution préconfigurée. Si vous ne voyez pas votre application dans la liste, sélectionnez **Applications que ma société possède** dans le menu déroulant **Afficher** et cliquez sur la coche.

6.  En bas de la page, cliquez sur **Gérer le manifeste** et **Télécharger le manifeste**.

7. Cela permet de télécharger un fichier .json sur votre ordinateur local. Ouvrez ce fichier pour le modifier dans un éditeur de texte de votre choix.

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

10.  Au bas de la page du portail de gestion Azure, sélectionnez **Gérer le manifeste** puis **Télécharger le manifeste** pour télécharger le fichier .json que vous avez enregistré à l’étape précédente.

11. Vous avez maintenant ajouté les rôles **Admin** et **ReadOnly** à votre application.

12. Pour affecter un de ces rôles à un utilisateur de votre répertoire, consultez [Autorisations sur le site azureiotsuite.com][lnk-permissions].

## Commentaires

Vous avez une personnalisation que vous aimeriez voir couverte dans ce document ? Veuillez ajouter des suggestions de fonctionnalités à [User Voice](https://feedback.azure.com/forums/321918-azure-iot) ou commenter cet article ci-dessous.

## Étapes suivantes

Pour plus d’informations sur les appareils IoT, consultez le [site de développement Azure IoT](https://azure.microsoft.com/develop/iot/) pour y trouver des liens et de la documentation.

[Kit SDK d’appareils IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-permissions]: iot-suite-permissions.md
[lnk-classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0330_2016-->