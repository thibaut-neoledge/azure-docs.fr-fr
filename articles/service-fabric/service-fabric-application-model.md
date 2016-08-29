<properties
   pageTitle="Modèle d'application Service Fabric | azure.microsoft.com/ Azure"
   description="Comment modéliser et décrire des applications et des services dans Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"   
   ms.author="seanmck"/>

# Modéliser une application dans Service Fabric

Cet article fournit une vue d’ensemble du modèle d’application Service Fabric Azure. Il explique également comment définir une application et un service via des fichiers manifestes et empaqueter l’application en vue de son déploiement.

## Comprendre le modèle d'application

Une application est une collection de services constitutifs qui exécutent une ou plusieurs fonctions. Un service exécute une fonction complète et autonome (il peut démarrer et s'exécuter indépendamment des autres services) et est composé de code, d’une configuration et de données. Pour chaque service, le code se compose de fichiers binaires exécutables, la configuration comprend des paramètres de service qui peuvent être chargés pendant l'exécution, tandis que les données comportent des données statiques arbitraires destinées à être consommées par le service. Chaque composant de ce modèle d'application hiérarchique peut faire l'objet d'une gestion de versions et d'une mise à niveau indépendantes.

![Modèle d'application Service Fabric][appmodel-diagram]


Un type d'application est une catégorisation d'une application constituée d'un ensemble de types de service. Un type de service est la catégorisation d’un service. La catégorisation peut avoir différents paramètres et différentes configurations, mais la fonctionnalité principale reste la même. Les instances d'un service représentent les variantes de configuration de service d'un même type de service.

Les classes (ou « types ») d'applications et services sont décrites à l'aide de fichiers XML (manifestes d'application et manifestes de service) qui sont des modèles sur lesquels des applications peuvent être instanciées à partir du magasin d'images du cluster. La définition de schéma pour les fichiers ServiceManifest.xml et ApplicationManifest.xml est installée avec le Kit de développement logiciel (SDK) Service Fabric et les outils sous *C:\\Program Files\\azure.microsoft.com/ SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

Le code de différentes instances d'application s'exécute sous forme de processus distincts même si elles sont hébergées par le même nœud Service Fabric. En outre, le cycle de vie de chaque instance d'application peut être géré (c'est-à-dire, mis à niveau) indépendamment. Comme le montre le diagramme suivant, les types d'application sont composés de types de service, eux-mêmes constitués de code, de configuration et de packages. Pour simplifier le diagramme, seuls les packages code/configuration/données pour `ServiceType4` sont affichés, même si chaque type de service inclut certains ou tous ces types de packages.

![Types d’application service Fabric et types de service][cluster-imagestore-apptypes]

Deux fichiers manifestes différents sont utilisés pour décrire des applications et des services : le manifeste de service et le manifeste d'application. Ils sont traités en détail dans les sections suivantes.

Une ou plusieurs instances d'un type de service peuvent être actives dans le cluster. Par exemple, les instances de service avec état, ou réplicas, assurent une grande fiabilité en répliquant l'état entre les réplicas situés sur différents nœuds du cluster. Cette réplication garantit notamment la redondance du service en cas de défaillance d’un nœud du cluster. Un [service partitionné](service-fabric-concepts-partitioning.md) subdivise son état (et les modèles d'accès à cet état) entre les nœuds du cluster.

Le diagramme suivant montre la relation entre les applications et les instances de service, les partitions et les réplicas.

![Partitions et réplicas au sein d'un service][cluster-application-instances]


>[AZURE.TIP] Vous pouvez afficher la disposition des applications dans un cluster à l’aide de l’outil Service Fabric Explorer disponible à l’adresse http://&lt;yourclusteraddress&gt;:19080/Explorer. Pour plus de détails, consultez [Visualisation de votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Décrire un service

Le manifeste de service définit de manière déclarative le type de service et la version. Il spécifie les métadonnées de service telles que le type de service, les propriétés du contrôle d’intégrité, des mesures d’équilibrage de charge, des fichiers binaires de service et des fichiers de configuration. Autrement dit, il décrit les packages de code, de configuration et de données qui composent un package de service pour prendre en charge un ou plusieurs types de service. Voici un exemple simple de manifeste de service :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.azure.microsoft.com/.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
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

**ServiceTypes** déclare les types de service pris en charge par des packages de code (**CodePackage**) dans ce manifeste. Quand un service est instancié par rapport à un de ces types de service, tous les packages de code déclarés dans ce manifeste sont activés en exécutant leurs points d'entrée. Les processus qui en résultent sont censés inscrire les types de service pris en charge au moment de l'exécution. Notez que les types de service sont déclarés au niveau du manifeste et non au niveau du package de code. Ainsi, s'il existe plusieurs packages de code, ceux-ci sont tous activés chaque fois que le système recherche l'un des types de service déclarés.

**SetupEntryPoint** est un point d'entrée privilégié qui s'exécute avec les mêmes informations d'identification que Service Fabric (généralement le compte *LocalSystem*) avant tout autre point d'entrée. Le fichier exécutable spécifié par **EntryPoint** est généralement l’hôte de service à exécution longue. La présence d’un point d’entrée de configuration distinct évite d’avoir à exécuter l’hôte de service avec des privilèges élevés pendant de longues périodes de temps. Le fichier exécutable spécifié par **EntryPoint** est exécuté une fois que **SetupEntryPoint** se termine correctement. Le processus résultant fait l'objet d'une surveillance et est redémarré (à partir de **SetupEntryPoint**) en cas d'interruption ou de défaillance.

**DataPackage** déclare un dossier, nommé par l'attribut **Name**, qui contient des données statiques arbitraires destinées à être consommées par le processus pendant l'exécution.

**ConfigPackage** déclare un dossier, nommé par l'attribut **Name**, qui contient un fichier *Settings.xml*. Ce fichier contient des sections de paramètres clé-valeur définis par l'utilisateur que le processus peut lire pendant l'exécution. Le processus en cours d’exécution n’est pas redémarré pendant la mise à niveau si seule la **version** de **ConfigPackage** a changé. Au lieu de cela, un rappel indique au processus que les paramètres de configuration ont été modifiés afin qu'ils puissent être rechargés dynamiquement. Voici un exemple de fichier *Settings.xml* :

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.azure.microsoft.com/.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE] Un manifeste de service peut contenir plusieurs packages de code, de configuration et de données. Les versions de chacun de ces packages peuvent être gérées de manière indépendante.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## Décrire une application


Le manifeste d’application décrit le type et la version de manière déclarative. Il spécifie les métadonnées de composition de service telles que les noms stables, le schéma de partitionnement, le nombre d'instances/facteur de réplication, la stratégie de sécurité/d'isolation, les contraintes de placement, les remplacements de configuration et les types de service constitutifs. Les domaines d'équilibrage de charge dans lesquels l'application est placée sont également décrits.

Ainsi, un manifeste d'application décrit les éléments au niveau de l'application et fait référence à un ou plusieurs des manifestes de service pour composer un type d'application. Voici un exemple simple de manifeste d'application :

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.azure.microsoft.com/.com/2011/01/fabric"
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

> [AZURE.NOTE] Un manifeste d'application peut contenir plusieurs importations de manifeste de service et services par défaut. La version de chaque importation de manifeste de service peut être gérée indépendamment.

Pour savoir comment mettre à jour différents paramètres d’application et de service dans des environnements individuels, consultez [Gestion des paramètres d’application pour plusieurs environnements](service-fabric-manage-multiple-environment-app-configuration.md)

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

Les dossiers sont nommés d'après les attributs **Name** de chaque élément correspondant. Par exemple, si le manifeste de service contient deux packages de code nommés **MyCodeA** et **MyCodeB**, deux dossiers de même nom contiennent les fichiers binaires nécessaires pour chaque package de code.

### Utilisation de SetupEntrypoint

**SetupEntryPoint** est généralement utilisé lorsque vous avez besoin d’exécuter un fichier exécutable avant le démarrage du service ou si vous devez effectuer une opération avec des privilèges élevés. Par exemple :

- la configuration et l’initialisation de variables d'environnement dont le fichier exécutable du service a besoin, sans limitation aux seuls exécutables écrits via les modèles de programmation de Service Fabric. Par exemple, npm.exe a besoin de certaines variables d’environnement configurées pour le déploiement d’une application node.js.

- La configuration d’un contrôle d’accès via l’installation de certificats de sécurité.

### Création d'un package à l'aide de Visual Studio

Si vous utilisez Visual Studio 2015 pour créer votre application, vous pouvez utiliser la commande Package pour créer automatiquement un package qui correspond à la disposition décrite ci-dessus.

Pour créer un package, cliquez avec le bouton droit sur l'application dans l'Explorateur de solutions et choisissez la commande Package, comme indiqué ci-dessous :

![Empaquetage d'une application avec Visual Studio][vs-package-command]

Quand la création du package est terminée, l'emplacement du package est indiqué dans la fenêtre **Sortie**. Notez que l'étape de création du package se produit automatiquement quand vous déployez ou déboguez votre application dans Visual Studio.

### Test du package

Vous pouvez vérifier la structure du package localement via PowerShell à l’aide de la commande **Test-ServiceFabricApplicationPackage**. Cette commande vérifie la présence de problèmes liés à l’analyse du manifeste, ainsi que toutes les références. Notez que cette commande vérifie uniquement l’exactitude structurelle des répertoires et des fichiers dans le package. Elle ne vérifie pas le code ou les données du contenu du package, mais uniquement si tous les fichiers nécessaires sont présents.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Cette erreur indique que le fichier *MySetup.bat* référencé dans le manifeste de service **SetupEntryPoint** est manquant dans le package de code. Une fois le fichier manquant ajouté, la phase de vérification est terminée :

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

[Déployer et supprimer des applications][10]

[Gestion des paramètres d’application pour plusieurs environnements][11]

[RunAs : exécution d'une application Service Fabric avec des autorisations de sécurité différentes][12]

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
[vs-package-command]: ./media/service-fabric-application-model/vs-package-command.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-manage-multiple-environment-app-configuration.md
[12]: service-fabric-application-runas-security.md

<!---HONumber=AcomDC_0817_2016-->