---
title: "Mettre à niveau un cluster Azure Service Fabric autonome sur Windows Server | Microsoft Docs"
description: "Mettez à niveau le code et/ou la configuration Azure Service Fabric qui exécute un cluster Service Fabric autonome, notamment en définissant le mode de mise à jour du cluster."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 6196cb7fa13cf664faa72b7f5f5e0645e4402739
ms.lasthandoff: 03/29/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Mettre à niveau un cluster Azure Service Fabric autonome sur Windows Server
> [!div class="op_single_selector"]
> * [Cluster Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)
>
>

Pour les systèmes modernes, la possibilité de mettre à niveau est essentielle à la réussite à long terme de votre produit. Un cluster Azure Service Fabric est une ressource que vous possédez. Cet article explique comment garantir que le cluster exécute toujours des versions prises en charge du code et des configurations Service Fabric.

## <a name="control-the-service-fabric-version-that-runs-on-your-cluster"></a>Contrôler la version de Service Fabric qui s’exécute sur votre cluster
Pour configurer votre cluster de sorte qu’il télécharge les mises à jour de Service Fabric lorsque Microsoft publie une nouvelle version, donnez la valeur true à la configuration de cluster **fabricClusterAutoupgradeEnabled**. Pour sélectionner une version prise en charge de Service Fabric pour votre cluster, donnez la valeur false à la configuration de cluser **fabricClusterAutoupgradeEnabled**.

> [!NOTE]
> Veillez à ce que votre cluster exécute toujours une version de Service Fabric prise en charge. Lorsque Microsoft annonce le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de l’annonce. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nouvelle version peut alors être sélectionnée.
>
>

Vous ne pouvez mettre à niveau votre cluster vers la nouvelle version que si vous utilisez une configuration de nœud de type production, où chaque nœud Service Fabric est alloué à un ordinateur physique ou virtuel distinct. Si vous disposez d’un cluster de développement dans lequel plusieurs nœuds Service Fabric se trouvent sur un seul ordinateur physique ou une seule machine virtuelle, vous devez recréer le cluster avec la nouvelle version.

Deux workflows distincts permettent de mettre à niveau votre cluster vers la dernière version ou une version prise en charge de Service Fabric. L’un concerne les clusters disposant d’une connectivité suffisante pour télécharger automatiquement la version la plus récente. L’autre workflow est destiné aux clusters ne disposant pas d’une connectivité suffisante pour télécharger la version de Service Fabric la plus récente.

### <a name="upgrade-clusters-that-have-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters disposant d’une connectivité suffisante pour télécharger le code et la configuration les plus récents
Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si vos nœuds de cluster disposent d’une connectivité Internet à [http://download.microsoft.com](http://download.microsoft.com).

Pour les clusters qui peuvent se connecter à [http://download.microsoft.com](http://download.microsoft.com), Microsoft vérifie régulièrement la disponibilité de nouvelles versions de Service Fabric.

Quand une nouvelle version de Service Fabric est disponible, le package est téléchargé localement dans le cluster et configuré pour la mise à niveau. De plus, pour informer l’utilisateur de cette nouvelle version, le système montre un avertissement explicite concernant l’intégrité du cluster, semblable à ce qui suit :

« La prise en charge de la version actuelle du cluster [numéro de version] se terminera le [date]. »

Une fois que le cluster exécute la version la plus récente, l’avertissement est retiré.

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
Quand l’avertissement concernant l’intégrité du cluster s’affiche, effectuez l’opération suivante :

1. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

    ```powershell

    ###### connect to the secure cluster using certs
    $ClusterName= "mysecurecluster.something.com:19000"
    $CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7FG2D630F8F3"
    Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
        -X509Credential `
        -ServerCertThumbprint $CertThumbprint  `
        -FindType FindByThumbprint `
        -FindValue $CertThumbprint `
        -StoreLocation CurrentUser `
        -StoreName My
    ```

2. Obtenez la liste des versions de Service Fabric vers lesquelles vous pouvez effectuer une mise à niveau.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```

    La sortie est normalement de ce type :

    ![Obtenir des versions de Service Fabric][getfabversions]
3. Démarrer une mise à niveau du cluster vers une version disponible avec la commande PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pour surveiller la progression de la mise à niveau, vous pouvez utiliser Service Fabric Explorer ou exécuter la commande Windows PowerShell suivante.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Pour spécifier des stratégies d’intégrité personnalisées pour la commande **ServiceFabricClusterUpgrade**, consultez la documentation de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes ayant entraîné la restauration, relancez la mise à niveau, en suivant les étapes décrites précédemment.

### <a name="upgrade-clusters-that-have-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters <U>ne disposant pas d’une connectivité</u> suffisante pour télécharger le code et la configuration les plus récents
Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si les nœuds de votre cluster ne disposent pas d’une connectivité Internet à [http://download.microsoft.com](http://download.microsoft.com).

> [!NOTE]
> Si vous utilisez un cluster qui n’est pas connecté à Internet, vous devrez consulter régulièrement le blog de l’équipe Service Fabric pour être averti de la disponibilité des nouvelles versions. Le système n’affiche pas d’avertissement concernant l’intégrité du cluster vous alertant en cas de disponibilité d’une nouvelle version.  
>
>

Modifiez la configuration du cluster pour définir la propriété suivante sur false avant de commencer la mise à niveau d’une configuration.

        "fabricClusterAutoupgradeEnabled": false,

Pour plus d’informations, consultez [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Veillez à mettre à jour « clusterConfigurationVersion » dans votre code JSON avant de commencer la mise à niveau de la configuration.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster

1. Exécutez Get-ServiceFabricClusterUpgrade à partir d’un des nœuds du cluster et notez la valeur TargetCodeVersion.
2. Exécutez la commande suivante à partir d’un ordinateur connecté à Internet pour répertorier toutes les versions de mise à niveau compatibles avec la version actuelle et téléchargez le package correspondant à l’aide des liens de téléchargement associés.

    ```powershell

    ###### Get list of all upgrade compatible packages  
    Get-ServiceFabricRuntimeUpgradeVersion -BaseVersion <TargetCodeVersion as noted in Step 1> 
    ```

3. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Here is a filled-out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```
4. Copiez le package téléchargé dans le magasin d’images du cluster.

5. Inscrivez le package copié.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Here is a filled-out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
6. Commencez une mise à niveau de cluster vers une version disponible.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Here is a filled-out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou exécuter la commande PowerShell suivante.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Pour spécifier des stratégies d’intégrité personnalisées pour la commande **ServiceFabricClusterUpgrade**, consultez la documentation de [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes ayant entraîné la restauration, relancez la mise à niveau, en suivant les étapes décrites précédemment.


## <a name="upgrade-the-cluster-configuration"></a>Mettre à niveau la configuration du cluster
Pour mettre à niveau la configuration du cluster, exécutez **Start-ServiceFabricClusterConfigurationUpgrade**. La mise à niveau de la configuration est traitée par domaine de mise à niveau.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

### <a name="cluster-certificate-config-upgrade"></a>Mise à niveau de la configuration du certificat de cluster  
Le certificat de cluster est utilisé pour l’authentification entre les nœuds du cluster, donc la mise à niveau du certificat doit être effectuée avec prudence, car tout échec empêchera la communication entre les nœuds du cluster.  
Techniquement, deux options sont prises en charge :  

1. Mise à niveau de certificat unique : le chemin de la mise à niveau est « Certificat A (principal) -> Certificat B (principal) -> Certificat C (principal) ->... ».   
2. Double mise à niveau de certificat : le chemin de la mise à niveau est « Certificat A -> (principal) -> Certificat A (principal) et B (secondaire) -> Certificat B (principal) -> Certificat B (principal) et C (secondaire) -> Certificat C (principal) ->... ».


## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de clusters Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
* Découvrez les [mises à niveau d’applications](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

