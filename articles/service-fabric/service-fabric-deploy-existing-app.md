<properties
   pageTitle="Déploiement d'une application personnalisée dans Azure Service Fabric | Microsoft Azure"
   description="Procédure pas à pas pour empaqueter une application existante afin de la déployer sur un cluster Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Déploiement d'une application personnalisée dans Service Fabric

Vous pouvez exécuter n'importe quel type d'application existante, comme Node.js, Java ou des applications natives dans Service Fabric. Service Fabric traite ces applications comme des services sans état et place celles-ci sur les nœuds d'un cluster en fonction de la disponibilité et d'autres mesures. Cet article décrit comment empaqueter et déployer une application existante dans un cluster Service Fabric.

## Avantages de l'exécution d'une application personnalisée dans Service Fabric

Il existe quelques avantages en ce qui concerne l'exécution de l'application dans un cluster Service Fabric :

- Haute disponibilité : les applications qui sont exécutées dans Service Fabric sont hautement disponibles dès le départ. Service Fabric s'assure qu'une instance d'une application est toujours en cours d'exécution.
- Contrôle d'intégrité : l'analyse du fonctionnement Service Fabric détecte si l'application est en cours d'exécution et fournit des informations de diagnostic en cas d'échec.   
- Gestion du cycle de vie des applications : outre les mises à niveau sans interruption de service, Service Fabric permet également de revenir à la version précédente, si un problème est survenu pendant la mise à niveau.    
- Densité : vous pouvez exécuter plusieurs applications dans un cluster, ce qui élimine le besoin pour chaque application de s'exécuter sur son propre matériel.

Dans cet article, nous abordons les étapes de base pour empaqueter une application existante et la déployer dans Service Fabric.


## Rapide vue d'ensemble des fichiers manifeste d’application et de service

Avant d'entrer dans les détails du déploiement d'une application existante, il est utile de comprendre le modèle d'empaquetage et de déploiement Service Fabric. Le modèle d'empaquetage et de déploiement Service Fabric repose principalement sur deux fichiers :


* **Manifeste d’application**

  Le manifeste d'application est utilisé pour décrire l'application et répertorie les services qui la composent ainsi que d'autres paramètres, comme le nombre d'instances, utilisés pour définir la manière dont les services doivent être déployés. Dans l'univers Service Fabric, une application représente l'unité pouvant être mise à niveau. Une application peut être mise à niveau comme une unité unique dans laquelle les défaillances potentielles (et les restaurations potentielles) sont gérées par la plateforme pour garantir que la mise à niveau réussit complètement ou, si elle échoue, ne laisse pas l'application dans un état inconnu/instable.


* **Manifeste de service**

  Le manifeste de service décrit les composants d'un service. Il comprend des données telles que le nom et le type de service (informations que Service Fabric utilise pour gérer le service), son code, les composants de configuration et de données ainsi que des paramètres supplémentaires permettant de configurer le service une fois qu'il est déployé. Nous n’étudierons pas en détail tous les différents paramètres disponibles dans le manifeste de service. Nous examinerons le sous-ensemble requis pour s'exécuter une application existante sur Service Fabric


## Structure de fichier d'un package d'application
Pour déployer une application dans Service Fabric, l'application doit respecter une structure de répertoires prédéfinie. Vous trouverez ci-dessous un exemple de cette structure :

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

La racine contient le fichier ApplicationManifest.xml qui définit l'application. Un sous-répertoire pour chaque service inclus dans l'application est utilisé pour contenir tous les artefacts nécessaires au service : le fichier ServiceManifest.xml et généralement 3 répertoires :

- *code* : contient le code du service.
- *config* : contient un fichier settings.xml (et d'autres fichiers si nécessaire) auquel le service peut accéder à l'exécution pour récupérer des paramètres de configuration spécifiques.
- *data* : un répertoire supplémentaire pour stocker des données locales supplémentaires dont le service peut avoir besoin. Remarque : le répertoire Data doit être utilisé pour stocker uniquement des données éphémères ; Service Fabric ne copie/réplique pas les modifications dans le répertoire des données si le service doit être déplacé, par exemple, pendant le basculement.

Remarque : inutile de créer les répertoires `config` et `data` si vous n'en avez pas besoin.

## Processus d’empaquetage d’une application existante

Le processus d'empaquetage d'une application existante est basé sur les étapes suivantes :

- Création de la structure de répertoires du package
- Ajout des fichiers de code et de configuration de l'application
- Mise à jour du fichier de manifeste de service
- Mise à jour du manifeste d’application

>[AZURE.NOTE]\: nous fournissons un outil d'empaquetage qui vous permet de créer ApplicationPackage automatiquement. Cet outil est actuellement en version préliminaire. Vous pouvez le télécharger [ici](http://aka.ms/servicefabricpacktool).

### Création de la structure de répertoires du package
Vous pouvez commencer par créer la structure de répertoires, comme décrit auparavant.

### Ajout des fichiers de code et de configuration de l’application
Après avoir créé la structure de répertoires, vous pouvez ajouter les fichiers de code et de configuration de l'application dans les répertoires code et config. Vous pouvez également créer d'autres répertoires ou sous-répertoires dans les répertoires code ou config. Service Fabric crée une copie xcopy du contenu du répertoire racine de l'application. Par conséquent, il n'existe aucune structure prédéfinie à utiliser autre que la création des deux principaux répertoires code et config (mais vous pouvez choisir des noms différents si vous le souhaitez ; consultez la section suivante pour plus de détails).

>[AZURE.NOTE]Veillez à bien inclure la totalité des fichiers/dépendances nécessaires à l’application. Service Fabric copiera le contenu du package de l'application sur tous les nœuds du cluster où les services de l'application seront déployés. Le package doit contenir tout le code nécessaire à l’exécution de l'application. Il n'est pas recommandé de supposer que les dépendances sont déjà installées.

### Modification du fichier de manifeste de service
L’étape suivante consiste à modifier le fichier de manifeste de service afin d’inclure les informations suivantes :

- Le nom du type de service. Il s’agit d’un « ID » que Service Fabric utilise pour identifier un service.
- La commande à utiliser pour lancer l’application (ExeHost).
- N'importe quel script qui doit être exécuté pour installer/configurer l'application (SetupEntrypoint).

Vous trouverez ci-dessous un exemple de `ServiceManifest.xml` :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Examinons les différentes parties du fichier que vous devez mettre à jour :

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Vous pouvez choisir n'importe quel nom pour `ServiceTypeName` ; la valeur est utilisée dans le fichier `ApplicationManifest.xml` pour identifier le service.
- Vous devez spécifier la valeur `UseImplicitHost="true"`. Cet attribut indique à Service Fabric que le service est basé sur une application autonome et que la seule opération nécessaire consiste à la lancer en tant que processus et à surveiller son intégrité.

### CodePackage
CodePackage spécifie l'emplacement (et la version) du code du service.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L'élément `Name` sert à spécifier le nom du répertoire dans le package d'application qui contient le code du service. `CodePackage` contient également l'attribut `version` qui peut être utilisé pour spécifier la version du code et, potentiellement, pour mettre à niveau le code du service en utilisant l'infrastructure ALM de Service Fabric.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntrypoint sert à spécifier un fichier exécutable ou de commandes qui doit être exécuté avant le lancement du code du service. Cet élément est facultatif et il n’est donc pas nécessaire de l'inclure si aucune initialisation/configuration n’est requise. SetupEntrypoint est exécuté chaque fois que le service est redémarré. Comme il n’existe qu’un seul paramètre SetupEntrypoint, les scripts d’installation/de configuration doivent être regroupés dans un même fichier de commandes si l’installation/la configuration de l’application requiert plusieurs scripts. À l'instar de l'élément Entrypoint, l'élément SetupEntrypoint peut exécuter n'importe quel type de fichier : fichier exécutable, fichiers de commandes, applet de commande PowerShell. Dans l'exemple ci-dessus, l'élément SetupEntrypoint est basé sur un fichier de commandes launchConfig.cmd qui se trouve dans le sous-répertoire `scripts` du répertoire Code (en supposant que l'élément WorkingDirectory est défini sur Code).

### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

L'élément `Entrypoint` dans le fichier de manifeste de service sert à spécifier comment lancer le service. L'élément `ExeHost` spécifie le fichier exécutable (et les arguments) à utiliser pour lancer le service.

- `Program` : spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
- `Arguments` : spécifie les arguments qui doivent être transmis au fichier exécutable. Il peut s'agir d'une liste de paramètres avec des arguments.
- `WorkingFolder` : spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier deux valeurs :
	- `CodeBase` : le répertoire de travail sera défini sur le répertoire Code dans le package d'application (répertoire `Code` dans la structure ci-dessous).
	- `CodePackage` : le répertoire de travail sera défini sur la racine du package d'application (`MyServicePkg`).
- L'élément `WorkingDirectory` est utile pour définir le répertoire de travail correct afin que des chemins d'accès relatifs puissent être utilisés par l'application ou des scripts d'initialisation.

### Points de terminaison

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
L'élément `Endpoint` spécifie les points de terminaison que l'application peut écouter. Dans cet exemple, l'application Node.js écoute sur le port 3000.

## Fichier de manifeste d'application

Une fois que vous avez configuré le fichier `servicemanifest.xml`, vous devez apporter des modifications au fichier `ApplicationManifest.xml` afin de garantir que le type et le nom du service utilisés sont corrects.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

Dans `ServiceManifestImport`, vous pouvez spécifier un ou plusieurs services à inclure dans l'application. Les services sont référencés avec `ServiceManifestName`, qui spécifie le nom du répertoire où se trouve le fichier `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configuration de la journalisation
Pour une application existante, il est très utile de pouvoir consulter les journaux de la console afin de déterminer si les scripts de l'application et de configuration affichent une erreur. Une redirection de la console peut être configurée dans le fichier `ServiceManifest.xml` à l'aide de l'élément `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* Une `ConsoleRedirection` peut être utilisée pour rediriger la sortie de la console (stdout et stderr) vers un répertoire de travail afin qu'elle puisse être utilisée pour vérifier qu'aucune erreur ne s'est produite au cours de l'installation ou de l'exécution de l'application dans le cluster Service Fabric.

	* `FileRetentionCount` détermine le nombre de fichiers enregistrés dans le répertoire de travail. La valeur 5, par exemple, signifie que les fichiers journaux des 5 précédentes exécutions sont stockés dans le répertoire de travail.
	* `FileMaxSizeInKb` spécifie la taille maximale des fichiers journaux.

Les fichiers journaux sont enregistrés dans un des répertoires de travail du service. Pour déterminer l’emplacement des fichiers, vous devez utiliser l'Explorateur de Fabric Service pour savoir dans quel nœud le service s'exécute et quel est le répertoire de travail actuellement utilisé. La procédure est couverte plus loin dans cet article.

### Déploiement
La dernière étape consiste à déployer votre application. Le script PowerShell ci-dessous montre comment déployer votre application dans le cluster de développement local et démarrer un nouveau service Service Fabric.

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Un service Fabric Service peut être déployé dans différentes « configurations ». Par exemple, il peut être déployé comme une ou plusieurs instances, ou de manière à ce qu'il existe une seule instance du service sur chaque nœud du cluster Service Fabric.

Le paramètre `InstanceCount` de l'applet de commande `New-ServiceFabricService` permet de spécifier le nombre d'instances du service qui doivent être lancées dans le cluster Service Fabric. Vous pouvez définir la valeur `InstanceCount` selon le type d'application que vous déployez. Les deux scénarios les plus courants sont : * `InstanCount = "1"` : dans ce cas, une seule instance du service sera déployée sur le cluster. Le planificateur de Service Fabric détermine le nœud sur lequel le service sera déployé.

* `InstanceCount ="-1"` : dans ce cas, une seule instance du service sera déployée sur chaque nœud du cluster Service Fabric. Nous obtiendrons finalement une seule (et unique) instance du service pour chaque nœud du cluster. Cette configuration est utile pour les applications frontales (par exemple, un point de terminaison REST) car les applications clientes doivent simplement « se connecter » à n’importe quel nœud du cluster afin d'utiliser le point de terminaison. Cette configuration peut également être utilisée lorsque, par exemple, tous les nœuds du cluster Service Fabric sont connectés à un équilibreur de charge pour permettre de distribuer le trafic client sur le service en cours d'exécution sur tous les nœuds du cluster.

### Vérification de votre application en cours d'exécution

Dans l'Explorateur Service Fabric, identifiez le nœud sur lequel le service s'exécute. Dans cet exemple, il s'exécute sur le nœud 1 :

![application en cours d'exécution](./media/service-fabric-deploy-existing-app/runningapplication.png)

Si vous accédez au nœud et accédez à l'application, vous verrez des informations essentielles sur le nœud, notamment son emplacement sur le disque.

![emplacement sur le disque](./media/service-fabric-deploy-existing-app/locationondisk.png)

Si vous accédez au répertoire à l'aide de l'Explorateur de serveurs, vous trouverez le répertoire de travail et le dossier des journaux du service comme indiqué ci-dessous.

![emplacement sur le disque](./media/service-fabric-deploy-existing-app/loglocation.png)


## Étapes suivantes
Dans cet article, vous avez appris à empaqueter une application existante et la déployer dans Service Fabric. Pour continuer, vous pouvez consulter du contenu supplémentaire sur ce sujet.

- Exemple pour empaqueter et déployer une application personnalisée sur [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Custom/SimpleApplication), y compris un lien vers la version préliminaire de l'outil d'empaquetage.
- Découvrez comment [déployer plusieurs applications personnalisées](service-fabric-deploy-multiple-apps.md).
- Procédure de mise en route avec la [création de votre première application Service Fabric à l'aide de Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

<!---HONumber=AcomDC_1125_2015-->