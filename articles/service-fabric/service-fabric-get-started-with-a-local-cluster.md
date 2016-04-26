<properties
   pageTitle="Prise en main avec le déploiement et la mise à niveau d’applications sur votre cluster local | Microsoft Azure"
   description="Configurer un cluster de Service Fabric local et déployer une application existante vers ce dernier, puis mettre à niveau cette application."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/12/2016"
   ms.author="seanmck"/>

# Prise en main avec le déploiement et la mise à niveau d’applications sur votre cluster local
Le kit de développement logiciel Fabric Service comprend un environnement de développement local complet que vous pouvez utiliser pour rapidement maîtriser le déploiement et la gestion des applications sur un cluster local. Dans cet article, vous allez créer un cluster local, déployer une application existante, puis mettre à niveau cette application vers une nouvelle version, le tout à partir de Windows PowerShell.

> [AZURE.NOTE] Cet article suppose que vous avez déjà [configuré votre environnement de développement](service-fabric-get-started.md).

## Créer un cluster local
Un cluster Service Fabric représente un ensemble de ressources matérielles sur lequel vous pouvez déployer des applications. En règle générale, un cluster est constitué d’un nombre de machines compris entre cinq et plusieurs milliers. Toutefois, le kit de développement logiciel Service Fabric propose une configuration de cluster qui peut s’exécuter sur un seul ordinateur.

Il est important de savoir que le cluster local Service Fabric n’est ni un émulateur, ni un simulateur. Il exécute le même code de plateforme que celui qu’on trouve sur les clusters comportant plusieurs ordinateurs. La seule différence est qu’elle exécute les processus de plateforme normalement répartis entre cinq ordinateurs virtuels sur une seule machine.

Le kit de développement logiciel offre deux façons de configurer un cluster local : un script Windows PowerShell et l’application de barre d’état système Gestionnaire du cluster local. Pour ce didacticiel, nous utiliserons le script Powershell.

> [AZURE.NOTE] Si vous avez déjà créé un cluster local en déployant une application depuis Visual Studio, vous pouvez ignorer cette section.


1. Lancez une nouvelle fenêtre PowerShell en tant qu’administrateur.

2. Exécutez le script de configuration de cluster à partir du dossier du kit de développement logiciel :

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    L’installation du cluster prendra quelques instants. Une fois l’installation terminée, vous devriez obtenir un résultat qui ressemble à ceci :

    ![Résultat de configuration du cluster][cluster-setup-success]

    Vous êtes maintenant prêt à tester le déploiement d’une application sur votre cluster.

## Déployer une application
Le kit de développement de Fabric Service inclut un ensemble complet d’infrastructures et outils pour créer des applications. Si vous souhaitez apprendre à créer des applications dans Visual Studio, consultez [Créer votre première application Service Fabric dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Dans ce didacticiel, nous allons utiliser un exemple d’application existant (appelé WordCount) afin de pouvoir nous concentrer sur les aspects de la gestion de la plateforme, notamment le déploiement, la surveillance et la mise à niveau.


1. Lancez une nouvelle fenêtre PowerShell en tant qu’administrateur.

2. Importez le module de kit de développement logiciel Service Fabric PowerShell.

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. Créez un répertoire pour stocker l’application que vous allez télécharger et déployer, par exemple c:\\ServiceFabric.

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. [Téléchargez l’application WordCount](http://aka.ms/servicefabric-wordcountapp) à l’emplacement que vous avez créé.

5. Pour se connecter au cluster :

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. Appelez la commande de déploiement du kit de développement logiciel pour créer une nouvelle application, en fournissant un nom et un chemin d’accès au package d’application.

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    Si tout se passe correctement, vous devez obtenir un résultat ressemblant à ce qui suit :

    ![Déploiement d’une application sur le cluster local][deploy-app-to-local-cluster]

7. Pour voir l’application en action, lancez le navigateur et accédez à [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Le résultat suivant devrait s'afficher :

    ![Interface utilisateur des applications déployées.][deployed-app-ui]

    L’application WordCount est très simple. Il inclut le code JavaScript côté client pour générer des « mots » à cinq caractères aléatoires, qui sont ensuite relayés vers l’application via une API web ASP.NET. Un service avec état conserve un suivi du nombre de mots comptés. Ils sont partitionnés en fonction du premier caractère du mot.

    L’application que nous avons déployée contient quatre partitions. Les mots commençant par les lettres A à G sont stockés dans la première partition, ceux qui commencent par les lettres H à N sont stockés dans la deuxième et ainsi de suite.

## Afficher les détails et l’état de l’application
Maintenant que l’application est déployée, examinons certains des détails de l’application dans PowerShell.

1. Interroger toutes les applications déployées sur le cluster :

    ```powershell
    Get-ServiceFabricApplication
    ```

    Si vous avez déployé uniquement l’application de comptage de mots, vous allez voir quelque chose qui ressemble à ceci :

    ![Interroger toutes les applications déployées sur PowerShell :][ps-getsfapp]

2. Accédez au niveau suivant en interrogeant l’ensemble de services inclus dans l’application WordCount.

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Répertorier les services de l’application dans PowerShell][ps-getsfsvc]

    Notez que l’application se compose de deux services : le serveur web frontal et le service avec l’état qui gère les mots.

3. Enfin, examinez la liste des partitions de WordCountService :

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![Afficher les partitions de service dans PowerShell][ps-getsfpartitions]

    Le jeu de commandes que vous venez d’utiliser, ainsi que toutes les commandes PowerShell de Service Fabric, est disponible pour n’importe quel cluster auquel vous pouvez connecter, qu’il soit local ou distant.

    Pour un moyen plus visuel d’interagir avec le cluster, vous pouvez utiliser l’outil explorateur de Fabric Service web en accédant à [http://localhost:19080/Explorer](http://localhost:19080/Explorer) dans le navigateur.

    ![Afficher les détails de l’application dans Fabric Service Explorer][sfx-service-overview]

    > [AZURE.NOTE] Pour en savoir plus sur Service Fabric Explorer, consultez [Visualiser votre cluster avec le Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

## Mettre à niveau une application
Service Fabric fournit des mises à niveau sans temps mort en analysant l’état de l’application au fur et à mesure qu’il se déploie sur le cluster. Nous allons effectuer une simple mise à niveau de l’application WordCount.

La nouvelle version de l’application compte désormais uniquement les mots commençant par une voyelle. Au fur et à mesure que la mise à niveau se déploie, nous allons voir deux changements de comportement dans l’application. Tout d’abord, la vitesse de développement du nombre baisse, car un nombre de mots inférieur est compté. Ensuite, comme la première partition comporte deux voyelles (A et E) et tous les autres une seule, le décompte doit commencer pour devancer les autres.

1. [Téléchargez le package WordCount v2](http://aka.ms/servicefabric-wordcountappv2) vers le même emplacement que celui où vous avez téléchargé le package v1.

2. Revenez à votre fenêtre PowerShell et utilisez les commandes de mise à niveau du kit de développement logiciel pour enregistrer la nouvelle version dans le cluster. Commencez ensuite la mise à niveau de l’application : /WordCount application.

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    Vous devriez voir dans PowerShell un résultat ressemblant à ce qui suit au moment où la mise à niveau commence.

    ![Progression de la mise à niveau dans PowerShell][ps-appupgradeprogress]

3. Bien que la mise à niveau s’exécute, il peut s’avérer plus facile de surveiller son état à partir de Fabric Service Explorer. Lancez une fenêtre de navigateur et accédez à [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Développez **Applications** dans l'arborescence sur la gauche, puis choisissez **WordCount**, et enfin **fabric:/WordCount**. Dans l'onglet Bases, vous verrez l'état de la mise à niveau à mesure qu'elle se poursuit dans les domaines de mise à niveau du cluster.

    ![Progression de la mise à niveau dans Service Fabric Explorer][sfx-upgradeprogress]

    Comme la mise à niveau se poursuit dans chaque domaine, les vérifications d’état sont effectuées pour vous assurer que l’application se comporte correctement.

4. Si vous réexécutez la requête qui précède pour l’ensemble des services inclus dans l’application fabric:/Wordcount, vous remarquerez que pendant le changement de version de WordCountService, la version de WordCountWebService n’a pas :

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![Services d’application de requête après mise à niveau][ps-getsfsvc-postupgrade]

    Cela met en évidence la façon dont Service Fabric gère les mises à niveau d’application. Cela ne touche que l’ensemble des services (ou des packages de configuration/code au sein de ces services) qui ont été modifiés, rendant le processus de mise à niveau plus rapide et plus fiable.

5. Enfin, retournez au navigateur pour observer le comportement de la nouvelle version de l’application. Comme prévu, le nombre progresse plus lentement et la première partition se termine avec légèrement plus du volume.

    ![Afficher la nouvelle version de l’application dans le navigateur][deployed-app-ui-v2]

## Étapes suivantes
- Maintenant que vous avez déployé et mis à niveau certaines des applications pré intégrées, vous pouvez [Réessayer de générer les vôtres dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
- Toutes les opérations effectuées sur le cluster local dans cet article peuvent également être effectuées sur un [cluster Azure](service-fabric-cluster-creation-via-portal.md).
- La mise à niveau exécutée dans cet article a été très simple. Consultez la [documentation relative à la mise à niveau](service-fabric-application-upgrade.md) pour en savoir plus sur la puissance et la flexibilité des mises à niveau de Service Fabric.

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png

<!---HONumber=AcomDC_0420_2016-->