<properties
   pageTitle="Gestion de vos applications Service Fabric dans Visual Studio"
   description="Vous pouvez gérer vos applications et services Microsoft Azure Service Fabric via Visual Studio."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/14/2015"
   ms.author="jesseb"/>

# Gestion de vos applications Service Fabric dans Visual Studio

Vous pouvez gérer vos applications et services Microsoft Azure Service Fabric via Visual Studio. Une fois que vous avez [configuré votre environnement de développement](../service-fabric-setup-your-development-environment), vous pouvez utiliser Visual Studio pour créer des applications Service Fabric, ajouter des services ou empaqueter, enregistrer et déployer des applications dans votre cluster de développement local.

Pour gérer votre application Service Fabric, dans l'Explorateur de solutions, cliquez avec le bouton droit sur votre projet d'application.

![Gérer votre application Service Fabric en cliquant avec le bouton droit sur le projet d'application][manageservicefabric]

## Déploiement de votre application Service Fabric

Le déploiement d'une application Service Fabric combine les étapes suivantes en une simple opération.

1. Création du package d'application
2. Téléchargement du package d'application dans le magasin d'images
3. Enregistrement du type d'application
4. Suppression des instances d'application en cours d'exécution
5. Création d'une instance d'application

Dans Visual Studio, pour effectuer le déploiement, cliquez avec le bouton droit sur votre application Service Fabric dans l'**Explorateur de solutions**, puis cliquez sur **Déployer**. Vous pouvez aussi appuyer sur **F5** pour déployer votre application et attacher le débogueur à toutes les instances de l'application.

Le déploiement peut être supprimé à l'aide de **Supprimer le déploiement**. Les étapes de déploiement ci-dessus seront annulées.

1. Supprimer toutes les instances d'application en cours d'exécution
2. Annuler l'enregistrement du type d'application
3. Télécharger le package d'application à partir du magasin d'images

## Ajout d'un service à votre application Service Fabric

Vous pouvez ajouter de nouveaux services Service Fabric à votre application pour étendre ses fonctionnalités. Pour garantir que le service est inclus dans votre package d'application, ajoutez-le via l'élément de menu **Nouveau service Service Fabric...**.

![Ajouter un nouveau service Service Fabric à votre application][newservice]

Sélectionnez un type de projet Service Fabric à ajouter à votre application et spécifiez un nom pour le service. Consultez [Choix d'une infrastructure pour votre service](service-fabric-choose-framework.md) pour vous aider à choisir le type de service à utiliser.

![Sélectionner un type de projet Service Fabric à ajouter à votre application][addserviceproject]

Le nouveau service est ajouté à votre solution et au package d'application existant. Les références du service et une instance de service par défaut sont ajoutés au manifeste d'application. Le service est créé et démarré la prochaine fois que vous déployez l'application.

![Le nouveau service est ajouté à votre manifeste d'application][newserviceapplicationmanifest]

## Empaquetage de votre application Service Fabric

Un package d'application doit être créé pour déployer l'application et ses services dans un cluster. Le package organise le manifeste d'application, le ou les manifestes de service et les autres fichiers nécessaires dans une disposition spécifique. Visual Studio configure et gère le package dans le dossier du projet d'application, dans le répertoire « pkg ». Cliquez sur **Package** pour créer ou mettre à jour le package d'application. Ce peut être utile si vous déployez l'application à l'aide de scripts Powershell personnalisés.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes

- [Modèle d'application Service Fabric](../service-fabric-application-model)
- [Déploiement d'applications Service Fabric](../service-fabric-deploy-remove-applications)
- [Débogage de votre application Service Fabric](../service-fabric-debugging-your-application)
- [Visualisation de votre cluster à l'aide de l'outil Service Fabric Explorer](../service-fabric-visualizing-your-cluster)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
 

<!---HONumber=July15_HO2-->