<properties
   pageTitle="Configuration d’un cluster Service Fabric à partir du portail Azure | Microsoft Azure"
   description="Configuration d’une infrastructure de cluster Service Fabric à partir du portail Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="chackdan"/>

# Configuration d’une infrastructure de cluster Service Fabric à partir du portail Azure

Cette page vous permet de configurer un cluster de l’infrastructure de service. Cela suppose que votre abonnement comporte suffisamment de cœurs pour déployer les machines virtuelles IaaS qui composeront ce cluster


## Création du cluster

1. Connectez-vous au portail Azure [http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal).

2. Cliquez sur **+ New** pour ajouter un nouveau modèle de ressources. Recherchez notre modèle sur Marketplace sous Tout. Il est appelé **Service Fabric Cluster** (vous pouvez naviguer vers **Tout** en cliquant sur la catégorie de niveau supérieur - Marketplace > puis recherchez « Fabric » sous « Tout » et appuyez sur Entrée. ll arrive que le filtre automatique ne fonctionne pas, alors assurez-vous d’avoir **appuyé sur entrée**) ![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. Sélectionnez « Service Fabric Cluster » dans la liste
4. Accédez au panneau de Cluster Service Fabric, cliquez sur **créer** et fournissez des détails sur votre cluster
5. Créer un **nouveau groupe de ressources (RG)** - et en aire la même chose que le nom de cluster. Il est préférable de les rechercher ultérieurement. Cela s’avère particulièrement utile lorsque vous essayez d’apporter des modifications à votre déploiement et/ou de supprimer votre cluster.

  	Remarque : bien que vous puissiez décider d’utiliser un groupe de ressources, il est conseillé de créer un nouveau groupe de ressources. Cela facilite la suppression des clusters dont vous n’avez pas besoin

 	 ![CreateRG][CreateRG]


6. Veillez à sélectionner l’**abonnement** sur lequel vous souhaitez que votre cluster soit déployé, surtout si vous avez des abonnements multiples...

7. Sélectionnez un **emplacement** dans la liste déroulante (si vous souhaitez le créer un autre emplacement, dans le cas contraire, il prendra par défaut la valeur des États-Unis de l’ouest)

8. Configurer votre **Type de nœud**. Le Type de nœud peut être considéré comme équivalent à « Rôles » dans les Services de cloud. Ils définissent les tailles de machine virtuelle, le nombre de machines virtuelles et ses propriétés. Votre cluster peut avoir plusieurs types de nœud. La seule contrainte est que vous devez disposer d’au moins un type de nœud (primaire, ou le premier que vous définissez sur le portail) d’au moins 5 machines virtuelles.
	1. Sélectionnez la taille de machine virtuelle/niveau de tarification dont vous avez besoin. (La valeur par défaut est D4 Standard, si vous souhaitez simplement utiliser ce cluster pour tester votre application, il est possible de sélectionner D2 ou une taille inférieure pour la machine virtuelle)	
	2. Choisissez le nombre de machines virtuelles. Vous pouvez augmenter ou réduire le nombre de machines virtuelles sur NodeType plus tard. Cependant, le type de nœud principal ou le premier type de nœud contenant au moins 5 machines virtuelles
	3. Choisissez un nom pour votre type de nœud (1 à 12 caractères contenant uniquement des lettres et des chiffres)	
	4. Choisissez le nom d’utilisateur du bureau à distance de machine virtuelle et le mot de passe
	5. **Considérations relatives au type de nœud lorsque vous avez plusieurs types de nœud**. Si vous envisagez de déployer un seul cluster de type de nœud, passez à l’étape suivante.

		- Pour expliquer ce concept, prenons un exemple. Si vous voulez déployer une application qui contient un service « Frontal » et un service « Principal » et vous souhaitez placer le service « Frontal » sur les machines virtuelles plus petites (tailles de machine virtuelle comme A2, D2, etc.) dotées de ports ouverts sur Internet et le service « Principal » de calcul intensif sur des machines virtuelles supérieures (avec des tailles de machine virtuelle telles que D4, D6, D12, etc.) qui ne sont pas présentés par Internet.
		- Bien que vous puissiez placer les deux services sur un seul type de nœud NodeType, il est conseillé de les placer dans un cluster avec deux types de nœuds, chacun ayant des propriétés différentes, comme la connectivité internet et la taille et le nombre de machines virtuelles pouvant être mises à l’échelle indépendamment.
		- Définissez le type de nœud qui se terminera en ayant au moins 5 machines virtuelles dans un premier temps. Les autres types de nœud peuvent avoir au moins 1 machine virtuelle.
					

  	![CreateNodeType][CreateNodeType]

9. **Ports d’application** : si vous envisagez de déployer vos applications sur le cluster immédiatement, ajoutez les ports à ouvrir pour vos applications sur ce type de nœud (ou les types de nœud que vous avez créé). Vous pouvez ajouter des ports de Type de nœud ultérieurement en modifiant l’équilibreur de charge associé à ce type de nœud (vous devez ajouter une sonde, puis ajouter la sonde à son tour à l’équilibreur de charge). Le fait de le faire tout de suite peut être un peu plus simple, car l’automatisation du portail ajoutera les sondes et les règles nécessaires à l’équilibreur de charge.
	1. Vous pouvez trouver les ports de l’application dans vos manifestes de service faisant partie du package d’application. Accédez à chacune de vos applications, ouvrez les manifestes de service et prenez note de tous les points de terminaison d’« entrée » dont vos applications ont besoin pour communiquer avec le monde extérieur.
	2. Ajoutez tous les ports dans le champ séparé par des virgules « points de terminaison d’entrée d’Application ». Le point de terminaison de connexion TCP client - 19000 par défaut, vous n’avez pas besoin de les spécifier. Par exemple, mon application a besoin que le port « 83 » soit ouvert. Vous trouverez cela dans le servicemanifest.xml dans votre package d’application (il peut y avoir plusieurs servicemanifest.xml).

  La plupart des applications d’exemple utilisent le port 80 et 8081. Par conséquent, ajoutez-les si vous envisagez de déployer les exemples sur ce cluster.

  ![Ports][Ports]



1. **Facultatif : propriétés de sélection élective** : il est inutile d’ajouter des configurations ici. Une propriété de sélection élective de « NodeTypeName » est ajoutée par le système. Vous pouvez choisir d’en ajouter davantage si votre application en a besoin. 

  
## Configuration de sécurité

À ce stade, Fabric Service Fabric prend uniquement en charge les clusters de sécurisation via un certificat X509. Avant de commencer ce processus, vous devez télécharger votre certificat sur KeyVault. Reportez-vous à [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md) pour plus d’informations sur la procédure à suivre.

La sécurisation de votre cluster est facultative, mais elle est fortement recommandée. Si vous choisissez de ne pas sécuriser votre cluster, vous devez placer le commutateur Mode de sécurité sur « Aucun ».

Les détails sur les considérations de sécurité et la façon dont les procédures sont documentées dans [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)

![SecurityConfigs][SecurityConfigs]



## Facultatif : configuration des diagnostics

Par défaut, les diagnostics sont activés sur votre cluster afin de faciliter la résolution des problèmes. Si vous souhaitez désactiver le diagnostic :

1. Accédez au panneau Configurations de diagnostic.

2. Placez la bascule à l’état inactif (Off).

## Facultatifs : Paramètres Fabric

Il s’agit d’une option avancée qui vous permet de modifier les paramètres par défaut du service de cluster de fabric. Il est recommandé de **ne pas modifier** les valeurs par défaut, sauf si vous êtes certain que votre application et/ou le Cluster doit l’être.

## Achèvement de la création du cluster
Vous pouvez cliquer sur le **Résumé** pour voir les configurations que vous avez fournies ou télécharger le modèle ARM qui sera utilisé pour déployer votre cluster. Après avoir fourni les paramètres obligatoires, le bouton Créer est activé et vous pouvez démarrer le processus de création de cluster.
 

Vous pouvez consulter la progression dans NOTIFICATIONS (cliquez sur l’icône « Cloche » située à côté de la barre d’état vers la droite de l’écran). Si vous aviez cliqué sur « Épingler au tableau d’accueil » lors de la création du cluster, vous verrez « Déploiement Service Fabric de Cluster » épinglé à la carte de démarrage

![Notifications][Notifications]

## Affichage de l’état de votre cluster

Une fois votre déploiement terminé, vous pouvez inspecter votre cluster dans le portail.

1. Accédez à **Parcourir**, puis cliquez sur la ressource - **Service Fabric Clusters**.

2. Recherchez votre cluster et cliquez dessus.

  ![BrowseCluster][BrowseCluster]

3. Vous pouvez maintenant voir les détails de votre cluster dans le tableau de bord, notamment l’adresse IP publique du cluster. Notez que le fait de survoler l’**Adresse IP publique du cluster** affiche un presse-papiers sur lequel vous pouvez cliquer pour effectuer la copie.

  ![ClusterDashboard][ClusterDashboard]

  La partie du moniteur de nœud sur le panneau de tableau de bord du cluster, indique le nombre de machines virtuelles en état de marche ou non. Vous trouverez plus de détails sur l’intégrité dans la section [présentation du modèle d’état Service Fabric](service-fabric-health-introduction.md) de notre documentation


## Connexion au cluster et déploiement d’une application

Avec la configuration de cluster, vous pouvez maintenant vous connecter et commencer à déployer des applications.

1. Sur un ordinateur équipé d’un kit de développement logiciel Service Fabric, lancez Windows PowerShell.

2. Exécutez une des commandes PS suivantes, selon que vous avez créé un cluster sécurisé ou non sécurisé.
 

- Option 1 **Connexion à un cluster non sécurisé**. 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

Exécutez les commandes suivantes pour déployer votre application, en remplaçant les chemins d’accès indiqués avec ceux qui correspondent à votre machine.

 - Option 2 **Connexion à un cluster sécurisé**

Exécutez la commande suivante pour configurer le certificat sur la machine que vous souhaitez utiliser pour exécuter la cmd PS « Connect-serviceFabricCluster »

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

Exécutez la PS suivante pour vous connecter à un cluster sécurisé. Les détails du certificat sont les mêmes que ceux que vous avez donnés sur le portail

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
Par exemple, la commande PS ci-dessus doit ressembler à ce qui suit.

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


Exécutez les commandes suivantes pour déployer votre application, en remplaçant les chemins d’accès indiqués avec ceux qui correspondent à votre machine. L’exemple qui suit consiste à déployer l’exemple d’application de l’exemple d’application.

Copiez le package vers le cluster que vous avez connecté à l’étape précédente.


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
Register your application type with service fabric.

```powershell
Enregistrez Register-ServiceFabricApplicationType - ApplicationPathInImageStore « WordCount »
````

Créez une nouvelle instance du type d’application que vous venez d’enregistrer.

```powershell
New-ServiceFabricApplication - ApplicationName fabric: / WordCount - ApplicationTypeName WordCount - ApplicationTypeVersion 1.0.0.0
````

Ouvrez maintenant le navigateur de votre choix et connectez-vous au point de terminaison que l’application écoute. Concernant l’application de comptage, l’URL ressemble à ce qui suit.

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Étapes suivantes
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
- [Présentation du modèle d’état de Service Fabric](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!----HONumber=Nov15_HO4-->