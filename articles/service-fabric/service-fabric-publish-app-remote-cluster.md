<properties
    pageTitle="Publier une application sur un cluster distant à l’aide de Visual Studio | Microsoft Azure"
    description="Découvrez les étapes nécessaires pour publier une application sur un cluster Service Fabric distant à l’aide de Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawa"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="10/28/2015"
    ms.author="cawa" />

# Publier une application sur un cluster distant à l’aide de Visual Studio

Les outils Service Fabric de Visual Studio offrent un moyen facile et réutilisable de publier une application sur un cluster Service Fabric. Il est aussi possible de créer des scripts pour cette publication. Ceci peut se faire à l’aide d’un script de déploiement PowerShell avec des profils de publication. Les profils de publication sont des fichiers au format XML qui stockent les principales informations de publication. La boîte de dialogue **Publier une application Service Fabric** a également été ajoutée pour permettre aux utilisateurs de publier facilement une application sur un cluster local ou distant. Toutes les modifications apportées via cette boîte de dialogue sont capturées dans les profils de publication. Cela vous permet de modifier manuellement les paramètres de publication dans un processus d’automatisation ultérieurement.

## Artefacts nécessaires pour publier une application sur un cluster Service Fabric

### Deploy-FabricApplication.ps1

Il s’agit d’un script PowerShell qui utilise un chemin d’accès au profil de publication comme paramètre pour publier des applications Service Fabric.

### Profils de publication

Un dossier dans le projet d’application Service Fabric appelé **PublishProfiles** contient les fichiers **Cloud.XML** et **Local.XML**. Il s’agit de « profils de publication » qui stockent les informations essentielles à la publication d’une application, par exemple les paramètres de connexion au cluster Service Fabric, le chemin d’accès au fichier de paramètres de l’application, les paramètres de mise à niveau.

### Fichiers de paramètres de l’application

Un dossier du projet d’application Service Fabric appelé **ApplicationParameters** contient les fichiers XML pour les valeurs de paramètre du manifeste d’application spécifié par l’utilisateur. Les fichiers manifeste de l’application peuvent être paramétrés de sorte que vous pouvez utiliser des valeurs différentes pour les paramètres de déploiement.

Par exemple, vous pouvez modifier le nombre de partitions pour les différents environnements de chaque déploiement. Lorsque vous créez un projet, les paramètres du manifeste de l’application, comme **TargetReplicaSetSize** et **PartitionCount**, sont convertis en paramètres. La valeur par défaut de ces paramètres est spécifiée dans la section **Parameters** du fichier manifeste de l’application (ApplicationManifest.XML, dans le projet d’application Service Fabric). Toutes les valeurs que vous ajoutez au fichier de paramètres remplacent les valeurs par défaut dans le fichier manifeste de l’application.

>[AZURE.NOTE]Pour les services Actor, vous devez d’abord créer le projet pour générer les paramètres dans le fichier manifeste.

Voici un exemple de fichier manifeste de l’application :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application2Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Actor1ActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="Actor1ActorService_MinReplicaSetSize" DefaultValue="2" />
      <Parameter Name="Actor1ActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Actor1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Actor1ActorService">
         <StatefulService ServiceTypeName="Actor1ActorServiceType" TargetReplicaSetSize="[Actor1ActorService_TargetReplicaSetSize]" MinReplicaSetSize="[Actor1ActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[Actor1ActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## Publier une application sur un cluster Service Fabric à l’aide de la boîte de dialogue Publier une application Service Fabric

Les étapes suivantes montrent comment publier une application à l’aide de la boîte de dialogue **Publier une application Service Fabric** dans les outils Service Fabric de Visual Studio.

1. Dans le menu contextuel du projet d’application Service Fabric, choisissez **Publier...** pour afficher la boîte de dialogue **Publier une application Service Fabric**.

    ![][0]

    Le fichier sélectionné dans la liste déroulante **Profil cible** est l’emplacement où tous les paramètres sont enregistrés, à l’exception des **versions du manifeste**. Vous pouvez soit réutiliser un profil existant, soit en créer un en choisissant **<Gérer les profils...>** la zone de liste déroulante **Profil cible**. Lorsque vous choisissez un profil de publication, son contenu s’affiche dans les champs correspondants de la boîte de dialogue. Pour enregistrer vos modifications à tout moment, cliquez sur le lien **Enregistrer le profil**.

1. La section **Point de terminaison de la connexion** vous permet de spécifier un point de terminaison de publication local ou distant sur le cluster Service Fabric. Pour ajouter ou modifier le point de terminaison de la connexion, cliquez sur le bouton **Sélectionner...**. La boîte de dialogue **Sélectionner le cluster Service Fabric** affiche les points de terminaison de connexion disponibles sur le cluster Service Fabric et sur lesquels vous pouvez publier. (Si vous n’êtes pas déjà connecté à un abonnement Azure, vous êtes invité à le faire.) Choisissez un point de terminaison de connexion.

    ![][1]

    Une fois que vous avez choisi un point de terminaison, Visual Studio valide la connexion au cluster Service Fabric sélectionné. Si le cluster n’est pas sécurisé, Visual Studio peut vous y connecter immédiatement. Toutefois, si le cluster est sécurisé, vous devez installer un certificat sur votre ordinateur local avant de pouvoir continuer. Consultez la page [Configuration de connexions sécurisées](service-fabric-visualstudio-configure-secure-connections.md) pour plus d’informations. Lorsque vous avez terminé, cliquez sur le bouton **OK**. Le cluster sélectionné s’affiche dans la boîte de dialogue **Publier une application Service Fabric**.

1. La zone de liste déroulante **Fichier de paramètres d’application** vous permet d’accéder à un fichier de paramètres d’application. Ce dernier contient les valeurs spécifiées par l’utilisateur pour les paramètres du fichier manifeste de l’application. Pour ajouter ou modifier un paramètre, cliquez sur le bouton **Modifier**. Entrez ou modifiez la valeur du paramètre dans la grille **Paramètres**. Quand vous avez terminé, cliquez sur le bouton **Enregistrer**.

    ![][2]

1. La case à cocher **Mettre à niveau l’application** vous permet de spécifier si cette action de publication est une mise à niveau. Les actions de mise à niveau sont différentes des actions de publication normales. Consultez la page [Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md) pour obtenir la liste des différences. Pour configurer les paramètres de mise à niveau, cliquez sur le lien **Configurer les paramètres de mise à niveau**. L’éditeur de paramètres de mise à niveau s’affiche. Consultez la page [Configuration de la mise à niveau d’une application Service Fabric](service-fabric-visualstudio-configure-upgrade.md) pour en savoir plus sur les paramètres de mise à niveau.

1. Cliquez sur le bouton **Versions du manifeste...** pour afficher la boîte de dialogue **Modifier les versions**. Vous devez mettre à jour les applications et les versions de service pour que la mise à niveau puisse se dérouler. Consultez la page [Didacticiel sur la mise à niveau d’une application Service Fabric](service-fabric-application-upgrade-tutorial.md) pour en savoir plus la façon dont les versions de manifeste de l’application et du service affectent la mise à niveau.

    ![][3]

    Si la version de l’application et la version du service utilisent le contrôle de version sémantique comme 1.0.0 ou des valeurs numériques au format 1.0.0.0, sélectionnez l’option **Mettre à jour automatiquement la version de l’application et les versions de service**. Lorsque vous choisissez cette option, les numéros de version du service et de l’application sont automatiquement mis à jour chaque fois que la version du code, de la configuration ou du package de données est mise à jour. Si vous préférez modifier les versions manuellement, désactivez la case à cocher pour désactiver cette fonctionnalité.

    >[AZURE.NOTE]Pour que toutes les entrées de package s’affichent pour un projet Actor, créez d’abord le projet pour générer les entrées dans les fichiers manifeste de service.

1. Lorsque vous avez fini de spécifier tous les paramètres nécessaires, cliquez sur le bouton **Publier** pour publier votre application sur le cluster Service Fabric sélectionné. Les paramètres que vous avez spécifiés sont appliqués au processus de publication.

## Étapes suivantes

Pour savoir comment automatiser le processus de publication dans un environnement d’intégration continue, consultez [Service Fabric - Configurer l’intégration continue](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png

<!---HONumber=Nov15_HO4-->