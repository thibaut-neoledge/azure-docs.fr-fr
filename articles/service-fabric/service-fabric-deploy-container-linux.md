---
title: "Service Fabric et déploiement de conteneurs sous Linux | Microsoft Docs"
description: "Présentation de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs Linux. Cet article décrit les fonctionnalités que Service Fabric offre pour les conteneurs et explique comment déployer une image de conteneur Linux dans un cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 4ba99103-6064-429d-ba17-82861b6ddb11
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/24/2017
ms.author: msfussell
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: fb73507ed596a65607d60f59d6834cc8bf5734f7
ms.lasthandoff: 04/26/2017


---
# <a name="deploy-a-linux-container-to-service-fabric"></a>Déployer un conteneur Linux sur Service Fabric
> [!div class="op_single_selector"]
> * [Déployer un conteneur Windows](service-fabric-deploy-container.md)
> * [Déployer un conteneur Linux](service-fabric-deploy-container-linux.md)
>
>

Cet article vous guide dans le processus de création des services en conteneur sous Linux.

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. On parle alors de services en conteneur.

Ces fonctionnalités sont les suivantes :

* Activation et déploiement d’images de conteneur
* Gouvernance des ressources
* Authentification de référentiels
* Mappage des ports de conteneur aux ports hôtes
* Découverte et communication entre des conteneurs
* Possibilité de configurer et de définir des variables d’environnement

## <a name="packaging-a-docker-container-with-yeoman"></a>Empaquetage d’un conteneur Docker avec Yeoman
Lors de l’empaquetage d’un conteneur sous Linux, vous pouvez choisir d’utiliser un modèle Yeoman ou de [créer le package d’application manuellement](#manually).

Une application Service Fabric peut contenir un ou plusieurs conteneurs, chacun ayant un rôle précis dans la fourniture de la fonctionnalité d’application. Le Kit de développement logiciel (SDK) Service Fabric pour Linux comprend un générateur [Yeoman](http://yeoman.io/) qui facilite la création de votre application et l’ajout d’une image de conteneur. Nous allons utiliser Yeoman pour créer une application avec un seul conteneur Docker appelé *SimpleContainerApp*. Vous pourrez ajouter d’autres services ultérieurement en modifiant les fichiers du manifeste.

## <a name="install-docker-on-your-development-box"></a>Installer Docker dans votre espace de développement

Exécutez les commandes suivantes pour installer Docker dans votre espace de développement Linux (si vous utilisez l’image Vagrant sur OSX, Docker est déjà installé) :

```bash
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
```

## <a name="create-the-application"></a>Création de l'application
1. Saisissez `yo azuresfguest` dans un terminal.
2. Choisissez l’infrastructure **Conteneur** .
3. Donnez un nom à votre application, par exemple, SimpleContainerApp
4. Fournissez l’URL de l’image de conteneur à partir d’un référentiel DockerHub. Le paramètre d’image prend la forme [référentiel] / [nom de l’image]

![Générateur Yeoman Service Fabric pour les conteneurs][sf-yeoman]

## <a name="deploy-the-application"></a>Déployer l’application
Une fois que l’application est générée, vous pouvez la déployer vers le cluster local à l’aide de l’interface de ligne de commande Azure.

1. Connectez-vous au cluster Service Fabric local.

```bash
    azure servicefabric cluster connect
```

2. Utilisez le script d’installation fourni dans le modèle pour copier le package d’application dans le magasin d’images du cluster, inscrire le type d’application et créer une instance de l’application.

```bash
    ./install.sh
```

3. Ouvrez un navigateur et accédez à Service Fabric Explorer à l’adresse http://localhost:19080/Explorer (remplacez localhost par l’adresse IP privée de la machine virtuelle si vous utilisez Vagrant sur Mac OS X).
4. Développez le nœud Applications et notez qu’il existe désormais une entrée pour votre type d’application et une autre pour la première instance de ce type.
5. Utilisez le script de désinstallation fourni dans le modèle pour supprimer l’instance de l’application et annuler l’inscription du type d’application.

```bash
    ./uninstall.sh
```

Pour un exemple d’application, [consultez les exemples de code de conteneur Service Fabric sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-containers).

## <a name="adding-more-services-to-an-existing-application"></a>Ajout d’autres services à une application existante

Pour ajouter un autre conteneur à une application déjà créée à l’aide de `yo`, procédez comme suit : 

1. Accédez au répertoire à la racine de l’application existante.  Par exemple, `cd ~/YeomanSamples/MyApplication`, si `MyApplication` est l’application créée par Yeoman.
2. Exécutez `yo azuresfguest:AddService`.

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container-image"></a>Empaquetage et déploiement manuel d’une image de conteneur
Le processus d’empaquetage manuel d’un service avec conteneur est basé sur les étapes suivantes :

1. Publiez les conteneurs dans votre référentiel.
2. Créez la structure de répertoires du package.
3. Modifiez le fichier de manifeste de service.
4. Modifiez le fichier de manifeste d’application.

## <a name="deploy-and-activate-a-container-image"></a>Déploiement et activation d’une image de conteneur
Dans le [modèle d’application](service-fabric-application-model.md)Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service. Pour déployer et activer un conteneur, placez le nom de l’image de conteneur dans un élément `ContainerHost` au sein du manifeste de service.

Dans le manifeste de service, ajoutez un `ContainerHost` pour le point d’entrée. Définissez ensuite le nom de référentiel de conteneur et d’image comme `ImageName`. Le manifeste partiel suivant affiche un exemple illustrant le déploiement du conteneur appelé `myimage:v1` depuis un référentiel appelé `myrepo` :

```xml
    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimage:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>
```

Vous pouvez fournir des commandes d’entrée en spécifiant l’élément facultatif `Commands` avec un ensemble de commandes délimitées par des virgules à exécuter au sein du conteneur.

## <a name="understand-resource-governance"></a>Présentation de la gouvernance des ressources
La gouvernance des ressources est une fonctionnalité du conteneur, qui limite les ressources que le conteneur peut utiliser sur l’hôte. L’élément `ResourceGovernancePolicy`, spécifié dans le manifeste de l’application, est utilisé pour déclarer des limites relatives aux ressources pour un package de code de service. Des limites de ressources peuvent être définies pour les ressources suivantes :

* Mémoire
* MemorySwap
* CpuShares (pondération relative à l’UC)
* MemoryReservationInMB  
* BlkioWeight (poids relatif de l’élément BlockIO)

> [!NOTE]
> Dans une version ultérieure, il sera possible de prendre en charge la spécification de limites relatives aux E/S en mode bloc précises (E/S par seconde, bits par seconde en lecture/écriture...).
> 
> 

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500"
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="authenticate-a-repository"></a>Authentification d’un référentiel
Pour télécharger un conteneur, vous devrez peut-être fournir des informations d’identification dans le référentiel du conteneur. Les informations d’identification de connexion spécifiées dans le manifeste de l’application sont utilisées pour spécifier les informations de connexion (ou clé SSH) pour le téléchargement de l’image de conteneur à partir du référentiel d’images. L’exemple suivant utilise un compte appelé *TestUser*, ainsi que le mot de passe en texte clair associé (*non* recommandé) :

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

Nous vous recommandons de chiffrer le mot de passe à l’aide d’un certificat qui est déployé sur l’ordinateur.

L’exemple suivant utilise un compte appelé *TestUser* avec un mot de passe chiffré à l’aide d’un certificat nommé *MyCert*. Vous pouvez utiliser la commande PowerShell `Invoke-ServiceFabricEncryptText` pour créer le texte de chiffrement secret du mot de passe. Pour en savoir plus, consultez l’article [Gestion des secrets dans des applications Service Fabric](service-fabric-application-secret-management.md).

La clé privée du certificat utilisée pour déchiffrer le mot de passe doit être déployée sur l’ordinateur local dans une méthode hors bande (dans Azure, cette méthode est Azure Resource Manager). Ensuite, lorsque le Service Fabric déploie le package de service sur l’ordinateur, il peut déchiffrer la clé secrète. Avec la clé secrète et le nom du compte, il peut ensuite s’authentifier dans le référentiel de conteneur.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-port-to-host-port-mapping"></a>Configuration du mappage des ports de conteneur aux ports hôtes
Vous pouvez configurer un port d’hôte utilisé pour communiquer avec le conteneur en spécifiant un élément `PortBinding` dans le manifeste de l’application. La liaison de port mappe le port sur lequel le service écoute, à l’intérieur du conteneur, à un port sur l’hôte.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

## <a name="configure-container-to-container-discovery-and-communication"></a>Configuration de la découverte et de la communication entre des conteneurs.
À l’aide de la stratégie `PortBinding`, vous pouvez mapper un port de conteneur à un élément `Endpoint` dans le manifeste de service. Le point de terminaison `Endpoint1` peut spécifier un port fixe (par exemple, le port 80). Il peut également ne spécifier aucun port, auquel cas un port aléatoire est sélectionné dans la plage de ports des applications du cluster.

Si vous spécifiez un point de terminaison à l’aide de la balise `Endpoint` dans le manifeste de service d’un conteneur invité, Service Fabric peut publier automatiquement ce point de terminaison au Naming Service. D’autres services exécutés dans le cluster peuvent ainsi détecter ce conteneur à l’aide de requêtes REST pour la résolution.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>
```

En vous inscrivant auprès du Naming Service, vous pouvez facilement établir des communications entre les conteneurs dans le code au sein de votre conteneur à l’aide d’un [proxy inverse](service-fabric-reverseproxy.md). Pour établir la communication, vous devez fournir le port d’écoute HTTP associé au proxy inverse et le nom des services avec lesquels vous souhaitez communiquer en tant que variables d’environnement. Pour en savoir plus, consultez la section suivante. 

## <a name="configure-and-set-environment-variables"></a>Configurer et définir des variables d’environnement
Des variables d’environnement peuvent être spécifiées pour chaque package de code dans le manifeste de service pour les services déployés dans des conteneurs, ou pour les services déployés en tant qu’exécutables invités/processus. Ces valeurs de variable d’environnement peuvent être remplacées dans le manifeste de l’application, ou spécifiées lors du déploiement, en tant que paramètres d’application.

L’extrait de code XML du manifeste de service suivant illustre la méthode à suivre pour spécifier des variables d’environnement pour un package de code :

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>
```

Ces variables d’environnement peuvent être remplacées au niveau du manifeste de l’application :

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>
```

Dans l’exemple ci-dessus, nous avons indiqué une valeur explicite pour la variable d’environnement `HttpGateway` (19000) alors que la valeur du paramètre `BackendServiceName` est définie par le biais du paramètre d’application `[BackendSvc]`. De cette manière, vous pouvez spécifier la valeur de l’élément `BackendServiceName`au moment du déploiement de l’application, sans avoir de valeur fixe dans le manifeste.

## <a name="complete-examples-for-application-and-service-manifest"></a>Exemples complets pour le manifeste de l’application et de service

Voici un exemple de fichier manifeste de l’application :

```xml
    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>
```

Voici un exemple de manifeste de service (indiqué dans le manifeste de l’application ci-dessus) :

```xml
    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless front end in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Endpoints>
                <Endpoint Name="Endpoint1" UriScheme="http" Port="80" Protocol="http"/>
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez déployé un service en conteneur, découvrez comment gérer son cycle de vie en lisant [Cycle de vie des applications Service Fabric](service-fabric-application-lifecycle.md).

* [Vue d’ensemble de Service Fabric et des conteneurs](service-fabric-containers-overview.md)
* [Interaction avec les clusters Service Fabric à l’aide de l’interface de ligne de commande Azure](service-fabric-azure-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-deploy-container-linux/sf-container-yeoman1.png

