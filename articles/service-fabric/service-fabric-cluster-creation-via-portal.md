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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Créer un cluster Service Fabric à partir du portail Azure

Cette page vous aide à configurer un cluster Azure Service Fabric. Votre abonnement doit avoir suffisamment de cœurs pour déployer les machines virtuelles IaaS qui composeront ce cluster.


## Création du cluster

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Cliquez sur **+ Nouveau** pour ajouter un nouveau modèle de ressources. Recherchez votre modèle dans le **Marketplace** sous **Tout** ; il porte le nom **Cluster Service Fabric**.

    a. Au niveau supérieur, cliquez sur **Marketplace**.

    b. Sous **Tout**, cliquez sur **Fabric** et appuyez sur Entrée. Parfois, le filtre automatique ne fonctionne pas ; veillez donc à appuyer sur Entrée. ![Capture d’écran de la recherche du modèle de cluster Service Fabric sur le portail Azure.][SearchforServiceFabricClusterTemplate]

3. Sélectionnez **Cluster Service Fabric** dans la liste.

4. Accédez au panneau **Cluster Service Fabric**, cliquez sur **Créer** et fournissez des détails sur votre cluster.

5. Sous **Créer un groupe de ressources**, attribuez au groupe de ressources le même nom que celui du cluster. Leur recherche ultérieure s’en trouve facilitée, en particulier quand vous tentez d’apporter des modifications à votre déploiement ou que vous supprimez votre cluster.

    >[AZURE.NOTE] Bien que vous puissiez décider d’utiliser un groupe de ressources existant, nous vous conseillons de créer un groupe de ressources. Cela facilite la suppression des clusters dont vous n’avez pas besoin.

 	![Capture d’écran de la création d’un groupe de ressources.][CreateRG]

6. Veillez à sélectionner l’**Abonnement** sur lequel vous souhaitez que votre cluster soit déployé, surtout si vous avez des abonnements multiples.

7. Dans la liste déroulante, sélectionnez un **Emplacement**. La valeur par défaut est **États-Unis de l’Ouest**.

8. Configurer votre **Type de nœud**. Le type de nœud peut être considéré comme équivalent aux rôles dans les services cloud. Les types de nœuds définissent les tailles de machine virtuelle, le nombre de machines virtuelles et leurs propriétés. Votre cluster peut avoir plusieurs types de nœuds, mais le type de nœud principal (le premier que vous définissez sur le portail) doit avoir au moins cinq machines virtuelles. Pour configurer votre type de nœud :

	a. Sélectionnez la taille de machine virtuelle/le niveau tarifaire dont vous avez besoin. La valeur par défaut est D4 Standard, mais si vous souhaitez simplement utiliser ce cluster pour tester votre application, vous pouvez sélectionner D2 ou une machine virtuelle de taille inférieure.

	b. Choisissez le nombre de machines virtuelles. Vous pouvez augmenter ou réduire le nombre de machines virtuelles dans un type de nœud par la suite, mais le premier type de nœud doit avoir au moins cinq machines virtuelles.

	c. Choisissez un nom pour votre type de nœud (1 à 12 caractères contenant uniquement des lettres et des chiffres).

	d. Choisissez les **Nom d’utilisateur** et **Mot de passe** pour le bureau à distance de machine virtuelle.

	e. Si vous avez besoin de plusieurs types de nœuds dans votre cluster, considérez les points suivants. (Si vous envisagez de déployer un cluster avec un seul type de nœud, passez à l’étape 9.)

	* Supposons que vous souhaitez déployer une application qui contient un service frontal et un service principal. Vous souhaitez placer le service frontal sur des machines virtuelles plus petites (par exemple, de taille A2, D2 et ainsi de suite) ayant des ports ouverts sur Internet. Toutefois, vous souhaitez placer le service principal, gourmand en calculs, sur des machines virtuelles plus grandes (par exemple, de taille D4, D6, D12 et ainsi de suite) qui ne sont pas connectées à Internet.

	* Bien que vous puissiez placer les deux services sur un type de nœud, nous vous recommandons de les placer dans un cluster comportant deux types de nœuds. Chaque type de nœud peut avoir des propriétés distinctes, telles que la connectivité Internet, la taille de machine virtuelle et le nombre de machines virtuelles, qui peuvent être mises à l’échelle indépendamment.

	* Commencez par définir un type de nœud destiné à accueillir cinq machines virtuelles. Les autres types de nœud peuvent avoir au moins une machine virtuelle.

  	![Capture d’écran de la création d’un type de nœud.][CreateNodeType]

9. Si vous envisagez de déployer vos applications sur le cluster immédiatement, ajoutez les ports à ouvrir pour vos applications sur un type de nœud **Ports d’application** (ou sur les types de nœuds que vous avez créés). Vous pouvez ajouter des ports au type de nœud ultérieurement en modifiant l’équilibreur de charge associé à ce type de nœud. (Ajoutez une sonde, puis ajoutez celle-ci aux règles d’équilibreur de charge.) Le fait de le faire tout de suite peut être un peu plus simple, car l’automatisation du portail ajoutera les sondes et les règles nécessaires à l’équilibreur de charge.

	a. Vous pouvez trouver les ports de l’application dans vos manifestes de service, qui font partie du package d’application. Accédez à chacune de vos applications, ouvrez les manifestes de service et prenez note de tous les points de terminaison d’entrée dont vos applications ont besoin pour communiquer avec le monde extérieur.

	b. Ajoutez tous les ports dans le champ séparé par des virgules **Points de terminaison d’entrée d’application**. Le point de terminaison de connexion TCP client étant 19000 par défaut, vous n’avez pas besoin de les spécifier. Ainsi, l’exemple d’application de comptage de mots a besoin du port 83 pour être ouvert. Cette information se trouve dans le fichier servicemanifest.xml, dans votre package d’application. (Plusieurs fichiers servicemanifest.xml peuvent coexister.)

    c. La plupart des applications d’exemple utilisent les ports 80 et 8081. Ainsi, ajoutez-les si vous envisagez de déployer les exemples sur ce cluster. ![Ports][Ports]

10. Vous n’avez pas besoin de configurer **Propriétés de sélection élective**, car une propriété de sélection élective par défaut de « NodeTypeName » est ajoutée par le système. Vous pouvez en ajouter si votre application le nécessite.

## Configurer la sécurité

À ce stade, Service Fabric ne prend en charge les clusters de sécurisation que par le biais d’un certificat X509. Avant de commencer ce processus, vous devez charger votre certificat sur le coffre de clés. Pour savoir comment effectuer cette opération, consultez [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md).

La sécurisation de votre cluster est facultative, mais elle est fortement recommandée. Si vous choisissez de ne pas sécuriser votre cluster, placez le commutateur **Mode de sécurité** sur **Aucun**.

Les considérations de sécurité et les instructions sont documentées dans [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md).

![Capture d’écran des configurations de sécurité sur le portail Azure.][SecurityConfigs]

## Facultatif : configurer les diagnostics

Par défaut, les diagnostics sont activés sur votre cluster afin de faciliter la résolution des problèmes. Si vous souhaitez désactiver les diagnostics :

1. Accédez au panneau **Configurations de diagnostic**.

2. Placez la bascule à l’**état** inactif (**Off**).

## Facultatif : configurer les paramètres du cluster Service Fabric

Grâce à cette option avancée, vous pouvez modifier les paramètres par défaut du cluster Service Fabric. Nous vous recommandons de ne pas modifier les valeurs par défaut, sauf si vous êtes certain que votre application ou cluster le nécessite.

## Achever la création du cluster

Pour achever la création du cluster, cliquez sur **Résumé** pour voir les configurations que vous avez fournies, ou téléchargez le modèle Azure Resource Manager qui permet de déployer votre cluster. Après avoir fourni les paramètres obligatoires, le bouton **Créer** est activé ; vous pouvez cliquer dessus pour démarrer le processus de création de cluster.

Vous pouvez voir la progression de la création dans les notifications. (Cliquez sur l’icône représentant une cloche près de la barre d’état dans l’angle supérieur droit de votre écran.) Si vous avez cliqué sur **Épingler au Tableau d’accueil** pendant la création du cluster, **Déploiement du cluster Service Fabric** apparaît épinglé au **tableau d’accueil**.

![Capture d’écran du Tableau d’accueil affichant « Déploiement du cluster Service Fabric ».][Notifications]

## Afficher l’état de votre cluster

Une fois votre déploiement terminé, vous pouvez inspecter votre cluster dans le portail :

1. Accédez à **Parcourir**, puis cliquez sur **Clusters Service Fabric**.

2. Recherchez votre cluster et cliquez dessus. ![Capture d’écran de la recherche de votre cluster dans le portail.][BrowseCluster]

3. Vous pouvez maintenant voir les détails de votre cluster dans le tableau de bord, notamment l’adresse IP publique du cluster. Le fait de survoler l’**Adresse IP publique du cluster** affiche un presse-papiers sur lequel vous pouvez cliquer pour copier l’adresse. ![Capture d’écran des détails du cluster dans le tableau de bord.][ClusterDashboard]

  La section **Surveillance des nœuds** du panneau du tableau de bord du cluster indique le nombre de machines virtuelles intègres et de machines virtuelles non intègres. Pour plus d’informations sur l’intégrité du cluster, consultez [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md).

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l'état, situation appelée "conservation du quorum". Par conséquent, il est généralement déconseillé d'arrêter tous les ordinateurs du cluster, sauf si vous avez d'abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## Se connecter au cluster et déployer une application

Une fois la configuration du cluster terminée, vous pouvez vous connecter et commencer à déployer des applications. Commencez par lancer Windows PowerShell sur un ordinateur équipé du Kit de développement logiciel (SDK) Service Fabric. Ensuite, pour vous connecter au cluster, exécutez une des commandes PowerShell suivantes, selon que vous avez créé un cluster sécurisé ou non sécurisé :

- Option 1 : se connecter à un cluster non sécurisé

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- Option 2 : se connecter à un cluster sécurisé

    1. Exécutez la commande suivante pour configurer le certificat sur la machine que vous souhaitez utiliser pour exécuter la commande PowerShell « Connect-serviceFabricCluster »

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

        Par exemple, la commande PowerShell ci-dessus doit ressembler à ce qui suit :

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

Étant maintenant connecté, exécutez les commandes suivantes pour déployer votre application, en remplaçant les chemins d’accès indiqués par ceux qui correspondent à votre machine. L’exemple ci-après déploie l’exemple d’application de comptage de mots :

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

4. Ouvrez maintenant le navigateur de votre choix et connectez-vous au point de terminaison que l’application écoute. Concernant l’application de comptage de mots, l’URL ressemble à ce qui suit :

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## RDP dans une instance de jeu VMSS (Virtual Machine Scale Set, jeu de mise à l’échelle de machine virtuelle) ou un nœud de cluster 

Chacun des types de nœud que vous spécifiez dans votre cluster entraîne la configuration d’un jeu VMSS. Pour plus d’informations, consultez [RDP dans votre instance VMSS](service-fabric-cluster-nodetypes.md).

## Étapes suivantes

- [Gestion de vos applications Service Fabric dans Visual Studio](service-fabric-manage-application-in-visual-studio.md)
- [Sécurité d’un cluster Service Fabric](service-fabric-cluster-security.md)
- [Présentation du modèle d’intégrité de Service Fabric](service-fabric-health-introduction.md)
- [RDP dans votre instance VMSS](service-fabric-cluster-nodetypes.md)

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

<!---HONumber=AcomDC_0224_2016-->