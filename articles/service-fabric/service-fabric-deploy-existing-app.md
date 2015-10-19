<properties
   pageTitle="Déploiement d’une application existante dans Azure Service Fabric | Microsoft Azure"
   description="Procédure pas à pas pour empaqueter une application existante afin de la déployer sur un cluster Azure Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="claudioc"/>

# Déploiement d’une application existante dans Azure Service Fabric

Azure Service Fabric peut être utilisé pour déployer des applications existantes, en leur permettant de bénéficier de l’analyse du fonctionnement et de la gestion du cycle de vie des applications (ALM).

Ce didacticiel décrit le processus et les concepts de base impliqués dans la sélection et l’empaquetage d’une application existante pour le déploiement dans un cluster Service Fabric.


## Rapide vue d'ensemble des fichiers manifeste d’application et de service

Avant d’entrer dans les détails du déploiement d’une application existante, il est utile de comprendre le modèle de déploiement Service Fabric. Le modèle de déploiement Service Fabric repose principalement sur deux fichiers :


* **Manifeste d’application**

  Le manifeste d'application est utilisé pour décrire l'application et répertorie les services qui la composent ainsi que d’autres paramètres, comme le nombre d'instances, utilisés pour définir la manière dont les services doivent être déployés. Dans l’univers Service Fabric, une application représente l'unité pouvant être mise à niveau. Une application peut être mise à niveau comme une unité unique dans laquelle les défaillances potentielles (et les restaurations potentielles) sont gérées par la plateforme pour garantir que la mise à niveau réussit complètement ou, si elle échoue, ne laisse pas l'application dans un état inconnu/instable.

  Voici un exemple de manifeste d'application :

  ```xml <?xml version="1.0" encoding="utf-8"?> <ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
      <ConfigOverrides />
    </ServiceManifestImport>

    <DefaultServices>
      <Service Name="actor2">
        <StatelessService ServiceTypeName="actor2Type">
          <SingletonPartition />
        </StatelessService>
      </Service>
    </DefaultServices>

  </ApplicationManifest> ```

* **Manifeste de service**

  Un manifeste de service décrit les composants d'un service. Il comprend des données telles que le nom et le type de service (informations que Service Fabric utilise pour gérer le service), son code, les composants de configuration et de données ainsi que des paramètres supplémentaires permettant de configurer le service une fois qu'il est déployé. Nous n’étudierons pas en détail tous les différents paramètres disponibles dans le manifeste de service. Nous examinerons le sous-ensemble requis pour s'exécuter une application existante sur Service Fabric

  Voici un exemple de manifeste de service

  ```xml <?xml version="1.0" encoding="utf-8"?> <ServiceManifest Name="actor2Pkg" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"> <ServiceTypes> <StatelessServiceType ServiceTypeName="actor2Type" /> </ServiceTypes>

    <CodePackage Name="Code" Version="1.0.0.0">
      <EntryPoint>
        <ExeHost>
          <Program>actor2.exe</Program>
        </ExeHost>
      </EntryPoint>
    </CodePackage>

    <ConfigPackage Name="Config" Version="1.0.0.0" />

    <Resources>
      <Endpoints>
        <Endpoint Name="ServiceEndpoint" />
      </Endpoints>
    </Resources>
  </ServiceManifest> ```

## Structure de fichiers d’un package d’application
Pour déployer une application à l'aide, par exemple, des applets de commande powershell, l'application doit respecter une structure de répertoires prédéfinie.

```
\applicationmanifest.xml
\MyServicePkg
    \servicemanifest.xml
    \code
    \config
    \data
```

La racine contient le fichier applicationmanifest.xml qui définit l'application. Un sous-répertoire pour chaque service inclus dans l'application est utilisé pour contenir tous les artefacts nécessaires au service : le fichier servicemanifest.xml et généralement 3 répertoires :

- *code* : contient le code du service.
- *config* : contient un fichier settings.xml (et d’autres fichiers si nécessaire) auquel le service peut accéder à l’exécution pour récupérer des paramètres de configuration spécifiques.
- *data* : un répertoire supplémentaire pour stocker des données locales supplémentaires dont le service peut avoir besoin. Remarque : le répertoire Data doit être utilisé pour stocker uniquement des données éphémères ; Service Fabric ne copie/réplique pas les modifications dans le répertoire des données si le service doit être déplacé, par exemple, pendant le basculement.

Remarque : vous pouvez utiliser n'importe quel nom arbitraire pour les répertoires Code, Config et Data. Assurez-vous simplement d’utiliser la même valeur dans le fichier ApplicationManifest.

## Processus d’empaquetage d’une application existante

Le processus d'empaquetage d'une application existante est basé sur les étapes suivantes :

- Création de la structure de répertoires du package
- Ajout des fichiers de code et de configuration de l’application
- Mise à jour du fichier de manifeste de service
- Mise à jour du manifeste d’application


### Création de la structure de répertoires du package
Vous pouvez commencer par créer la structure de répertoires, comme décrit ci-dessus. J'ai créé un répertoire et copié les manifestes de d'application et de service à partir d'un projet existant que j'avais créé dans Visual Studio.

![][1] ![][2]


### Ajout des fichiers de code et de configuration de l’application
Après avoir créé la structure de répertoires, vous pouvez ajouter les fichiers de code et de configuration de l’application dans les répertoires Code et Config. Vous pouvez également créer d'autres répertoires ou sous-répertoires dans les répertoires Code ou Config. Service Fabric crée une copie xcopy du contenu du répertoire racine de l’application. Par conséquent, il n'existe aucune structure prédéfinie à utiliser autre que la création des deux principaux répertoires Code et Config (mais vous pouvez choisir des noms différents si vous le souhaitez ; consultez la section suivante pour plus de détails).

>[AZURE.NOTE]Veillez à bien inclure la totalité des fichiers/dépendances nécessaires à l’application. Service Fabric copiera le contenu du package de l'application sur tous les nœuds du cluster où les services de l'application seront déployés. Le package doit contenir tout le code nécessaire à l’exécution de l'application. Il n'est pas recommandé de supposer que les dépendances sont déjà installées.

### Modification du fichier de manifeste de service
L’étape suivante consiste à modifier le fichier de manifeste de service afin d’inclure les informations suivantes :

- Le nom du type de service. Il s’agit d’un « ID » que Service Fabric utilise pour identifier un service.
- La commande à utiliser pour lancer l’application (ExeHost).
- N’importe quel script qui doit être exécuté pour installer/configurer l’application (SetupEntrypoint).

Voici un exemple de fichier `servicemanifest.xnml` qui crée un package à partir d’une application node.js :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
  </ServiceTypes>

  <CodePackage Name="Code" Version="1.0.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>node.exe</Program>
        <Arguments>server.js</Arguments>
        <WorkingFolder>CodeBase</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0.0"/>

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>

</ServiceManifest>
```

Examinons les différentes parties du fichier que vous devez mettre à jour :

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

- Vous pouvez choisir n’importe quel nom pour `ServiceTypeName` ; la valeur est utilisée dans le fichier `applicationmanifest.xml` pour identifier le service.
- Vous devez spécifier la valeur `UserImplicitHost = "true"`. Cet attribut indique à Service Fabric que le service est basé sur une application autonome et que la seule opération nécessaire consiste à la lancer en tant que processus et à surveiller son intégrité.

### CodePackage
CodePackage spécifie l'emplacement (et la version) du code du service.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

L’élément `Name` sert à spécifier le nom du répertoire dans le package d’application qui contient le code du service. `CodePackage` contient également l’attribut `version` qui peut être utilisé pour spécifier la version du code et, potentiellement, pour mettre à niveau le code du service en utilisant l’infrastructure ALM de Service Fabric.


### Entrypoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```


L’élément `Entrypoint` dans le fichier de manifeste de service sert à spécifier comment lancer le service. L’élément `ExeHost` spécifie le fichier exécutable (et les arguments) à utiliser pour lancer le service.

- `Program` : spécifie le nom du fichier exécutable qui doit être exécuté pour démarrer le service.
- `Arguments` : spécifie les arguments qui doivent être transmis au fichier exécutable. Il peut s’agir d’une liste de paramètres avec des arguments.
- `WorkingFolder` : spécifie le répertoire de travail pour le processus qui va être démarré. Vous pouvez spécifier deux valeurs :
	- `CodeBase` : le répertoire de travail sera défini sur le répertoire Code dans le package d’application (répertoire `Code` dans la structure ci-dessous).
	- `CodePackage` : le répertoire de travail sera défini sur la racine du package d’application (`MyServicePkg`).
- L’élément `WorkingDirectory` est utile pour définir le répertoire de travail correct afin que des chemins d’accès relatifs puissent être utilisés par l’application ou des scripts d’initialisation.

Il existe également une autre valeur que vous pouvez spécifier pour l’élément `WorkingFolder` (`Work`), mais elle n’est pas très utile pour un scénario avec une application existante.


```
\applicationmanifest.xml
\MyServicePkg
	\servicemanifest.xml
	\code
		 \bin
			  \ ...
	\config
	\data
		\...
```


#### Le point d'entrée de la configuration

```xml
<SetupEntryPoint>
  <ExeHost>
    <Program>scripts\myAppsetup.cmd</Program>
  </ExeHost>
</SetupEntryPoint>
```

`SetupEntrypoint` sert à spécifier un fichier exécutable ou de commandes qui doit être exécuté avant le lancement du code du service. Cet élément est facultatif et il n’est donc pas nécessaire de l'inclure si aucune initialisation/configuration n’est requise. Le paramètre Entrypoint est exécuté chaque fois que le service est redémarré. Comme il n’existe qu’un seul paramètre SetupEntrypoint, les scripts d’installation/de configuration doivent être regroupés dans un même fichier de commandes si l’installation/la configuration de l’application requiert plusieurs scripts. À l’instar de l’élément `Entrypoint`, l’élément `SetupEntrypoint` peut exécuter n’importe quel type de fichier : fichier exécutable, fichiers de commandes, applet de commande PowerShell. Dans l’exemple ci-dessus, l’élément `SetupEntrypoint` est basé sur un fichier de commandes myAppsetup.cmd qui se trouve dans le sous-répertoire des scripts du répertoire Code (en supposant que `WorkingDirectory` est défini sur `Code`).

## Fichier de manifeste d'application

Une fois que vous avez configuré le fichier `servicemanifest.xml`, vous devez apporter des modifications au fichier `applicationmanifest.xml` afin de garantir que le type et le nom du service utilisés sont corrects.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
    </StatelessService>
  </Service>
</DefaultServices>
```

### ServiceManifestImport

Dans `ServiceManifestImport`, vous pouvez spécifier un ou plusieurs services à inclure dans l’application. Les services sont référencés avec `ServiceManifestName`, qui spécifie le nom du répertoire où se trouve le fichier `servicemanifest.xml`.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### DefaultServices

L’élément `DefaultServices` dans le fichier de manifeste d’application sert à définir certaines propriétés du service.

```xml
<DefaultServices>
  <Service Name="actor2">
    <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
    </StatelessService>
  </Service>
</DefaultServices>
```

* `ServiceTypeName` est utilisé comme un « ID » pour le service. Dans le contexte du portage d’une application existante, `ServiceTypeName` doit simplement être un identificateur unique de votre service.
* `StatelessService` : Service Fabric prend en charge deux types de services, sans état et avec état. Dans le cas du portage d’une application existante, le service est un service sans état et `StatelessService` doit donc toujours être utilisé.

Un service Fabric Service peut être déployé dans différentes « configurations ». Par exemple, il peut être déployé comme une ou plusieurs instances, ou de manière à ce qu'il existe une seule instance du service sur chaque nœud du cluster Service Fabric. Dans le fichier `applicationmanifest.xml`, vous pouvez spécifier la façon dont l’application sera déployée.

* `InstanceCount` : permet de spécifier le nombre d’instances du service qui doivent être lancées dans le cluster Service Fabric. Vous pouvez définir la valeur `InstanceCount` selon le type d’application que vous déployez. Les deux scénarios les plus courants sont :

	* `InstanCount = "1"` : dans ce cas, une seule instance du service sera déployée sur le cluster. Le planificateur de Service Fabric détermine le nœud sur lequel le service sera déployé. Une seule instance peut s'avérer utile pour les applications qui nécessitent une configuration différente si elles s'exécutent sur plusieurs instances. Dans ce cas, il est plus facile de définir plusieurs services dans le même fichier de manifeste d’application et d’utiliser `InstanceCount = "1"`. Par conséquent, nous obtiendrons finalement plusieurs instances du même service, mais chacune avec une configuration spécifique. Une valeur `InstanceCount` supérieure à 1 n’est utile que si l’objectif est d’avoir plusieurs instances d’une configuration rigoureusement identique.

	* `InstanceCount ="-1"` : dans ce cas, une seule instance du service sera déployée sur chaque nœud du cluster Service Fabric. Nous obtiendrons finalement une seule (et unique) instance du service pour chaque nœud du cluster. Cette configuration est utile pour les applications frontales (par exemple, un point de terminaison REST) car les applications clientes doivent simplement « se connecter » à n’importe quel nœud du cluster afin d'utiliser le point de terminaison. Cette configuration peut également être utilisée lorsque, par exemple, tous les nœuds du cluster Service Fabric sont connectés à un équilibreur de charge pour permettre de distribuer le trafic client sur le service en cours d'exécution sur tous les nœuds du cluster.


### Test
Pour une application existante, il est très utile de pouvoir consulter les journaux de la console afin de déterminer si les scripts de l'application et de configuration affichent une erreur. Une redirection de la console peut être configurée dans le fichier `servicemanifest.xml` à l’aide de l’élément `ConsoleRedirection`.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>server.js</Arguments>
    <WorkingFolder></WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* Une `ConsoleRedirection` peut être utilisée pour rediriger la sortie de la console (stdout et stderr) vers un répertoire de travail afin qu’elle puisse être utilisée pour vérifier qu’aucune erreur ne s’est produite au cours de l’installation ou de l’exécution de l’application dans le cluster Service Fabric.

	* `FileRetentionCount` détermine le nombre de fichiers enregistrés dans le répertoire de travail. La valeur 5, par exemple, signifie que les fichiers journaux des 5 précédentes exécutions sont stockés dans le répertoire de travail.
	* `FileMaxSizeInKb` spécifie la taille maximale des fichiers journaux.

Les fichiers journaux sont enregistrés dans un des répertoires de travail du service. Pour déterminer l’emplacement des fichiers, vous devez utiliser l'Explorateur de Fabric Service pour savoir dans quel nœud le service s'exécute et quel est le répertoire de travail actuellement utilisé.

Dans l'Explorateur de Service Fabric, identifiez le nœud sur lequel le service s'exécute.

![][3]

Une fois que vous avez identifié le nœud sur lequel le service est actuellement exécuté, vous pouvez déterminer le répertoire de travail utilisé

![][4]

Lorsque vous sélectionnez le nom du service, le volet droit indique où sont stockés le code de service et les paramètres

![][5]

Si vous cliquez sur le lien du champ Disk Location, vous pouvez accéder au répertoire dans lequel les services sont exécutés.

![][6]

Le répertoire de journaux contient tous les fichiers journaux.

Remarque : cet exemple montre le cas d'une seule instance du service en cours d'exécution dans le cluster. S'il existe plusieurs instances, vous devrez peut-être consulter le fichier journal sur tous les nœuds où le service est en cours d'exécution.


## Étapes suivantes
Nous travaillons actuellement sur un outil capable d’empaqueter une application existante simplement en la pointant vers la racine de la structure de répertoires de l'application. L'outil se charge de générer les fichiers manifestes et de configurer les paramètres de base nécessaires pour « transformer » l’application en service Fabric Service.

Consultez [ceci](service-fabric-develop-your-service-index.md) si vous souhaitez plus d’informations sur la façon de développer une application Service Fabric traditionnelle.

[1]: ./media/service-fabric-deploy-existing-app/directory-structure-1.png
[2]: ./media/service-fabric-deploy-existing-app/directory-structure-2.png
[3]: ./media/service-fabric-deploy-existing-app/service-node-1.png
[4]: ./media/service-fabric-deploy-existing-app/service-node-2.png
[5]: ./media/service-fabric-deploy-existing-app/service-node-3.png
[6]: ./media/service-fabric-deploy-existing-app/service-node-4.png

<!---HONumber=Oct15_HO2-->