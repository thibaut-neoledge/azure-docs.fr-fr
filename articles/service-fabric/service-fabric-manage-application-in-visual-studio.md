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

### Mode de débogage d’application

Lors du débogage local du service, il se peut que vous souhaitiez conserver l’application et les données existantes. Les outils Service Fabric de Visual Studio fournissent une propriété appelée **Mode de débogage d’application**, qui vérifie si **F5** doit désinstaller l’application ou la conserver après une session de débogage.

#### Pour définir la propriété Mode de débogage d’application

1. Dans le menu contextuel du projet d’application, cliquez sur **Propriétés** (ou appuyez sur la touche **F4**).
2. Dans la fenêtre **Propriétés**, configurez la propriété **Mode de débogage d’application**sur **Supprimer** ou **Mise à niveau automatique**.

![Définir la propriété Mode de débogage d’application][debugmodeproperty]

Le fait de définir cette valeur de propriété sur **Mise à niveau automatique** a pour effet de laisser l’application s’exécuter sur le cluster local. Le **F5** suivant traite le déploiement comme une mise à niveau, en utilisant le mode automatique non contrôlé pour mettre rapidement à niveau l’application vers une version plus récente avec une chaîne de date ajoutée. Le processus de mise à niveau conserve les données que vous avez saisies au cours de la précédente session de débogage.

![Exemple d’une nouvelle version d’application à laquelle date1 est ajouté][preservedate]

Les données sont préservées à l’aide de la fonctionnalité de mise à niveau de la plateforme de Service Fabric. Pour plus d’informations sur la mise à niveau d’une application, reportez-vous à [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md).

**Remarque :** cette propriété n’existe pas avant la version 1.1 des outils Service Fabric pour Visual Studio. Avant la version 1.1, veuillez utiliser la propriété **Conserver les données au démarrage** pour obtenir le même comportement.
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
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0706_2016-->