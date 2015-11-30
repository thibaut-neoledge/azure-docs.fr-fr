<properties
   pageTitle="Modèle d'application Service Fabric | Microsoft Azure"
   description="Comment modéliser et décrire des applications et des services dans Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/02/2015"
   ms.author="alexwun"/>

# Modéliser une application dans Service Fabric

Cet article fournit une vue d'ensemble du modèle d'application de Service Fabric et explique comment définir une application et un service via des fichiers manifestes et empaqueter l'application en vue de son déploiement.

## Comprendre le modèle d'application

Une application est une collection de services constitutifs qui exécutent une ou plusieurs fonctions. Un service exécute une fonction complète et autonome (il peut démarrer et s'exécuter indépendamment des autres services) et est composé de code, de configuration et de données. Pour chaque service, le code se compose de fichiers binaires exécutables, la configuration comprend des paramètres de service qui peuvent être chargés au moment de l'exécution, tandis que les données comportent des données statiques arbitraires destinées à être consommées par le service. Chaque composant de ce modèle d'application hiérarchique peut faire l'objet d'une gestion de versions et d'une mise à niveau indépendantes.

![][1]


Un type d'application est une catégorisation d'une application, constituée d'un ensemble de types de service. Un type de service est une catégorisation d'un service, qui peut avoir différents paramètres et configurations, mais la fonctionnalité principale reste la même. Les instances d'un service représentent les variantes de configuration de service d'un même type de service.

Les classes (ou « types ») des applications et services sont décrites à l'aide de fichiers XML (manifestes d'application et manifestes de service) qui sont des modèles par rapport auxquels les applications peuvent être instanciées. Le code de différentes instances d'application s'exécute sous forme de processus distincts même si elles sont hébergées par le même nœud Service Fabric. En outre, le cycle de vie de chaque instance d'application peut être géré (c'est-à-dire, mis à niveau) indépendamment. Comme le montre le diagramme suivant, les types d'application sont composés de types de service, eux-mêmes constitués de code, de configuration et de packages.

![Types d'applications et types de services Service Fabric][Image1]

Deux fichiers manifestes différents sont utilisés pour décrire les applications et les services : le manifeste de service et le manifeste d'application, qui sont traités en détail dans les sections suivantes.

Une ou plusieurs instances d'un type de service peuvent être actives dans le cluster. Par exemple, les instances de service avec état, ou réplicas, assurent une grande fiabilité en répliquant l'état entre les réplicas situés sur différents nœuds du cluster (la redondance ainsi obtenue garantira notamment la disponibilité du service en cas de défaillance d'un nœud du cluster). Un [service partitionné](service-fabric-concepts-partitioning.md) subdivise son état (et les modèles d'accès à cet état) entre les nœuds du cluster.

Le diagramme suivant montre la relation entre les applications et les instances de service, les partitions et les réplicas.

![Partitions et réplicas au sein d'un service][Image2]


## Décrire un service

Le manifeste de service définit la version et le type de service de manière déclarative et spécifie les métadonnées de service telles que le type de service, les propriétés de vérification d'intégrité, les métriques d'équilibrage de charge, les fichiers binaires du service et les fichiers de configuration. Autrement dit, il décrit les packages de code, de configuration et de données qui composent un package de service pour prendre en charge un ou plusieurs types de service. Voici un exemple simple de manifeste de service :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

Les attributs **Version** sont des chaînes non structurées et ne sont pas analysés par le système. Ils permettent de gérer les versions de chaque composant au moment des mises à niveau.

**ServiceTypes** déclare les types de service pris en charge par les packages de code (**CodePackage**) dans ce manifeste. Quand un service est instancié par rapport à un de ces types de service, tous les packages de code déclarés dans ce manifeste sont activés en exécutant leurs points d'entrée. Les processus qui en résultent sont censés inscrire les types de service pris en charge au moment de l'exécution. Notez que les types de service sont déclarés au niveau du manifeste et non au niveau du package de code. Ainsi, s'il existe plusieurs packages de code, ceux-ci sont tous activés chaque fois que le système recherche l'un des types de service déclarés.

**SetupEntryPoint** est un point d'entrée privilégié qui s'exécute avec les mêmes informations d'identification que Service Fabric (généralement le compte *LocalSystem*) avant tout autre point d'entrée. Le fichier exécutable spécifié par **EntryPoint** étant généralement l'hôte de service de longue durée, la présence d'un point d'entrée de configuration distinct évite d'avoir à exécuter l'hôte de service avec des privilèges élevés pendant de longues périodes de temps. Le fichier exécutable spécifié par **EntryPoint** est exécuté une fois que **SetupEntryPoint** se termine correctement. Le processus résultant fait l'objet d'une surveillance et est redémarré (de nouveau à partir de **SetupEntryPoint**) en cas d'interruption ou de défaillance.

**DataPackage** déclare un dossier nommé par l'attribut **Name** qui contient des données statiques arbitraires destinées à être consommées par le processus au moment de l'exécution.

**ConfigPackage** déclare un dossier nommé par l'attribut **Name** contenant un fichier *Settings.xml*. Ce fichier contient des sections de paramètres clé-valeur définis par l'utilisateur que le processus peut lire au moment de l'exécution. Pendant la mise à niveau, si seule la **version** de **ConfigPackage** a changé, le processus en cours d'exécution n'est pas redémarré. Au lieu de cela, un rappel indique au processus que les paramètres de configuration ont été modifiés afin qu'ils puissent être rechargés dynamiquement. Voici un exemple de fichier *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Foo" />
    <Parameter Name="MySettingB" Value="Bar" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE]Un manifeste de service peut contenir plusieurs packages de code, de configuration et de données, chacun pouvant faire l'objet d'une gestion de versions indépendante.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## Décrire une application


Le manifeste d'application décrit de manière déclarative le type d'application et la version, et spécifie les métadonnées de composition de service telles que les noms stables, le schéma de partitionnement, le nombre d'instances/facteur de réplication, la stratégie de sécurité/d'isolation, les contraintes de placement, les substitutions de configuration et les types de service constitutifs. Les domaines d'équilibrage de charge dans lesquels l'application est placée sont également décrits. Ainsi, un manifeste d'application décrit les éléments au niveau de l'application et fait référence à un ou plusieurs des manifestes de service pour composer un type d'application. Voici un exemple simple de manifeste d'application :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Comme dans le cas des manifestes de service, les attributs **Version** sont des chaînes non structurées et ne sont pas analysés par le système. En outre, ils permettent de gérer les versions de chaque composant au moment des mises à niveau.

**ServiceManifestImport** contient des références aux manifestes de service qui composent ce type d'application. Les manifestes de service importés déterminent les types de service qui sont valides dans ce type d'application.

**DefaultServices** déclare des instances de service qui sont créées automatiquement chaque fois qu'une application est instanciée par rapport à ce type d'application. Les services par défaut peuvent s'avérer pratiques et se comportent à tous égards comme des services normaux une fois qu'ils ont été créés. Ils sont mis à niveau avec tous les autres services dans l'instance d'application et peuvent également être supprimés.

> [AZURE.NOTE]Un manifeste d'application peut contenir plusieurs importations de manifeste de service et services par défaut. La version de chaque importation de manifeste de service peut être gérée indépendamment.

Pour savoir comment mettre à jour différents paramètres d’application et de service des environnements individuels, consultez [Managing application parameters for multiple environments](service-fabric-manage-multiple-environment-app-configuration.md)

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## Empaqueter une application

### Disposition du package

Le manifeste d'application, le(s) manifeste(s) de service et les autres fichiers de package nécessaires doivent être organisés selon une disposition spécifique en vue du déploiement dans un cluster Service Fabric. Les exemples de manifestes dans cet article doivent être organisés selon la structure de répertoires suivante :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Les dossiers sont nommés d'après les attributs **Name** de chaque élément correspondant. Par exemple, si le manifeste de service contient deux packages de code nommés **MonCodeA** et **MonCodeB**, deux dossiers de même nom contenant les fichiers binaires nécessaires pour chaque package de code sont nécessaires.

### Utilisation de SetupEntryPoint
SetupEntryPoint est en général utilisée lorsque vous avez besoin de faire quelque chose avant le démarrage du service, ou que vous devez effectuer une opération nécessitant des privilèges plus élevés. Cela pourrait être, par exemple, la configuration et l’initialisation de variables d'environnement que l'exécutable du service peut utiliser. Cela inclut non seulement les exécutables écrits avec les modèles de programmation de Service Fabric mais aussi les EXE. Par exemple, si vous déployez une application nodejs, les variations d’environnement doivent être configurées pour l’exécutable npm.exe. - ACL une ressource comme un certificat

Voici les étapes permettant de vous assurer que votre code (exe), fichier de commandes ou PowerShell sont empaquetés correctement dans un projet Visual Studio.


### Création d'un package à l'aide de Visual Studio

Si vous utilisez Visual Studio 2015 pour créer votre application, vous pouvez utiliser la commande Package pour créer automatiquement un package qui correspond à la disposition décrite ci-dessus.

Pour créer un package, cliquez simplement avec le bouton droit sur le projet d'application dans l'Explorateur de solutions et choisissez la commande Package, comme indiqué ci-dessous :

![][2]

Quand la création du package est terminée, l'emplacement du package est indiqué dans la fenêtre Sortie. Notez que l'étape de création du package se produit automatiquement quand vous déployez ou déboguez votre application dans Visual Studio.

### Test du package

La structure du package peut être vérifiée localement via PowerShell à l'aide de la commande **Test-ServiceFabricApplicationPackage**, qui détecte les problèmes d'analyse de manifeste éventuels et vérifier toutes les références. Notez que cette commande ne vérifie que l'exactitude de la structure des répertoires et des fichiers du package et la présence de tous les fichiers nécessaires ; elle ne vérifie pas le contenu du package relatif au code ou aux données :

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Cette erreur indique que le fichier *MySetup.bat* référencé dans le manifeste de service **SetupEntryPoint** est manquant dans le package de code. Une fois le fichier manquant ajouté, l'application passe avec succès la phase de vérification :

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Une fois l'application correctement empaquetée et vérifiée, elle peut être déployée.

## Étapes suivantes

[Déploiement et suppression d’applications][10] [Gestion de paramètres d’application pour les environnements multiples][11] [RunAs : exécution d’une application Service Fabric avec différentes autorisations de sécurité][12] <!--Image references--> [1]: ./media/service-fabric-application-model/application-model.jpg [2]: ./media/service-fabric-application-model/vs-package-command.png [Image1]: media/service-fabric-application-model/Service1.jpg [Image2]: media/service-fabric-application-model/Service2.jpg

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

<!---HONumber=Nov15_HO4-->