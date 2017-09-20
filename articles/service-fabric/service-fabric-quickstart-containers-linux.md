---
title: "Créer une application de conteneur Microsoft Azure Service Fabric sur Linux | Microsoft Docs"
description: "Créez votre première application de conteneur Linux sur Microsoft Azure Service Fabric.  Concevez une image Docker avec votre application, envoyez l’image vers un registre de conteneurs, créez et déployez une application de conteneur Service Fabric."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/05/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: 78306672e812745fd1902ae264c2adea196ab721
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---

# <a name="deploy-a-service-fabric-linux-container-application-on-azure"></a>Déployer une application de conteneur Linux Service Fabric sur Azure
Azure Service Fabric est une plateforme de systèmes distribués pour le déploiement et la gestion de microservices et conteneurs extensibles et fiables. 

L’exécution d’une application existante dans un conteneur Linux sur un cluster Service Fabric ne nécessite aucune modification de votre application. Ce guide de démarrage rapide montre comment déployer une image de conteneur Docker préconstruite dans une application Service Fabric. Une fois terminé, vous disposez d’un conteneur nginx en fonction.  Ce guide de démarrage rapide décrit la procédure de déploiement d’un conteneur Linux. Consultez [ce guide de démarrage rapide](service-fabric-quickstart-containers.md) si vous souhaitez déployer un conteneur Windows.

![Nginx][nginx]

Dans ce guide de démarrage rapide, vous apprenez à :
> [!div class="checklist"]
> * Placer un conteneur d’images Docker dans un package
> * Configurer la communication
> * Créer et placer l’application Service Fabric dans un package
> * Déployer l’application de conteneur dans Azure

## <a name="prerequisites"></a>Composants requis
Installer le [Kit de développement logiciel (SDK) Service Fabric, la CLI Service Fabric, et les générateurs de modèles Yeoman Service Fabric](service-fabric-get-started-linux.md).
  
## <a name="package-a-docker-image-container-with-yeoman"></a>Placer un conteneur d’images Docker avec Yeoman dans un package
Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre application et l’ajout d’une image de conteneur. 

Pour créer une application de conteneur Service Fabric, ouvrez une fenêtre de terminal et exécutez `yo azuresfcontainer`.  

Nommez votre application « MyFirstContainer » et le service d’application « MyContainerService ».

Indiquer le nom d’image conteneur « nginx:latest » (l’[image conteneur nginx](https://hub.docker.com/r/_/nginx/) sur Docker Hub). 

Cette image dispose d’un point d’entrée de charge de travail défini. Vous devez donc spécifier clairement les commandes d’entrée. 

Spécifiez un nombre d’instances de « 1 ».

![Générateur Yeoman Service Fabric pour les conteneurs][sf-yeoman]

## <a name="configure-communication-and-container-port-to-host-port-mapping"></a>Configurer la communication et le mappage des ports de conteneur aux ports hôtes
Configurez un point de terminaison HTTP pour que les clients puissent communiquer avec votre service.  Ouvrez le fichier *./MyFirstContainer/MyContainerServicePkg/ServiceManifest.xml* et déclarez une ressource de point de terminaison dans l’élément **ServiceManifest**.  Ajoutez le protocole, le port et le nom. Dans ce guide de démarrage rapide, le service écoute le port 80 : 

```xml
<Resources>
  <Endpoints>
    <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
    <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
  </Endpoints>
</Resources>

```
Fournir `UriScheme` enregistre automatiquement le point de terminaison du conteneur avec le service Service Fabric Naming pour la découverte. Un exemple de fichier ServiceManifest.xml complet est fourni à la fin de cet article. 

Mappez un port de conteneur à un service `Endpoint` à l’aide d’une stratégie `PortBinding` dans `ContainerHostPolicies` du fichier ApplicationManifest.xml.  Dans ce guide de démarrage rapide, `ContainerPort` est 80 (le conteneur expose le port 80) et `EndpointRef` est « myserviceTypeEndpoint » (le point de terminaison défini précédemment dans le manifeste de service).  Les demandes entrantes pour le service sur le port 80 sont mappées au port 80 dans le conteneur.  

```xml
<Policies>
  <ContainerHostPolicies CodePackageRef="Code">
    <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
  </ContainerHostPolicies>
</Policies>
```

## <a name="build-and-package-the-service-fabric-application"></a>Créer et placer l’application Service Fabric dans un package
Les modèles Yeoman Service Fabric incluent un script de build pour [Gradle](https://gradle.org/), que vous pouvez utiliser pour générer l’application à partir du terminal. Enregistrez toutes vos modifications.  Pour générer et placer l’application dans un package, exécutez ce qui suit :

```bash
cd MyFirstContainer
gradle
```
## <a name="create-a-cluster"></a>Créer un cluster
Pour déployer l’application sur un cluster dans Azure, vous pouvez choisir de créer votre propre cluster ou d’utiliser un cluster tiers.

Les clusters tiers sont des clusters Service Fabric gratuits et limités dans le temps, hébergés sur Azure et gérés par l’équipe Service Fabric, où chacun peut déployer des applications et découvrir la plateforme. Pour obtenir l’accès à un cluster tiers, [suivez ces instructions](http://aka.ms/tryservicefabric).  

Pour plus d’informations sur la création de votre propre cluster, voir [Créer votre premier cluster Service Fabric sur Azure](service-fabric-get-started-azure-cluster.md).

Notez le point de terminaison de connexion que vous utilisez à l’étape suivante.

## <a name="deploy-the-application-to-azure"></a>Déploiement de l'application dans Azure
Une fois que l’application est générée, vous pouvez la déployer vers le cluster Azure à l’aide de la CLI Service Fabric.

Connectez-vous au cluster Service Fabric dans Azure.

```bash
sfctl cluster select --endpoint http://lnxt10vkfz6.westus.cloudapp.azure.com:19080
```

Utilisez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

```bash
./install.sh
```

Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://lnxt10vkfz6.westus.cloudapp.azure.com:19080/Explorer. Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.

![Service Fabric Explorer][sfx]

Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web qui pointe vers l’adresse IP renvoyée sur le port 80, par exemple « lnxt10vkfz6.westus.cloudapp.azure.com:80 ». La page d’accueil nginx devrait s’afficher dans le navigateur.

![Nginx][nginx]

## <a name="clean-up"></a>Nettoyer
Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance de l’application du cluster et annuler l’inscription du type d’application.

```bash
./uninstall.sh
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemples complets de manifestes d’application et de service Service Fabric
Voici les manifestes d’application et de service complets utilisés dans ce guide de démarrage rapide.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyContainerServicePkg" Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" >

   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="MyContainerServiceType" UseImplicitHost="true">
   </StatelessServiceType>
   </ServiceTypes>
   
   <CodePackage Name="code" Version="1.0.0">
      <EntryPoint>
         <ContainerHost>
            <ImageName>nginx:latest</ImageName>
            <Commands></Commands>
         </ContainerHost>
      </EntryPoint>
      <EnvironmentVariables> 
      </EnvironmentVariables> 
   </CodePackage>
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myserviceTypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
 </ServiceManifest>

```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest  ApplicationTypeName="MyFirstContainerType" ApplicationTypeVersion="1.0.0"
                      xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
   
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyContainerServicePkg" ServiceManifestVersion="1.0.0" />
   <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <PortBinding ContainerPort="80" EndpointRef="myserviceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
   
   <DefaultServices>
      <Service Name="MyContainerService">
        <!-- On a local development cluster, set InstanceCount to 1.  On a multi-node production 
        cluster, set InstanceCount to -1 for the container service to run on every node in 
        the cluster.
        -->
        <StatelessService ServiceTypeName="MyContainerServiceType" InstanceCount="1">
            <SingletonPartition />
        </StatelessService>
      </Service>
   </DefaultServices>
   
</ApplicationManifest>

```

## <a name="next-steps"></a>Étapes suivantes
Dans ce guide de démarrage rapide, vous apprenez à :
> [!div class="checklist"]
> * Placer un conteneur d’images Docker dans un package
> * Configurer la communication
> * Créer et placer l’application Service Fabric dans un package
> * Déployer l’application de conteneur dans Azure

* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-containers-overview.md).
* Consultez le didacticiel [Déployer une application .NET dans un conteneur vers Azure Service Fabric](service-fabric-host-app-in-a-container.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
* Consulter les [exemples de code de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) sur GitHub.

[sfx]: ./media/service-fabric-quickstart-containers-linux/SFX.png
[nginx]: ./media/service-fabric-quickstart-containers-linux/nginx.png
[sf-yeoman]: ./media/service-fabric-quickstart-containers-linux/YoSF.png

