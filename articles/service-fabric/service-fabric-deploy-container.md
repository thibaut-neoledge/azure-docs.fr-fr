<properties
   pageTitle="Service Fabric et le déploiement de conteneurs | Microsoft Azure"
   description="Présentation de Service Fabric et de la méthode à suivre pour déployer des applications de microservices au moyen de conteneurs. Cet article détaille les fonctionnalités que Service Fabric offre pour les conteneurs et explique comment déployer une image de conteneur dans un cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>

# Aperçu : Déployer un conteneur sur Service Fabric

>[AZURE.NOTE] Cette fonctionnalité est disponible en version préliminaire pour Linux. Par contre, elle n’est pas disponible actuellement sur Windows Server. Elle sera proposée en version préliminaire sur Windows Server dans la version suivante de Service Fabric, après la disponibilité générale de Windows Server 2016. La version ultérieure devrait prendre cette fonctionnalité en charge.

Service Fabric dispose de plusieurs fonctionnalités de gestion des conteneurs, qui vous aident à créer des applications composées de microservices mis en conteneur. On parle de services en conteneur. Ces fonctionnalités sont les suivantes :

- Activation et déploiement d’images de conteneur
- Gouvernance des ressources
- Authentification de référentiels
- Mappage des ports de conteneur aux ports hôtes
- Découverte et communication entre des conteneurs
- Possibilité de configurer et de définir des variables d’environnement

Examinons à présent chacune des fonctionnalités lors de l’empaquetage d’un service en conteneur à inclure dans votre application.

## Empaquetage d’un conteneur

Lors de l’empaquetage d’un conteneur, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou de [créer le package d’application manuellement](#manually). À l’aide de Visual Studio, la structure de package d’application et les fichiers manifest sont créés pour vous par l’assistant de nouveau projet.

## Utilisation de Visual Studio pour empaqueter un exécutable existant

>[AZURE.NOTE] Dans une prochaine version du Kit de développement logiciel (SDK) des outils Visual Studio, vous pourrez ajouter un conteneur à une application de la même manière que vous ajoutez actuellement un exécutable invité. Voir [Déploiement d’un exécutable invité dans Service Fabric](service-fabric-deploy-existing-app.md). Actuellement, vous devez effectuer l’empaquetage manuel comme indiqué ci-dessous.

<a id="manually"></a>
## Empaquetage manuel et déploiement de conteneurs
Le processus d’empaquetage manuel d’un service avec conteneur est basé sur les étapes suivantes :

1. Publiez les conteneurs dans votre référentiel.
2. Créez la structure de répertoires du package.
3. Modifiez le fichier de manifeste de service.
4. Modifiez le fichier de manifeste d’application.

## Activation et déploiement d’images de conteneur
Dans le [modèle d’application](service-fabric-application-model.md) Service Fabric, un conteneur représente un hôte d’application sur lequel sont placés plusieurs réplicas de service. Pour déployer et activer un conteneur, placez le nom de l’image de conteneur dans un élément `ContainerHost` au sein du manifeste de service.

Dans le manifeste de service, ajoutez un élément `ContainerHost` pour le point d’entrée et définissez l’élément `ImageName` de manière à ce qu’il corresponde au nom de l’image et du référentiel du conteneur. Le manifeste partiel suivant affiche un exemple illustrant le déploiement du conteneur appelé *myimage:v1* dans le référentiel *MonRéf*.

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Vous pouvez fournir des commandes d’entrée à l’image de conteneur en spécifiant le paramètre facultatif `Commands` avec un ensemble de commandes, délimitées par des virgules, à exécuter au sein du conteneur.

## Gouvernance des ressources
La gouvernance des ressources est une fonctionnalité du conteneur, qui limite les ressources que le conteneur peut utiliser sur l’hôte. L’élément `ResourceGovernancePolicy`, spécifié dans le manifeste de l’application, offre la possibilité de déclarer des limites relatives aux ressources pour un package de code de service. Les limites relatives aux ressources peuvent être définies pour les éléments suivants :

- Mémoire
- MemorySwap
- CpuShares (pondération relative à l’UC)
- MemoryReservationInMB
- BlkioWeight (poids relatif de l’élément BlockIO)

>[AZURE.NOTE] Dans une version ultérieure, il sera possible de prendre en charge la spécification de limites relatives aux E/S en mode bloc précises (E/S par seconde, bits par seconde en lecture/écriture...).


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## Authentification de référentiels
Pour télécharger un conteneur, vous devrez peut-être fournir des informations d’identification dans le référentiel du conteneur. Les informations d’identification de connexion spécifiées dans le manifeste de l’*application* sont utilisées pour spécifier les informations de connexion (ou clé SSH) pour le téléchargement de l’image de conteneur à partir du référentiel d’images. L’exemple suivant représente un compte appelé *TestUser*, ainsi que le mot de passe en texte clair associé. Cela n’est **pas** recommandé.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Le mot de passe peut et doit être chiffré à l’aide d’un certificat déployé sur l’ordinateur.

L’exemple suivant représente un compte appelé *TestUser* avec un mot de passe chiffré à l’aide d’un certificat nommé *MonCert*. Vous pouvez utiliser la commande Powershell `Invoke-ServiceFabricEncryptText` pour créer le texte de chiffrement secret du mot de passe. Pour en savoir plus sur la procédure à suivre, consultez l’article [Gestion des secrets dans des applications Service Fabric](service-fabric-application-secret-management.md). La clé privée du certificat pour déchiffrer le mot de passe doit être déployée sur l’ordinateur local dans une méthode hors bande (via Resource Manager dans Azure). Ensuite, lorsque Service Fabric déploie le package de service sur l’ordinateur, il est en mesure de déchiffrer le secret, ainsi que le nom du compte, et de s’authentifier auprès du référentiel du conteneur à l’aide des informations d’identification.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
            <SecurityAccessPolicies>
                <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
            </SecurityAccessPolicies>
        </Policies>
    </ServiceManifestImport>

## Mappage des ports de conteneur aux ports hôtes
Vous pouvez configurer un port d’hôte utilisé pour communiquer avec le conteneur en spécifiant un élément `PortBinding` dans le manifeste de l’application. La liaison de port mappe le port sur lequel le service écoute, à l’intérieur du conteneur, à un port sur l’hôte.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## Découverte et communication entre des conteneurs
À l’aide de la stratégie `PortBinding`, vous pouvez mapper un port de conteneur à un élément `Endpoint` dans le manifeste de service, comme indiqué dans l’exemple suivant. Le point de terminaison `Endpoint1` peut spécifier un port fixe, par exemple le port 80, ou aucun port, auquel cas un port aléatoire est sélectionné dans la plage de ports des applications du cluster.

Pour les conteneurs invités, la spécification d’un élément `Endpoint` comme celui-ci dans le manifeste de service permet à Service Fabric de publier automatiquement ce point de terminaison sur le service d’affectation de noms, afin que d’autres services exécutés dans le cluster puissent découvrir ce conteneur à l’aide des requêtes REST du service de résolution.

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

En vous inscrivant auprès du service d’affectation de noms, vous pouvez facilement établir des communications entre les conteneurs dans le code au sein de votre conteneur, en utilisant un [proxy inverse](service-fabric-reverseproxy.md). Pour cela, il vous suffit de fournir le port d’écoute HTTP associé au proxy inverse et le nom des services avec lesquels vous souhaitez communiquer en les définissant en tant que variables d’environnement. Pour savoir comment procéder, consultez la section suivante.

## Configurer et définir des variables d’environnement
Des variables d’environnement peuvent être spécifiées pour chaque package de code dans le manifeste de service pour les services déployés dans des conteneurs, ou en tant qu’exécutables invités/processus. Ces valeurs de variable d’environnement peuvent être remplacées dans le manifeste de l’application, ou spécifiées lors du déploiement, en tant que paramètres d’application.

L’extrait de code XML du manifeste de service suivant illustre la méthode à suivre pour spécifier des variables d’environnement pour un package de code.

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

Ces variables d’environnement peuvent être remplacées au niveau du manifeste de l’application :

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

Dans l’exemple ci-dessus, nous avons indiqué une valeur explicite pour la variable d’environnement `HttpGateway` (19000) alors que la valeur de l’élément `BackendServiceName` est définie via le paramètre d’application `[BackendSvc]`. Cela vous permet de spécifier la valeur de l’élément `BackendServiceName` au moment du déploiement de l’application, sans avoir de valeur fixe dans le manifeste.

## Exemples complets pour le manifeste de l’application et de service
Voici un exemple de manifeste d’application qui illustre les fonctionnalités du conteneur.


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


Voici un exemple de manifeste de service (indiqué dans le manifeste de l’application ci-dessus) qui détaille les fonctionnalités du conteneur.

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
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
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>

<!---HONumber=AcomDC_0928_2016-->