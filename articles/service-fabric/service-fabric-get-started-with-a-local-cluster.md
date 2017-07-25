---
title: "Déployer et mettre à niveau les microservices Azure localement | Microsoft Docs"
description: "Apprenez à configurer un cluster de Service Fabric local et déployer une application existante vers ce dernier, puis mettre à niveau cette application."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 60a1f6a5-5478-46c0-80a8-18fe62da17a8
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/13/2017
ms.author: ryanwi;mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ad284e152dcf44f27fb53340dd641d95b103db78
ms.contentlocale: fr-fr
ms.lasthandoff: 07/08/2017

---
# <a name="get-started-with-deploying-and-upgrading-applications-on-your-local-cluster"></a>Prise en main avec le déploiement et la mise à niveau d’applications sur votre cluster local
Le kit de développement logiciel Fabric Service comprend un environnement de développement local complet que vous pouvez utiliser pour rapidement maîtriser le déploiement et la gestion des applications sur un cluster local. Dans cet article, vous créez un cluster local, déployez une application existante, puis mettez à niveau cette application vers une nouvelle version, le tout à partir de Windows PowerShell.

> [!NOTE]
> Cet article suppose que vous avez déjà [configuré votre environnement de développement](service-fabric-get-started.md).
> 
> 

## <a name="create-a-local-cluster"></a>Créer un cluster local
Un cluster Service Fabric représente un ensemble de ressources matérielles sur lequel vous pouvez déployer des applications. En règle générale, un cluster est constitué d’un nombre de machines compris entre cinq et plusieurs milliers. Toutefois, le kit de développement logiciel Service Fabric propose une configuration de cluster qui peut s’exécuter sur un seul ordinateur.

Il est important de savoir que le cluster local Service Fabric n’est ni un émulateur, ni un simulateur. Il exécute le même code de plateforme que celui qu’on trouve sur les clusters comportant plusieurs ordinateurs. La seule différence est qu’elle exécute les processus de plateforme normalement répartis entre cinq ordinateurs virtuels sur une seule machine.

Le kit de développement logiciel offre deux façons de configurer un cluster local : un script Windows PowerShell et l’application de barre d’état système Gestionnaire du cluster local. Pour ce didacticiel, nous utilisons le script Powershell.

> [!NOTE]
> Si vous avez déjà créé un cluster local en déployant une application depuis Visual Studio, vous pouvez ignorer cette section.
> 
> 

1. Lancez une nouvelle fenêtre PowerShell en tant qu’administrateur.
2. Exécutez le script de configuration de cluster à partir du dossier du kit de développement logiciel :
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
    ```
   
    L’installation du cluster prend quelques instants. Une fois l’installation terminée, vous devriez obtenir un résultat similaire à ceci :
   
    ![Résultat de configuration du cluster][cluster-setup-success]
   
    Vous êtes maintenant prêt à tester le déploiement d’une application sur votre cluster.

## <a name="deploy-an-application"></a>Déployer une application
Le kit de développement de Fabric Service inclut un ensemble complet d’infrastructures et outils pour créer des applications. Si vous souhaitez apprendre à créer des applications dans Visual Studio, consultez [Créer votre première application Service Fabric dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).

Dans ce didacticiel, vous utilisez un exemple d’application existant (appelé WordCount) afin de pouvoir vous concentrer sur les aspects de la gestion de la plateforme, autrement dit le déploiement, la surveillance et la mise à niveau.

1. Lancez une nouvelle fenêtre PowerShell en tant qu’administrateur.
2. Importez le module de kit de développement logiciel Service Fabric PowerShell.
   
    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```
3. Créez un répertoire pour stocker l’application que vous téléchargez et déployez, par exemple C:\ServiceFabric.
   
    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```
4. [Téléchargez l’application WordCount](http://aka.ms/servicefabric-wordcountapp) à l’emplacement que vous avez créé.  Remarque : le navigateur Microsoft Edge enregistre le fichier avec une extension *.zip* .  Remplacez l’extension du fichier par *.sfpkg*.
5. Pour se connecter au cluster :
   
    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```
6. Créez une nouvelle application en utilisant la commande de déploiement du SDK, en fournissant un nom et un chemin d’accès au package d’application.
   
    ```powershell  
   Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```
   
    Si tout se passe correctement, vous devez voir apparaître la sortie suivante :
   
    ![Déploiement d’une application sur le cluster local][deploy-app-to-local-cluster]
7. Pour voir l’application en action, lancez le navigateur et accédez à [http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html). Ce qui suit doit s’afficher :
   
    ![Interface utilisateur des applications déployées.][deployed-app-ui]
   
    L’application WordCount est d’une grande simplicité. Il inclut le code JavaScript côté client pour générer des « mots » à cinq caractères aléatoires, qui sont ensuite relayés vers l’application via une API web ASP.NET. Un service avec état conserve un suivi du nombre de mots comptés. Ils sont partitionnés en fonction du premier caractère du mot. Vous trouverez le code source de l’application WordCount dans les [exemples de prise en main classiques](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/WordCount).
   
    L’application que nous avons déployée contient quatre partitions. Les mots commençant par les lettres A à G sont stockés dans la première partition, ceux qui commencent par les lettres H à N sont stockés dans la deuxième et ainsi de suite.

## <a name="view-application-details-and-status"></a>Afficher les détails et l’état de l’application
Maintenant que l’application est déployée, examinons certains des détails de l’application dans PowerShell.

1. Interroger toutes les applications déployées sur le cluster :
   
    ```powershell
    Get-ServiceFabricApplication
    ```
   
    Si vous avez déployé uniquement l’application de comptage de mots, vous voyez quelque chose de similaire à ceci :
   
    ![Interroger toutes les applications déployées sur PowerShell :][ps-getsfapp]
2. Accédez au niveau suivant en interrogeant l’ensemble de services inclus dans l’application WordCount.
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Répertorier les services de l’application dans PowerShell][ps-getsfsvc]
   
    L’application se compose de deux services : le serveur web frontal et le service avec état qui gère les mots.
3. Enfin, examinez la liste des partitions de WordCountService :
   
    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```
   
    ![Afficher les partitions de service dans PowerShell][ps-getsfpartitions]
   
    Le jeu de commandes que vous avez utilisé, ainsi que toutes les commandes PowerShell de Service Fabric, sont disponibles pour n’importe quel cluster auquel vous pouvez connecter, qu’il soit local ou distant.
   
    Pour un moyen plus visuel d’interagir avec le cluster, vous pouvez utiliser l’outil explorateur de Fabric Service web en accédant à [http://localhost:19080/Explorer](http://localhost:19080/Explorer) dans le navigateur.
   
    ![Afficher les détails de l’application dans Fabric Service Explorer][sfx-service-overview]
   
   > [!NOTE]
   > Pour plus d’informations sur Service Fabric Explorer, voir [Visualiser votre cluster à l’aide de l’outil Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
   > 
   > 

## <a name="upgrade-an-application"></a>Mettre à niveau une application
Service Fabric fournit des mises à niveau sans temps mort en analysant l’état de l’application au fur et à mesure qu’il se déploie sur le cluster. Effectuez une mise à niveau de l’application WordCount.

La nouvelle version de l’application compte désormais uniquement les mots commençant par une voyelle. Au fur et à mesure que la mise à niveau se déploie, nous voyons deux changements de comportement dans l’application. Tout d’abord, la vitesse de développement du nombre baisse, car un nombre de mots inférieur est compté. Ensuite, comme la première partition comporte deux voyelles (A et E) et tous les autres une seule, le décompte doit commencer pour devancer les autres.

1. [Téléchargez le package WordCount version 2](http://aka.ms/servicefabric-wordcountappv2) au même emplacement que celui où vous avez téléchargé le package version 1.
2. Revenez à votre fenêtre PowerShell et utilisez les commandes de mise à niveau du kit de développement logiciel pour enregistrer la nouvelle version dans le cluster. Commencez ensuite la mise à niveau de l’application : /WordCount application.
   
    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```
   
    Vous devriez voir apparaître la sortie ci-après dans PowerShell au début de la mise à niveau.
   
    ![Progression de la mise à niveau dans PowerShell][ps-appupgradeprogress]
3. Bien que la mise à niveau s’exécute, il peut s’avérer plus facile de surveiller son état à partir de Fabric Service Explorer. Lancez une fenêtre de navigateur et accédez à [http://localhost:19080/Explorer](http://localhost:19080/Explorer). Développez **Applications** dans l’arborescence figurant sur la gauche, puis choisissez **WordCount** et enfin **fabric:/WordCount**. Dans l’onglet Bases, vous voyez l’état de la mise à niveau à mesure qu’elle se poursuit dans les domaines de mise à niveau du cluster.
   
    ![Progression de la mise à niveau dans Service Fabric Explorer][sfx-upgradeprogress]
   
    Comme la mise à niveau se poursuit dans chaque domaine, les vérifications d’état sont effectuées pour vous assurer que l’application se comporte correctement.
4. Si vous réexécutez la requête qui précède pour l’ensemble des services inclus dans l’application fabric:/Wordcount, notez que la version de WordCountService a changé, mais non la version de WordCountWebService :
   
    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```
   
    ![Services d’application de requête après mise à niveau][ps-getsfsvc-postupgrade]
   
    Cet exemple met en évidence la façon dont Service Fabric gère les mises à niveau d’application. Cela ne touche que l’ensemble des services (ou des packages de configuration/code au sein de ces services) qui ont été modifiés, rendant le processus de mise à niveau plus rapide et plus fiable.
5. Enfin, retournez au navigateur pour observer le comportement de la nouvelle version de l’application. Comme prévu, le nombre progresse plus lentement et la première partition se termine avec légèrement plus du volume.
   
    ![Afficher la nouvelle version de l’application dans le navigateur][deployed-app-ui-v2]

## <a name="cleaning-up"></a>Nettoyage
Avant de conclure, il est important de se rappeler que le cluster local est réel. Les applications continuent de s’exécuter en arrière-plan jusqu’à ce que vous les supprimiez.  Selon la nature de vos applications, une application en cours d’exécution peut accaparer une quantité significative de ressources de votre ordinateur. Vous disposez de plusieurs options pour gérer les applications et le cluster :

1. Pour supprimer une application spécifique et toutes ses données, exécutez la commande suivante :
   
    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```
   
    Une autre possibilité consiste à supprimer l’application à partir du menu **ACTIONS** de Service Fabric Explorer ou du menu contextuel dans la vue Liste des applications sur la gauche.
   
    ![Supprimer une application dans Service Fabric Explorer][sfe-delete-application]
2. Après avoir supprimé l’application du cluster, annulez l’inscription des versions 1.0.0 et 2.0.0 du type d’application WordCount. Cette opération supprime les packages d’application, y compris le code et la configuration, à partir du magasin d’images du cluster.
   
    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```
   
    Une autre possibilité consiste à accéder à Service Fabric Explorer et à choisir **Type de mise hors service** pour l’application.
3. Pour arrêter le cluster tout en conservant les données et les traces de l’application, cliquez sur **Arrêter le cluster local** dans l’application de zone de notification.
4. Pour supprimer complètement le cluster, cliquez sur **Supprimer le cluster local** dans l’application de zone de notification. Cette option se traduira par un autre déploiement lent la prochaine fois que vous appuierez sur F5 dans Visual Studio. Supprimez le cluster local uniquement si vous n’envisagez pas de l’utiliser pendant un certain temps, ou si vous avez besoin de libérer des ressources.

## <a name="one-node-and-five-node-cluster-mode"></a>Modes de cluster un nœud et cinq nœuds
Lorsque vous développez des applications, vous êtes souvent amené à effectuer des itérations rapides d’écriture de code, de débogage, de modification de code et de débogage. Afin d’optimiser ce processus, le cluster local peut s’exécuter dans deux modes : un nœud ou cinq nœuds. Ces deux modes de cluster présentent chacun des avantages. Le mode de cluster cinq nœuds vous permet d’utiliser un cluster réel. Vous pouvez tester des scénarios de basculement, et travailler avec un plus grand nombre d’instances et de réplicas de vos services. Le mode de cluster un nœud est optimisé pour accélérer le déploiement et l’inscription des services, afin de vous aider à valider rapidement le code à l’aide du runtime Service Fabric.

Ni le mode de cluster un nœud, ni le mode de cluster cinq nœuds ne constituent un émulateur ou un simulateur. Le cluster de développement local exécute le même code de plateforme que celui qu’on trouve sur les clusters comportant plusieurs machines.

> [!WARNING]
> Lorsque vous changez le mode de cluster, le cluster actuel est supprimé de votre système, et un autre cluster est créé. La modification du mode de cluster entraîne la suppression des données stockées dans le cluster.
> 
> 

Pour sélectionner le mode de cluster un nœud, sélectionnez **Switch Cluster Mode (Changer de mode de cluster)** dans le gestionnaire de cluster local Service Fabric.

![Changer de mode de cluster][switch-cluster-mode]

Une autre possibilité consiste à modifier le mode de cluster en utilisant PowerShell :

1. Lancez une nouvelle fenêtre PowerShell en tant qu’administrateur.
2. Exécutez le script de configuration de cluster à partir du dossier du kit de développement logiciel :
   
    ```powershell
    & "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
    ```
   
    L’installation du cluster prend quelques instants. Une fois l’installation terminée, vous devriez obtenir un résultat similaire à ceci :
   
    ![Résultat de configuration du cluster][cluster-setup-success-1-node]

## <a name="next-steps"></a>Étapes suivantes
* Maintenant que vous avez déployé et mis à niveau certaines des applications pré intégrées, vous pouvez [Réessayer de générer les vôtres dans Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md).
* Dans cet article, toutes les opérations effectuées sur le cluster local sont également applicables à un [cluster Azure](service-fabric-cluster-creation-via-portal.md) .
* La mise à niveau exécutée dans cet article a été simple. Consultez la [documentation relative à la mise à niveau](service-fabric-application-upgrade.md) pour en savoir plus sur la puissance et la flexibilité des mises à niveau de Service Fabric.

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
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png

