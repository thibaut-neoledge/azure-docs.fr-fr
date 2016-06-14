<properties
   pageTitle="Gestion de vos applications dans Visual Studio | Microsoft Azure"
   description="Utilisez Visual Studio pour créer, développer, packager, déployer et déboguer vos applications et services Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="seanmck"/>

# Utilisation de Visual Studio pour simplifier l'écriture et la gestion des applications Service Fabric

Vous pouvez gérer vos applications et services Azure Service Fabric via Visual Studio. Après avoir [configuré votre environnement de développement](service-fabric-get-started.md), vous pouvez utiliser Visual Studio pour créer des applications Service Fabric, ajouter des services ou empaqueter, enregistrer et déployer des applications dans votre cluster de développement local.

Pour gérer une application, dans l’Explorateur de solutions, cliquez avec le bouton droit sur votre projet d’application.

![Gérez votre application Service Fabric en cliquant avec le bouton droit sur le projet d’application][manageservicefabric]

## Déploiement de votre application Service Fabric

Le déploiement d’une application regroupe les étapes suivantes en une simple opération :

1. Création du package d'application
2. Téléchargement du package d'application dans le magasin d'images
3. Enregistrement du type d'application
4. Suppression des instances d'application en cours d'exécution
5. Création d'une instance d'application

Dans Visual Studio, vous pouvez aussi appuyer sur **F5** pour déployer votre application et attacher le débogueur à toutes les instances de l'application. Vous pouvez utiliser **Ctrl + F5** pour déployer une application sans débogage ou la publier sur un cluster local ou distant à l’aide du profil de publication. Pour plus d’informations, reportez-vous à la section [Publication d’une application dans un cluster à distance avec Visual Studio](service-fabric-publish-app-remote-cluster.md).

### Conservation des données entre les séries de tests

Souvent, vous testez les services localement en ajoutant l'entrée de données de test, en modifiant certains blocs de code, puis en effectuant un nouveau débogage local. Les outils Service Fabric de Visual Studio sont dotés d’une propriété pratique appelée **Préserver les données au démarrage** qui permet de conserver les données saisies au cours de la session précédente et de les réutiliser.

#### Activation de la propriété Preserve Data on Start

1. Dans le menu contextuel du projet d’application, cliquez sur **Propriétés** (ou appuyez sur la touche **F4**).
1. Dans la fenêtre **Propriétés**, définissez la propriété **Preserve Data on Start** sur **Yes**.

	![Configuration de la propriété Preserve Data on Start][preservedata]

Lorsque vous exécutez votre application à nouveau, le script de déploiement traite le déploiement comme une mise à niveau, en utilisant le mode automatique non contrôlé pour mettre rapidement à niveau l’application vers une version plus récente avec une chaîne de date ajoutée. Le processus de mise à niveau conserve les données que vous avez saisies au cours de la précédente session de débogage.

![Exemple d’une nouvelle version de l'application avec une date ajoutée][preservedate]

Les données sont préservées à l’aide de la fonctionnalité de mise à niveau de la plateforme de Service Fabric. Pour plus d’informations sur la mise à niveau d’une application, reportez-vous à [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md).

## Ajouter un service à votre application Service Fabric

Vous pouvez ajouter de nouveaux services Service Fabric à votre application pour étendre ses fonctionnalités. Pour garantir que le service est inclus dans votre package d’application, ajoutez-le via l’élément de menu **Nouveau service Service Fabric…**.

![Ajoutez un nouveau service Service Fabric à votre application][newservice]

Sélectionnez un type de projet Service Fabric à ajouter à votre application et spécifiez un nom pour le service. Consultez [Choix d’une infrastructure pour votre service](service-fabric-choose-framework.md) pour vous aider à choisir le type de service à utiliser.

![Sélectionner un type de projet Service Fabric à ajouter à votre application][addserviceproject]

Le nouveau service est ajouté à votre solution et au package d'application existant. Les références du service et une instance de service par défaut sont ajoutés au manifeste d'application. Le service est créé et démarré la prochaine fois que vous déployez l'application.

![Le nouveau service est ajouté à votre manifeste d'application][newserviceapplicationmanifest]

## Empaquetage de votre application Service Fabric

Pour déployer l’application et ses services dans un cluster, vous devez créer un package d’application. Le package organise le manifeste d'application, le ou les manifestes de service et les autres fichiers nécessaires dans une disposition spécifique. Visual Studio configure et gère le package dans le dossier du projet d'application, dans le répertoire « pkg ». Cliquez sur **Package** dans le menu contextuel **Application** pour créer ou mettre à jour le package d'application. Cela peut être utile si vous déployez l’application à l’aide de scripts Powershell personnalisés.

## Supprimer une application

Vous pouvez supprimer les privilèges d’accès d’un type d’application à partir de votre cluster local à l’aide de l’explorateur Service Fabric. L’explorateur de cluster est accessible à partir du point de terminaison de passerelle HTTP du cluster (généralement 19080 ou 19007), par exemple, http://localhost:19080/Explorer. Les étapes de déploiement décrites ci-dessus seront annulées :

1. Supprimer toutes les instances d'application en cours d'exécution
2. Annuler l'enregistrement du type d'application

![Supprimer une application](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

- [Modèle d'application Service Fabric](service-fabric-application-model.md)
- [Déploiement d'applications Service Fabric](service-fabric-deploy-remove-applications.md)
- [Gestion des paramètres d’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)
- [Débogage de votre application Service Fabric](service-fabric-debugging-your-application.md)
- [Visualisation de votre cluster à l’aide de l’explorateur Service Fabric](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png

<!---HONumber=AcomDC_0601_2016-->