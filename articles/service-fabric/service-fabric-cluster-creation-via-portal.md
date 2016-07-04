<properties
   pageTitle="Créer un cluster Service Fabric à partir du portail Azure | Microsoft Azure"
   description="Créez un cluster Service Fabric à partir du portail Azure."
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>


# Créer un cluster Service Fabric à partir du portail Azure

Cette page vous aide à configurer un cluster Azure Service Fabric. Votre abonnement doit avoir suffisamment de cœurs pour déployer les machines virtuelles IaaS qui composeront ce cluster.


## Rechercher la ressource de cluster Service Fabric

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **+ Nouveau** pour ajouter un nouveau modèle de ressources. Recherchez votre modèle dans le **Marketplace** sous **Tout** ; il porte le nom **Cluster Service Fabric**.

    a. Au niveau supérieur, cliquez sur **Marketplace**.

    b. Sous **Tout**, entrez « Fabric » et appuyez sur Entrée. Parfois, le filtre automatique ne fonctionne pas ; veillez donc à appuyer sur Entrée. ![Capture d’écran de la recherche du modèle de cluster Service Fabric sur le portail Azure.][SearchforServiceFabricClusterTemplate]

3. Sélectionnez **Cluster Service Fabric** dans la liste.

4. Accédez au volet **Cluster Service Fabric**, puis cliquez sur **Créer**.

5. Le volet **Créer un cluster Service Fabric**, qui répertorie 4 étapes.

## Étape 1 : De base

Dans le volet De base, vous devez fournir les informations de base de votre cluster.

1. Entrez le nom de votre cluster.

2. Choisissez les **Nom d’utilisateur** et **Mot de passe** pour le bureau à distance de machine virtuelle.

3. Veillez à sélectionner l’**Abonnement** sur lequel vous souhaitez que votre cluster soit déployé, surtout si vous avez des abonnements multiples.

4. Créer un **nouveau groupe de ressources**, il est préférable de lui attribuer le même nom que le cluster, car cela vous permet de le retrouver plus facilement, surtout lorsque vous essayez d’apporter des modifications à votre déploiement ou de supprimer votre cluster.

    >[AZURE.NOTE] Bien que vous puissiez décider d’utiliser un groupe de ressources existant, nous vous conseillons de créer un groupe de ressources. Cela facilite la suppression des clusters dont vous n’avez pas besoin.

 	![Capture d’écran de la création d’un groupe de ressources.][CreateRG]


5. Dans la liste déroulante, sélectionnez un **Emplacement**. La valeur par défaut est **États-Unis de l’Ouest**. Appuyez sur OK.

## Étape 2 : configurer le cluster

10. Nous allons d’abord voir ce qu’est un **Type de nœud**. Le type de nœud peut être considéré comme équivalent aux rôles dans les services cloud. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Votre cluster peut avoir plusieurs types de nœud, mais le type de nœud principal (le premier que vous définissez sur le portail) doit avoir au moins cinq machines virtuelles. il s’agit du type de nœud où les services du système Service Fabric sont placés. Prenez en considération les points suivants pour déterminer vos besoins lorsque vous avez plusieurs types de nœuds.

	* L’application que vous souhaitez déployer contient un service frontal et un service principal. Vous souhaitez placer le service frontal sur des machines virtuelles plus petits (tailles de machine virtuelle de type D2) et leurs ports sont ouverts à Internet, mais vous souhaitez placer le service principal, qui est gourmand en calculs, sur des machines virtuelles plus importantes (dont les tailles sont de type D4, D6, D15, etc.) qui ne sont pas accessibles sur Internet.

	* Bien que vous puissiez placer les deux services sur un type de nœud, nous vous recommandons de les placer dans un cluster comportant deux types de nœuds. Chaque type de nœud peut avoir des propriétés distinctes, telles que la connectivité Internet, la taille de machine virtuelle et le nombre de machines virtuelles, qui peuvent être mises à l’échelle indépendamment.

	* Commencez par définir un type de nœud destiné à accueillir cinq machines virtuelles. Les autres types de nœud peuvent avoir au moins une machine virtuelle.

13.  Pour configurer votre type de nœud :

	a. Choisissez un nom pour votre type de nœud (1 à 12 caractères contenant uniquement des lettres et des chiffres).

	b. La taille minimale des machines virtuelles pour le type de nœud principal dépend de la couche de durabilité que vous choisissez pour le cluster. La valeur par défaut du niveau de durabilité est Bronze. Pour en savoir plus, consultez le document expliquant comment [choisir la fiabilité et la durabilité du cluster Service Fabric](service-fabric-cluster-capacity.md).

	b. Sélectionnez le niveau de tarification/la taille de la machine virtuelle. La valeur par défaut est D4 Standard, mais si vous souhaitez simplement utiliser ce cluster pour tester votre application, vous pouvez sélectionner D2 ou une machine virtuelle de taille inférieure.

	c. Le nombre minimum de machines virtuelles du type de nœud principal dépend du niveau de fiabilité que vous choisissez. La valeur par défaut du niveau de fiabilité est Silver. Pour en savoir plus, consultez le document expliquant comment [choisir la fiabilité et la durabilité du cluster Service Fabric](service-fabric-cluster-capacity.md).

	c. Choisissez le nombre de machines virtuelles du type de nœud. Vous pouvez augmenter ou réduire ultérieurement le nombre de machines virtuelles d’un type de nœud. Cependant, pour le type de nœud principal, le nombre de machines virtuelles minimum dépend du niveau de fiabilité que vous avez choisi. Les autres types de nœud peuvent avoir au moins 1 machine virtuelle.


  	![Capture d’écran de la création d’un type de nœud.][CreateNodeType]

9. Si vous envisagez de déployer vos applications sur le cluster immédiatement, ajoutez les ports à ouvrir pour vos applications sur un type de nœud **Ports d’application** (ou sur les types de nœuds que vous avez créés). Vous pouvez ajouter des ports au type de nœud ultérieurement en modifiant l’équilibreur de charge associé à ce type de nœud. (Ajoutez une sonde, puis ajoutez celle-ci aux règles d’équilibreur de charge.) Le fait de le faire tout de suite peut être un peu plus simple, car l’automatisation du portail ajoutera les sondes et les règles nécessaires à l’équilibreur de charge.

	a. Vous pouvez trouver les ports de l’application dans vos manifestes de service, qui font partie du package d’application. Accédez à chacune de vos applications, ouvrez les manifestes de service et prenez note de tous les points de terminaison d’entrée dont vos applications ont besoin pour communiquer avec le monde extérieur.

	b. Ajoutez tous les ports dans le champ séparé par des virgules **Points de terminaison d’entrée d’application**. Le point de terminaison de connexion TCP client étant 19000 par défaut, vous n’avez pas besoin de les spécifier. Ainsi, l’exemple d’application de comptage de mots a besoin du port 83 pour être ouvert. Cette information se trouve dans le fichier servicemanifest.xml, dans votre package d’application. (Plusieurs fichiers servicemanifest.xml peuvent coexister.)

    c. La plupart des applications d’exemple utilisent les ports 80 et 8081. Ainsi, ajoutez-les si vous envisagez de déployer les exemples sur ce cluster. ![Ports][Ports]

10. Vous n’avez pas besoin de configurer **Propriétés de sélection élective**, car une propriété de sélection élective par défaut de « NodeTypeName » est ajoutée par le système. Vous pouvez en ajouter si votre application le nécessite.

11. Vous n’avez pas besoin de configurer **Propriétés de capacité**, mais ceci est recommandé, car vous pouvez l’utiliser dans vos applications pour indiquer la charge du système, ce qui influence les décisions de placement et d’équilibrage des ressources du système dans le cluster Service Fabric. Consultez [ce document](service-fabric-cluster-resource-manager-architecture.md) pour en savoir plus sur le démarrage de l’équilibrage des ressources de Service Fabric.

12. Suivez les étapes ci-dessus pour tous les types de nœud.

14. Configurez les **diagnostics** du cluster. Par défaut, les diagnostics sont activés sur votre cluster afin de faciliter la résolution des problèmes. Si vous souhaitez désactiver les diagnostics, définissez l’**État** sur **Désactivé**. Nous vous recommandons de **ne pas** désactiver les diagnostics.

15. Facultatif : définissez les **paramètres du cluster Service Fabric**. Cette option avancée vous permet de modifier les paramètres par défaut du cluster Service Fabric. Nous vous recommandons de ne pas modifier les valeurs par défaut, sauf si vous êtes certain que votre application ou cluster le nécessite.

## Étape 3 : configurer la sécurité

Les scénarios et concepts de sécurité sont documentés dans [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md). À ce stade, Service Fabric ne prend en charge la sécurisation des clusters qu’avec un certificat X.509. Reportez-vous à [Sécuriser un cluster Service Fabric sur Azure à l’aide de certificats](service-fabric-secure-azure-cluster-with-certs.md) pour savoir comment procéder.

La sécurisation de votre cluster est facultative, mais elle est fortement recommandée. Si vous choisissez de ne pas sécuriser votre cluster, placez le commutateur **Mode de sécurité** sur **Non sécurisé**. Remarque : vous **ne pourrez pas** mettre à jour ultérieurement un cluster non sécurisé vers un cluster sécurisé.

![Capture d’écran des configurations de sécurité sur le portail Azure.][SecurityConfigs]


## Étape 4 : achever la création du cluster

Pour achever la création du cluster, cliquez sur **Résumé** pour voir les configurations que vous avez fournies, ou téléchargez le modèle Azure Resource Manager qui permet de déployer votre cluster. Après avoir fourni les paramètres obligatoires, le bouton **OK** est activé ; vous pouvez cliquer dessus pour démarrer le processus de création de cluster.

Vous pouvez voir la progression de la création dans les notifications. (Cliquez sur l’icône représentant une cloche près de la barre d’état dans l’angle supérieur droit de votre écran.) Si vous avez cliqué sur **Épingler au Tableau d’accueil** pendant la création du cluster, **Déploiement du cluster Service Fabric** apparaît épinglé au **tableau d’accueil**.

![Capture d’écran du Tableau d’accueil affichant « Déploiement du cluster Service Fabric ».][Notifications]

## Afficher l’état de votre cluster

Une fois votre cluster créé, vous pouvez l’inspecter dans le portail :

1. Accédez à **Parcourir**, puis cliquez sur **Clusters Service Fabric**.

2. Recherchez votre cluster et cliquez dessus. ![Capture d’écran de la recherche de votre cluster dans le portail.][BrowseCluster]

3. Vous pouvez maintenant voir les détails de votre cluster dans le tableau de bord, notamment l’adresse IP publique du cluster. Le fait de survoler **l’adresse IP publique du cluster** affiche un presse-papiers sur lequel vous pouvez cliquer pour copier l’adresse. ![Capture d’écran des détails du cluster dans le tableau de bord.][ClusterDashboard]

  La section **Surveillance des nœuds** du panneau du tableau de bord du cluster indique le nombre de machines virtuelles intègres et de machines virtuelles non intègres. Pour plus d’informations sur l’intégrité du cluster, consultez [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l'état, situation appelée "conservation du quorum". Par conséquent, il est généralement déconseillé d’arrêter tous les ordinateurs du cluster, sauf si vous avez d’abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## Se connecter au cluster et déployer une application

Une fois la configuration du cluster terminée, vous pouvez vous connecter et commencer à déployer des applications. Commencez par lancer Windows PowerShell sur un ordinateur équipé du Kit de développement logiciel (SDK) Service Fabric. Ensuite, pour vous connecter au cluster, exécutez une des commandes PowerShell suivantes, selon que vous avez créé un cluster sécurisé ou non sécurisé :

### Se connecter à un cluster non sécurisé

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
```

### Se connecter à un cluster sécurisé

1. Exécutez la commande suivante pour configurer le certificat sur la machine que vous souhaitez utiliser pour exécuter la commande PowerShell « Connect-serviceFabricCluster »

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

2. Exécutez la commande PowerShell suivante pour vous connecter à un cluster sécurisé. Les détails du certificat sont les mêmes que ceux que vous avez fournis sur le portail.

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
              -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
              -StoreLocation CurrentUser -StoreName My
    ```

    Par exemple, la commande PowerShell ci-dessus doit ressembler à ce qui suit :

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
              -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
              -StoreLocation CurrentUser -StoreName My
    ```

### Déployer votre application
Étant maintenant connecté, exécutez les commandes suivantes pour déployer votre application, en remplaçant les chemins d’accès indiqués par ceux qui correspondent à votre machine. L’exemple ci-après déploie l’exemple d’application de comptage de mots :

1. Copiez le package vers le cluster auquel vous vous êtes précédemment connecté.

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. Inscrivez votre type d’application auprès de Service Fabric.

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. Créez une instance sur le type d’application que vous venez d’inscrire.

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. Ouvrez maintenant le navigateur de votre choix et connectez-vous au point de terminaison que l’application écoute. Concernant l’application de comptage de mots, l’URL ressemble à ce qui suit :

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## Connexion distante à une instance de jeu de mise à l’échelle de machine virtuelle ou à un nœud de cluster

Chacun des types de nœud que vous spécifiez dans votre cluster entraîne la configuration d’un jeu de mise à l’échelle de machine virtuelle. Pour plus d’informations, consultez [Se connecter à distance à une instance du groupe de machines virtuelles identiques ou à un nœud de cluster](service-fabric-cluster-nodetypes.md#remote-connect-to-a-vm-scale-set-instance-or-a-cluster-node).

## Étapes suivantes

Après avoir créé un cluster, découvrez comment le protéger et y déployer des applications :
- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)


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

<!---HONumber=AcomDC_0622_2016-->