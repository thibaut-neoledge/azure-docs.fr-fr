<properties
   pageTitle="Déploiement d’un exécutable existant dans Azure Service Fabric | Microsoft Azure"
   description="Procédure pas à pas pour empaqueter une application existante en tant que fichier exécutable invité afin de la déployer sur un cluster Azure Service Fabric"
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
   ms.workload="na"
   ms.date="06/06/2016"
   ms.author="bscholl;mikhegn"/>

# Déploiement d'un exécutable invité dans Service Fabric

Vous pouvez exécuter n’importe quel type d’application, comme Node.js, Java ou des applications natives dans Azure Service Fabric. Dans la terminologie Service Fabric, ces types d’applications sont appelés invités exécutables. Les invités exécutables sont traités par le Service Fabric comme des services sans état. En conséquence, ils seront placés dans des nœuds dans un cluster, sur la base de la disponibilité et d’autres données. Cet article décrit comment empaqueter et déployer un exécutable invité dans un cluster Service Fabric, en utilisant Visual Studio ou un utilitaire en ligne de commande

## Avantages de l'exécution d'un exécutable invité dans Service Fabric

L’exécution d'un exécutable invité dans un cluster Service Fabric présente plusieurs avantages :

- Haute disponibilité : Les applications qui sont exécutées dans Service Fabric sont hautement disponibles dès le départ. Service Fabric s’assure qu’une instance d’une application est toujours en cours d’exécution.
- Analyse du fonctionnement. La fonction d’analyse du fonctionnement de Service Fabric détecte si l’application est en cours d’exécution et fournit des informations de diagnostic en cas d’échec.   
- Gestion du cycle de vie des applications. Outre les mises à niveau sans interruption de service, Service Fabric permet également de revenir à la version précédente, si un problème est survenu pendant la mise à niveau.    
- Densité. Vous pouvez exécuter plusieurs applications dans un cluster, ce qui élimine le besoin d’exécuter chaque application sur son propre matériel.

Dans cet article, nous abordons les étapes de base pour empaqueter un exécutable invité et le déployer dans Service Fabric.

## Vue d’ensemble rapide des fichiers du manifeste de service et d’application.

Dans le cadre du déploiement d’un exécutable invité, il est utile de comprendre le modèle d’empaquetage et de déploiement Service Fabric. Le modèle d’empaquetage et de déploiement Service Fabric repose principalement sur deux fichiers XML : les manifestes d’applications et de services. La définition de schéma pour les fichiers ServiceManifest.xml et ApplicationManifest.xml est installée avec le Kit de développement logiciel (SDK) Service Fabric et les outils sous *C:\\Program Files\\Microsoft SDKs\\Service Fabric\\schemas\\ServiceFabricServiceModel.xsd*.

* **Manifeste d’application**

  Le manifeste d’application permet de décrire l’application. Il répertorie les services qui la composent ainsi que d’autres paramètres utilisés pour définir la manière dont les services doivent être déployés (comme le nombre d’instances).

  Dans l’univers Service Fabric, une application représente l’unité pouvant être mise à niveau. Une application peut être mise à niveau en tant qu’une unité simple où les défaillances potentielles (et les restaurations potentielles) sont gérées par la plateforme. La plateforme garantit que la réussite complète de la mise à niveau ou, en cas d’échec, elle fait en sorte que l’application ne reste pas dans un état inconnu / instable.

* **Manifeste de service**

  Le manifeste de service décrit les composants d'un service. Il inclut des données, telles que le nom et le type de service (c’est-à-dire les informations utilisées par Service Fabric pour gérer le service) et ses composants de code, de configuration et de données. Le manifeste de service inclut également des paramètres supplémentaires qui peuvent être utilisés pour configurer le service après son déploiement.

  Nous n’entrerons pas dans les détails de tous les paramètres disponibles dans le manifeste de service. Nous allons examiner le sous-ensemble nécessaire pour exécuter un exécutable invité sur Service Fabric.

## Structure de fichier d'un package d'application
Pour déployer une application dans Service Fabric, l'application doit respecter une structure de répertoires prédéfinie. Vous trouverez ci-dessous un exemple de cette structure :

```
|-- ApplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|-- Settings.xml
  |-- data    
  |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

La racine contient le fichier ApplicationManifest.xml qui définit l'application. Un sous-répertoire pour chaque service inclus dans l’application est utilisé pour contenir tous les artefacts nécessaires au service : le fichier ServiceManifest.xml et généralement les trois répertoires suivants :

- *Code*. Ce répertoire contient le code du service.
- *Config*. Ce répertoire contient un fichier Settings.xml (et d’autres fichiers si nécessaire) auquel le service peut accéder lors de l’exécution pour récupérer des paramètres de configuration spécifiques.
- *Data*. Il s’agit d’un répertoire supplémentaire pour stocker des données locales supplémentaires dont le service peut avoir besoin. Remarque : le répertoire Data doit être utilisé uniquement pour stocker des données éphémères. Service Fabric ne copie / réplique pas les modifications dans le répertoire des données si le service doit être déplacé, par exemple, pendant le basculement.

Remarque : il est inutile de créer les répertoires `config` et `data` si vous n’en avez pas besoin.

## Processus d’empaquetage d’une application existante

Lors de l’empaquetage d’un fichier exécutable invité, vous pouvez choisir d’utiliser modèle de projet Visual Studio ou de créer le package d’application manuellement. À l’aide de Visual Studio, la structure de package d’application et les fichiers manifest sont créés pour vous par l’assistant de nouveau projet. Voir ce qui suit pour un guide détaillé de l’empaquetage d’un fichier exécutable invité avec Visual Studio.

Le processus d’empaquetage manuel d’un exécutable invité est basé sur les étapes suivantes :

1. Créez la structure de répertoires du package.
2. Ajoutez des fichiers de code et de configuration de l’application.
3. Modifiez le fichier de manifeste de service.
4. Modifiez le fichier de manifeste d’application.

>[AZURE.NOTE] Nous fournissons un outil d’empaquetage qui vous permet de créer ApplicationPackage automatiquement. Cet outil est actuellement en version préliminaire. Vous pouvez le télécharger [ici](http://aka.ms/servicefabricpacktool).

### Création de la structure de répertoires du package
Vous pouvez commencer par créer la structure de répertoires, comme décrit précédemment.

### Ajout des fichiers de code et de configuration de l’application
Après avoir créé la structure de répertoires, vous pouvez ajouter les fichiers de code et de configuration de l’application dans les répertoires code et config. Vous pouvez également créer d’autres répertoires ou sous-répertoires dans les répertoires code ou config.

Service Fabric crée une copie xcopy du contenu du répertoire racine de l’application. Par conséquent, il n’existe aucune structure prédéfinie à utiliser, autre que la création des deux principaux répertoires code et paramètres. (Vous pouvez choisir des noms différents si vous le souhaitez. Pour plus d’informations, consultez la section suivante.)

>[AZURE.NOTE] Veillez à bien inclure la totalité des fichiers / dépendances nécessaires à l’application. Service Fabric copiera le contenu du package de l'application sur tous les nœuds du cluster où les services de l'application seront déployés. Le package doit contenir tout le code nécessaire à l’exécution de l'application. Il n’est pas recommandé de supposer que les dépendances sont déjà installées.

### Modification du fichier de manifeste de service
L’étape suivante consiste à modifier le fichier de manifeste de service afin d’inclure les informations suivantes :

- Le nom du type de service. Il s’agit d’un ID que Service Fabric utilise pour identifier un service.
- La commande à utiliser pour lancer l’application (ExeHost).
- N’importe quel script qui doit être exécuté pour installer / configurer l’application (SetupEntrypoint).

Vous trouverez ci-dessous un exemple de fichier `ServiceManifest.xml` :

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

- Vous pouvez choisir n’importe quel nom pour `ServiceTypeName`. La valeur est utilisée dans le fichier `ApplicationManifest.xml` pour identifier le service.
- Vous devez spécifier la valeur `UseImplicitHost="true"`. Cet attribut indique à Service Fabric que le service est basé sur une application autonome et que la seule opération nécessaire consiste à la lancer en tant que processus et à surveiller son intégrité.

### CodePackage
L’élément CodePackage spécifie l’emplacement (et la version) du code du service.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L’élément `Name` est utilisé pour spécifier le nom du répertoire dans le package d’application qui contient le code du service. `CodePackage` a également l’attribut `version`. Cela peut être utilisé pour spécifier la version du code et peut également permettre de mettre à niveau le code de service en utilisant l’infrastructure de gestion du cycle de vie de l’application Service Fabric.
### SetupEntrypoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
L’élément SetupEntrypoint sert à spécifier un fichier exécutable ou de commandes qui doit être exécuté avant le lancement du code du service. Cet élément est facultatif et il n’est donc pas nécessaire de l’inclure si aucune procédure d’initialisation / de configuration n’est requise. SetupEntrypoint est exécuté chaque fois que le service est redémarré.

Comme il n’existe qu’un seul paramètre SetupEntrypoint, les scripts d’installation / de configuration doivent être regroupés dans un même fichier de commandes si l’installation / la configuration de l’application requiert plusieurs scripts. À l’instar de l’élément SetupEntryPoint, l’élément SetupEntrypoint peut exécuter n’importe quel type de fichier : fichiers exécutables, fichiers de commandes et applets de commande PowerShell. Dans l’exemple ci-dessus, l’élément SetupEntrypoint est basé sur un fichier de commandes LaunchConfig.cmd qui se trouve dans le sous-répertoire `scripts` du répertoire Code (en supposant que l’élément WorkingFolder est défini sur Code).

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

L’élément `Entrypoint` dans le fichier de manifeste de service sert à spécifier comment lancer le service. L’élément `ExeHost` spécifie le fichier exécutable (et les arguments) à utiliser pour lancer le service.

- `Program` spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
- `Arguments` spécifie les arguments qui doivent être passés au fichier exécutable. Il peut s’agir d’une liste de paramètres avec des arguments.
- `WorkingFolder` spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier deux valeurs :
	- `CodeBase` indique que le répertoire de travail sera défini selon le répertoire Code, dans le package d’application (répertoire `Code` dans la structure illustrée ci-dessous).
	- `CodePackage` indique que le répertoire de travail sera défini sur la racine du package d’application (`MyServicePkg`).
- `WorkingFolder` est utile pour définir le répertoire de travail correct afin que des chemins d’accès relatifs puissent être utilisés par l’application ou des scripts d’initialisation.

### Points de terminaison

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
L’élément `Endpoint` spécifie les points de terminaison que l’application peut écouter. Dans cet exemple, l’application Node.js écoute sur le port 3000.

## Modifiez le fichier de manifeste d’application

Après avoir configuré le fichier `Servicemanifest.xml`, vous devez apporter des modifications au fichier `ApplicationManifest.xml` afin de garantir que le type et le nom du service utilisés sont corrects.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

Dans l’élément `ServiceManifestImport`, vous pouvez spécifier un ou plusieurs services à inclure dans l’application. Les services sont référencés avec `ServiceManifestName`, qui spécifie le nom du répertoire dans lequel se trouve le fichier `ServiceManifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Configurez la journalisation
Pour les invités exécutables, il est très utile de pouvoir consulter les journaux de la console afin de déterminer si les scripts de l’application et de configuration affichent une erreur. La redirection de la console peut être configurée dans le fichier `ServiceManifest.xml` à l’aide de l’élément `ConsoleRedirection`.

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

* La `ConsoleRedirection` peut être utilisée pour rediriger la sortie de la console (stdout et stderr) vers un répertoire de travail afin de les utiliser pour vérifier qu’aucune erreur ne s’est produite au cours de l’installation ou de l’exécution de l’application dans le cluster Service Fabric.

	* `FileRetentionCount` détermine le nombre de fichiers enregistrés dans le répertoire de travail. La valeur 5, par exemple, signifie que les fichiers journaux des cinq précédentes exécutions sont stockés dans le répertoire de travail.
	* `FileMaxSizeInKb` spécifie la taille maximale des fichiers journaux.

Les fichiers journaux sont enregistrés dans un des répertoires de travail du service. Afin d’identifier l’emplacement des fichiers, vous devez utiliser l’explorateur Service Fabric afin d’identifier le nœud que le service exécute et le répertoire de travail utilisé. Cette procédure est décrite ultérieurement dans cet article.

### Déploiement
La dernière étape consiste à déployer votre application. Le script PowerShell ci-dessous montre comment déployer votre application dans le cluster de développement local et comment démarrer un nouveau service Service Fabric.

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Plusieurs configurations différentes peuvent être utilisées pour déployer un service Service Fabric. Par exemple, il peut être déployé sous la forme d’une ou de plusieurs instances, ou de manière à ce qu’il existe une seule instance du service sur chaque nœud du cluster Service Fabric.

Le paramètre `InstanceCount` de l’applet de commande `New-ServiceFabricService` permet de spécifier le nombre d’instances du service qui doivent être lancées dans le cluster Service Fabric. Vous pouvez définir la valeur `InstanceCount` selon le type d’application que vous déployez. Les deux scénarios les plus courants sont :

* `InstanceCount = "1"`. Dans ce cas, une seule instance du service sera déployée sur le cluster. Le planificateur de Service Fabric détermine le nœud sur lequel le service sera déployé.

* `InstanceCount ="-1"`. Dans ce cas, une seule instance du service sera déployée sur chaque nœud du cluster Service Fabric. Nous obtiendrons finalement une seule (et unique) instance du service pour chaque nœud du cluster.

Cette configuration est utile pour les applications frontales (par exemple, un point de terminaison REST), car les applications clientes doivent simplement « se connecter » à l’un des nœuds du cluster afin d’utiliser le point de terminaison. Cette configuration peut également être utilisée lorsque, par exemple, tous les nœuds du cluster Service Fabric sont connectés à un équilibreur de charge pour permettre de distribuer le trafic client sur le service en cours d’exécution sur tous les nœuds du cluster.

### Vérification de votre application en cours d'exécution

Dans l'Explorateur Service Fabric, identifiez le nœud sur lequel le service s'exécute. Dans cet exemple, il s’exécute sur le nœud 1 :

![Nœud sur lequel le service est en cours d’exécution](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Si vous accédez au nœud et accédez à l’application, vous verrez des informations essentielles sur le nœud, notamment son emplacement sur le disque.

![Emplacement sur le disque](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Si vous accédez au répertoire à l’aide de l’Explorateur de serveurs, vous trouverez le répertoire de travail et le dossier des journaux du service comme indiqué ci-dessous.

![Emplacement du journal](./media/service-fabric-deploy-existing-app/loglocation.png)

## Utilisation de Visual Studio pour empaqueter une application existante

Visual Studio fournit un modèle de service Service Fabric pour vous aider à déployer un fichier exécutable invité sur un cluster Service Fabric. Vous devez suivre les étapes ci-dessous pour terminer la publication :

1. Sélectionnez Fichier -> Nouveau projet pour créer une application Service Fabric
2. Choisissez Exécutable invité comme Modèle de service
3. Cliquez sur Parcourir pour sélectionner le dossier avec votre fichier exécutable et remplissez le reste des paramètres pour créer le service
  - Vous pouvez configurer le *Comportement du package de code* de sorte à copier tout le contenu de votre dossier dans le projet Visual Studio, ce qui est utile si l’exécutable ne change pas. Si vous vous attendez à ce que le fichier exécutable soit modifié et souhaitez avoir la possibilité de choisir les nouvelles versions de façon dynamique, vous pouvez choisir de créer un lien vers le dossier à la place.
  - *Programme* spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
  - *Arguments* spécifie les arguments qui doivent être passés au fichier exécutable. Il peut s’agir d’une liste de paramètres avec des arguments.
  - *WorkingFolder* spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier deux valeurs :
  	- *CodeBase* indique que le répertoire de travail sera défini selon le répertoire Code, dans le package d’application (répertoire `Code` dans la structure illustrée ci-dessous).
    - *CodePackage* indique que le répertoire de travail sera défini sur la racine du package d’application (`MyServicePkg`).
4. Donnez un nom à votre service et cliquez sur OK
5. Si votre service a besoin d’un système d’extrémité pour la communication, vous pouvez maintenant ajouter le protocole, le port et le type dans le fichier ServiceManifest.xml (par exemple) : ```<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />```
6. Vous pouvez maintenant essayer le package et l’action de publication sur votre cluster local en effectuant le débogage de la solution dans Visual Studio. Vous pouvez, quand vous le souhaitez, publier l’application sur un cluster à distance ou archiver la solution pour contrôler le code source.

>[AZURE.NOTE] Vous pouvez utiliser les dossiers liés lors de la création du projet d’application dans Visual Studio. Cela permet de créer un lien vers l’emplacement source à partir du projet, ce qui ouvre la possibilité de mettre à jour le fichier exécutable invité dans la destination de la source, en faisant de ces mises à jour une partie intégrante du package d’application de sa création.

## Étapes suivantes
Dans cet article, vous avez appris à empaqueter un exécutable invité et à le déployer dans Service Fabric. Pour continuer, vous pouvez consulter le contenu supplémentaire sur ce sujet.

- [Exemple pour empaqueter et déployer un exécutable invité sur GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication), y compris un lien vers la version préliminaire de l’outil d’empaquetage
- [Déploiement de plusieurs exécutables invités](service-fabric-deploy-multiple-apps.md)
- [Créez votre première application Service Fabric avec Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=AcomDC_0615_2016-->