---
title: "Créer une application de conteneur Azure Service Fabric | Microsoft Docs"
description: "Créez votre première application de conteneur Azure Service Fabric.  Concevez une image Docker avec votre application, envoyez l’image vers un registre de conteneurs, créez et déployez une application de conteneur Service Fabric."
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
ms.date: 05/08/2017
ms.author: ryanwi
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: acb68b274228aa647dc7be5d36b2b077bd213c1b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="create-your-first-service-fabric-container-app"></a>Créer votre première application de conteneur Service Fabric
L’exécution d’une application existante dans un conteneur Windows sur un cluster Service Fabric ne nécessite aucune modification de votre application. Ce guide de démarrage rapide vous explique comment créer une image Docker contenant une application web, comment envoyer la nouvelle image vers Azure Container Registry, comment créer une application de conteneur Service Fabric et comment déployer l’application de conteneur sur un cluster Service Fabric.  Cet article suppose une connaissance élémentaire de Docker. Pour en savoir plus sur Docker, consultez la [présentation de Docker](https://docs.docker.com/engine/understanding-docker/).

## <a name="prerequisites"></a>Composants requis
Un ordinateur de développement exécutant :
* Visual Studio 2015 ou Visual Studio 2017.
* [Outils et SDK Service Fabric](service-fabric-get-started.md).
*  Docker pour Windows.  [Obtenez Docker CE pour Windows (stable)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Après l’installation et le démarrage de Docker, cliquez avec le bouton droit de la souris sur l’icône de la barre d’état, puis sélectionnez **Switch to Windows containers** (Basculer vers les conteneurs Windows). Cette action est nécessaire pour exécuter des images Docker basées sur Windows.

Un cluster Windows avec au moins trois nœuds s’exécutant sur Windows Server 2016 avec conteneurs. [Créez un cluster](service-fabric-get-started-azure-cluster.md) ou [essayez gratuitement Service Fabric](http://tryazureservicefabric.westus.cloudapp.azure.com/). 

Un registre dans Azure Container Registry. [Créez un registre de conteneurs](../container-registry/container-registry-get-started-portal.md) dans votre abonnement Azure. 

## <a name="create-a-simple-web-app"></a>Créer une application web simple
Regroupez toutes les ressources que vous avez besoin de charger dans une image Docker à un seul emplacement. Pour ce guide de démarrage rapide, créez une application web « Hello World » sur votre ordinateur de développement.

1. Créez un répertoire, tel que *c:\temp\helloworldapp*.
2. Créez un sous-répertoire *c:\temp\helloworldapp\content*.
3. Créez un fichier *index.html* sous *c:\temp\helloworldapp\content*.
4. Modifiez le fichier *index.html*, puis ajoutez la ligne suivante :
    ```
    <h1>Hello World!</h1>
    ```
5. Enregistrez les modifications apportées à *index.html*.

## <a name="build-the-docker-image"></a>Créer l’image Docker
Créez une image basée sur [l’image microsoft/iis](https://hub.docker.com/r/microsoft/iis/) située sur Docker Hub. L’image microsoft/iis dérive de l’image du système d’exploitation de base Windows Server Core et contient Internet Information Services (IIS).  L’exécution de cette image dans votre conteneur démarre automatiquement IIS et les sites web installés.

Définissez votre image Docker dans un fichier Dockerfile. Le fichier Dockerfile contient des instructions pour créer l’image et charger l’application que vous souhaitez exécuter. Le fichier Dockerfile est l’entrée de la commande ```docker build```, qui crée l’image. 

1. Créez un fichier *Dockerfile* (sans extension de fichier) sous *c:\temp\helloworldapp* et ajoutez :

    ```
    # The `FROM` instruction specifies the base image. You are
    # extending the `microsoft/iis` image.
    FROM microsoft/iis

    # Create a directory to hold the web app in the container.
    RUN mkdir C:\site

    # Create a new IIS site.
    RUN powershell -NoProfile -Command \
        Import-module IISAdministration; \
        New-IISSite -Name "Site" -PhysicalPath C:\site -BindingInformation "*:8000:"

    # Opens port 8000 on the container.
    EXPOSE 8000

    # The final instruction copies the web app you created earlier into the container.
    ADD content/ /site
    ```

    Il n’existe aucune commande ```ENTRYPOINT``` dans ce fichier Dockerfile. Vous n’en avez pas besoin. Lorsque vous exécutez Windows Server avec IIS, le processus IIS est le point d’entrée, qui est configuré pour démarrer dans l’image de base.

    Pour plus d'informations, consultez les [références Dockerfile](https://docs.docker.com/engine/reference/builder/).

2. Exécutez la commande ```docker build``` pour créer l’image qui exécute votre application web. Ouvrez une fenêtre PowerShell et accédez à *c:\temp\helloworldapp*. Exécutez la commande suivante :

    ```
    docker build -t helloworldapp .
    ```
    Cette commande crée la nouvelle image en suivant les instructions de votre fichier Dockerfile, et la nomme (balisage -t) « helloworldapp ». La création d’une image extrait l’image de base de Docker Hub et crée une nouvelle image qui vient s’ajouter à votre application par-dessus l’image de base.  L’[image microsoft/iis](https://hub.docker.com/r/microsoft/iis/) et les images de base du système d’exploitation font 10,5 Go. Leur téléchargement et leur extraction sur votre ordinateur de développement peuvent donc prendre du temps.  N’hésitez pas à lancer l’opération pendant votre pause déjeuner ou votre pause café.  Le téléchargement prend moins de temps si vous avez précédemment extrait l’image de base du système d’exploitation sur votre ordinateur de développement.

3. Une fois la création terminée, exécutez la commande `docker images` pour afficher des informations sur la nouvelle image :

    ```
    docker images
    
    REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
    helloworldapp              latest              86838648aab6        2 minutes ago       10.1 GB
    ```

## <a name="verify-the-image-runs-locally"></a>Vérifier que l’image s’exécute localement
Vérifiez votre image en local avant de l’envoyer dans le registre de conteneurs.  

1. Démarrez le conteneur avec ```docker run``` :

    ```
    docker run -d -p 8000:8000 --name my-web-site helloworldapp
    ```

    *name* donne un nom au conteneur en cours d’exécution (au lieu d’utiliser l’ID de conteneur).

2. Une fois le conteneur démarré, recherchez son adresse IP pour vous connecter à votre conteneur en cours d’exécution à partir d’un navigateur :
    ```
    docker inspect -f "{{ .NetworkSettings.Networks.nat.IPAddress }}" my-web-site
    ```

3. Connectez le conteneur en cours d’exécution.  Ouvrez un navigateur web qui pointe vers l’adresse IP renvoyée sur le port 8000, par exemple « http://172.31.194.61:8000 ». Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

4. Pour arrêter votre conteneur, exécutez :

    ```
    docker stop my-web-site
    ```

5. Supprimez le conteneur de votre ordinateur de développement :

    ```
    docker rm my-web-site
    ```

## <a name="push-the-image-to-the-container-registry"></a>Envoyer l’image dans le registre de conteneurs
Après avoir vérifié que le conteneur s’exécute sur votre ordinateur de développement, envoyez l’image à votre registre dans Azure Container Registry.

1. Exécutez ``docker login`` pour vous connecter à votre Registre de conteneur à l’aide de vos [informations d’identification du Registre](../container-registry/container-registry-authentication.md).

    L’exemple suivant transmet l’ID et le mot de passe d’un [principal du service](../active-directory/active-directory-application-objects.md) Azure Active Directory . Par exemple, vous pouvez avoir affecté un principal du service à votre Registre pour un scénario d’automatisation.

    ```
    docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
    ```

2. La commande suivante crée une balise, ou un alias, de l’image, avec un chemin d’accès qualifié complet vers votre registre. Cet exemple place l’image dans l’espace de noms ```samples``` pour éviter un encombrement à la racine du registre.

    ```
    docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
    ```

3.  Envoyez l’image vers votre registre de conteneurs :

    ```
    docker push myregistry.azurecr.io/samples/helloworldapp
    ```

## <a name="create-and-package-the-containerized-service-in-visual-studio"></a>Créer et empaqueter le service en conteneur dans Visual Studio
Le kit de développement logiciel Service Fabric fournit un modèle de service pour vous aider à déployer un conteneur sur un cluster Service Fabric.

1. Démarrez Visual Studio.  Sélectionnez **Fichier** > **Nouveau** > **Projet**.
2. Sélectionnez **Service Fabric application** (Application Service Fabric), nommez-la « MyFirstContainer », puis cliquez sur **OK**.
3. Sélectionnez **Guest Container** (Conteneur invité) dans la liste des **modèles de service**.
4. Sous **Nom de l’image**, entrez « myregistry.azurecr.io/samples/helloworldapp », c’est-à-dire l’image que vous avez envoyée à votre référentiel de conteneurs. 
5. Donnez un nom à votre service et cliquez sur **OK**.
6. Si votre service en conteneur a besoin d’un point de terminaison pour les communications, vous pouvez désormais ajouter le protocole, le port et le type à ```Endpoint``` dans le fichier ServiceManifest.xml. Dans ce guide de démarrage rapide, le service en conteneur écoute le port 80 : 

    ```xml
    <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    ```
    Fournir ```UriScheme``` enregistre automatiquement le point de terminaison du conteneur avec le service Service Fabric Naming pour la découverte. Un exemple de fichier ServiceManifest.xml complet est fourni à la fin de cet article. 
7. Configurez le mappage port/hôte du conteneur en spécifiant une stratégie ```PortBinding``` dans ```ContainerHostPolicies``` dans le fichier ApplicationManifest.xml.  Pour ce guide de démarrage rapide, ```ContainerPort``` est 8000 (le conteneur expose le port 8000, tel que spécifié dans le fichier Dockerfile) et ```EndpointRef``` est « Guest1TypeEndpoint » (le point de terminaison défini dans le manifeste de service).  Les demandes entrantes pour le service sur le port 80 sont mappées au port 8000 dans le conteneur.  Si votre conteneur doit s’authentifier auprès d’un référentiel privé, ajoutez ```RepositoryCredentials```.  Pour ce guide de démarrage rapide, ajoutez le nom de compte et le mot de passe du registre de conteneurs myregistry.azurecr.io. 

    ```xml
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ```

    Un exemple de fichier ApplicationManifest.xml complet est fourni à la fin de cet article.
8. Configurez le point de terminaison de connexion du cluster de façon à pouvoir publier l’application sur votre cluster.  Vous trouverez le point de terminaison de connexion client dans le panneau d’aperçu de votre cluster dans le [portail Azure](https://portal.azure.com). Dans l’Explorateur de solutions, ouvrez *Cloud.xml* sous **MyFirstContainer**->**PublishProfiles**.  Ajoutez le nom du cluster et le port de connexion à **ClusterConnectionParameters**.  Par exemple :
    ```xml
    <ClusterConnectionParameters ConnectionEndpoint="containercluster.westus2.cloudapp.azure.com:19000" />
    ```
    
9. Enregistrez tous les fichiers et générez votre projet.  

10. Pour empaqueter votre application, cliquez avec le bouton droit de la souris sur **MyFirstContainer** dans l’Explorateur de solutions, puis sélectionnez **Package**. 

## <a name="deploy-the-container-app"></a>Déployer l’application de conteneur
1. Pour publier votre application, cliquez avec le bouton droit de la souris sur **MyFirstContainer** dans l’Explorateur de solutions, puis sélectionnez **Publier**.

2. [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) est un outil web dédié à l’inspection et à la gestion d’applications et de nœuds dans un cluster Service Fabric. Ouvrez un navigateur et accédez à http://containercluster.westus2.cloudapp.azure.com:19080/Explorer/, puis suivez le déploiement de l’application.  L’application se déploie mais se trouve dans un état d’erreur jusqu'à ce que l’image soit téléchargée sur les nœuds du cluster (ce qui peut prendre du temps, selon la taille de l’image) :  ![Erreur][1]

3. L’application est prête lorsqu’elle est à l’état ```Ready``` :  ![Prête][2]

4. Ouvrez un navigateur et accédez à http://containercluster.westus2.cloudapp.azure.com. Vous devez voir le titre « Hello World ! » s’afficher dans le navigateur.

## <a name="clean-up"></a>Nettoyer
Vous continuez à être facturé tant que le cluster est en cours d’exécution. Pensez à [supprimer votre cluster](service-fabric-get-started-azure-cluster.md#remove-the-cluster).  Les [clusters d’essai](http://tryazureservicefabric.westus.cloudapp.azure.com/) sont automatiquement supprimés après quelques heures.

Une fois l’image publiée dans le registre de conteneurs, vous pouvez supprimer l’image locale de votre ordinateur de développement :

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Exemples complets de manifestes d’application et de service Service Fabric
Voici les manifestes d’application et de service complets utilisés dans ce guide de démarrage rapide.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Guest1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="Guest1Type" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying Windows containers to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    <!--
    <EnvironmentVariables>
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
    </EnvironmentVariables>
    -->
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="Guest1TypeEndpoint" UriScheme="http" Port="80" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="MyFirstContainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Guest1_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Guest1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="8000" EndpointRef="Guest1TypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Guest1">
      <StatelessService ServiceTypeName="Guest1Type" InstanceCount="[Guest1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur l’exécution des [conteneurs sur Service Fabric](service-fabric-containers-overview.md).
* En savoir plus sur le [cycle de vie des applications](service-fabric-application-lifecycle.md) Service Fabric.
* Consulter les [exemples de code de conteneur Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-containers) sur GitHub.

[1]: ./media/service-fabric-get-started-containers/MyFirstContainerError.png
[2]: ./media/service-fabric-get-started-containers/MyFirstContainerReady.png

