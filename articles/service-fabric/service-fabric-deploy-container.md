---
title: "Service Fabric et le déploiement de conteneurs | Microsoft Docs"
description: "Présentation de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs. Cet article décrit les fonctionnalités que Service Fabric offre pour les conteneurs et explique comment déployer une image de conteneur Windows dans un cluster."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: 799cc9ad-32fd-486e-a6b6-efff6b13622d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/24/2016
ms.author: mfussell
translationtype: Human Translation
ms.sourcegitcommit: af9f761179896a1acdde8e8b20476b7db33ca772
ms.openlocfilehash: 1c5f3bc66c902c3b7186cad44728fa5237dd298a


---
# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Aperçu : Déployer un conteneur Windows sur Service Fabric
> [!div class="op_single_selector"]
> * [Déployer un conteneur Windows](service-fabric-deploy-container.md)
> * [Déployer un conteneur Docker](service-fabric-deploy-container-linux.md)
> 
> 

Cet article vous guide dans le processus de création des services dans des conteneurs Windows.

> [!NOTE]
> Cette fonctionnalité est disponible en version préliminaire pour Linux. Elle n’est pas encore disponible sur Windows Server 2016. La fonctionnalité sera disponible en version préliminaire pour Windows Server 2016 dans la prochaine version d’Azure Service Fabric. 
> 
> 

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. 

Ces fonctionnalités sont les suivantes :

* Activation et déploiement d’images de conteneur
* Gouvernance des ressources
* Authentification de référentiels
* Mappage des ports de conteneur aux ports hôtes
* Découverte et communication entre des conteneurs
* Possibilité de configurer et de définir des variables d’environnement

Examinons à présent le fonctionnement de chacune des fonctionnalités lors de l’empaquetage d’un service en conteneur à inclure dans votre application.

## <a name="package-a-windows-container"></a>Empaquetage d’un conteneur Windows
Lors de l’empaquetage d’un conteneur, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou de [créer le package d’application manuellement](#manually). Lorsque vous utilisez Visual Studio, la structure de package d’application et les fichiers manifeste sont créés par le modèle de nouveau projet. Le modèle Visual Studio sera disponible dans une version ultérieure.

## <a name="use-visual-studio-to-package-an-existing-container-image"></a>Utilisation de Visual Studio pour empaqueter une image de conteneur
> [!NOTE]
> Dans une prochaine version de Visual Studio pour Service Fabric, vous pouvez ajouter un conteneur à une application de la même manière que vous pouvez ajouter un exécutable invité aujourd'hui. Pour en savoir plus, consultez la rubrique [Déploiement d’un exécutable invité dans Service Fabric](service-fabric-deploy-existing-app.md). Actuellement, vous devez empaqueter manuellement un conteneur comme décrit dans la section suivante.
> 
> 

<a id="manually"></a>

## <a name="manually-package-and-deploy-a-container"></a>Empaquetage et déploiement d’un conteneur manuellement
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
À l’aide de la stratégie `PortBinding`, vous pouvez mapper un port de conteneur à un élément `Endpoint` dans le manifeste de service, comme indiqué dans l’exemple suivant. Le point de terminaison `Endpoint1` peut spécifier un port fixe (par exemple, le port 80). Il peut également ne spécifier aucun port, auquel cas un port aléatoire est sélectionné dans la plage de ports des applications du cluster.

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
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Endpoints>
        </Resources>
    </ServiceManifest>
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez déployé un service en conteneur, découvrez comment gérer son cycle de vie en lisant [Cycle de vie des applications Service Fabric](service-fabric-application-lifecycle.md).




<!--HONumber=Nov16_HO3-->


