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
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: df15775f8e93c1dfad82c69ee4caa7838a39a545
ms.lasthandoff: 03/07/2017


---
# <a name="upgrade-your-standalone-azure-service-fabric-cluster-on-windows-server"></a>Mettre à niveau un cluster Azure Service Fabric autonome sur Windows Server
> [!div class="op_single_selector"]
> * [Cluster Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)
>
>

Pour les systèmes modernes, la réussite à long terme d’un produit dépend de sa capacité à être mis à niveau. Un cluster Azure Service Fabric est une ressource que vous possédez. Cet article explique comment garantir que le cluster exécute toujours des versions prises en charge du code et des configurations Service Fabric.

## <a name="control-the-fabric-version-that-runs-on-your-cluster"></a>Contrôler la version de structure qui s’exécute sur votre cluster
Vous pouvez définir votre cluster de façon à télécharger des mises à jour Service Fabric quand Microsoft publie une nouvelle version. Vous pouvez également sélectionner une version de structure prise en charge pour votre cluster.

Pour contrôler la version de structure, définissez la configuration de cluster « fabricClusterAutoupgradeEnabled » sur true ou sur false.

> [!NOTE]
> Veillez à ce que votre cluster exécute toujours une version de Service Fabric prise en charge. Quand le lancement d’une nouvelle version de Service Fabric est annoncé, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de lancement. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/).
>
>

Vous ne pouvez mettre à niveau votre cluster vers la nouvelle version que si vous utilisez une configuration de nœud de type production, où chaque nœud Service Fabric est alloué sur un ordinateur physique ou une machine virtuelle distincts. Si vous disposez d’un cluster de développement dans lequel plusieurs nœuds Service Fabric se trouvent sur un seul ordinateur physique ou une seule machine virtuelle, vous devez supprimer votre cluster et le recréer avec la nouvelle version.

Les deux flux de travail suivants sont disponibles pour mettre à niveau votre cluster vers la version la plus récente ou vers une version de Service Fabric prise en charge :


*   Les clusters disposant d’une connectivité pour télécharger automatiquement la version la plus récente
*   Les clusters ne disposant pas d’une connectivité pour télécharger la version de Service Fabric la plus récente

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters disposant d’une connectivité pour qu’ils téléchargent le code et la configuration les plus récents
Si vos nœuds de cluster disposent d’une connectivité Internet au [Centre de téléchargement Microsoft](http://download.microsoft.com), suivez les étapes ci-après pour mettre à niveau votre cluster vers une version prise en charge.

Pour les clusters qui disposent d’une connectivité au [Centre de téléchargement Microsoft](http://download.microsoft.com), nous vérifions régulièrement la disponibilité de nouvelles versions de Service Fabric.

Quand une nouvelle version de Service Fabric est disponible, le package est téléchargé localement dans le cluster et configuré pour la mise à niveau. De plus, pour informer l’utilisateur de cette nouvelle version, le système affiche un avertissement explicite concernant l’intégrité du cluster, semblable à ce qui suit :

« La prise en charge de la version actuelle du cluster [numéro de version] se terminera le [date]. »

Une fois que le cluster exécute la version la plus récente, l’avertissement est supprimé.

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
Quand l’avertissement concernant l’intégrité du cluster s’affiche, vous devez faire ce qui suit :

1. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel le script suivant s’exécute ne doit pas nécessairement faire partie du cluster.

    ```powershell

    ###### Connect to the secure cluster using certs
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

    Vous devez recevoir une sortie semblable à ce qui suit :

    ![Obtenir les versions de Service Fabric prises en charge][getfabversions]

3. Démarrer une mise à niveau du cluster vers l’une des versions disponibles à l’aide de la commande Windows PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

    ```powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Cluster upgrade example

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pour surveiller la progression de la mise à niveau, vous pouvez utiliser Service Fabric Explorer ou exécuter la commande Windows PowerShell suivante.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Vous pouvez, à ce moment-là, spécifier des stratégies d’intégrité personnalisées pour la commande Start-ServiceFabricClusterUpgrade. Pour plus d’informations, consultez [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes entraînant la restauration, vous devez relancer la mise à niveau, en suivant la procédure décrite précédemment.

### <a name="upgrade-the-clusters-with-no-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters ne disposant pas d’une connectivité pour télécharger le code et la configuration les plus récents
Si vos nœuds de cluster ne disposent pas d’une connectivité Internet au [Centre de téléchargement Microsoft](http://download.microsoft.com), suivez les procédures présentées dans cette section pour mettre à niveau votre cluster vers une version prise en charge.

> [!NOTE]
> Si vous utilisez un cluster qui n’est pas connecté à Internet, vous devrez consulter régulièrement le [blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/) pour savoir quand une nouvelle version est disponible. Le système *n’affiche pas* d’avertissement concernant l’intégrité du cluster pour vous informer de la disponibilité de cette version.  
>
>

1. Modifiez la configuration du cluster pour définir la propriété suivante sur false.

        "fabricClusterAutoupgradeEnabled": false,

2.      Démarrez une mise à niveau de la configuration. Pour plus d’informations, consultez [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Avant de commencer la mise à niveau de la configuration, mettez à jour la valeur de clusterConfigurationVersion dans votre fichier JSON (JavaScript Objet Notation).

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
1. Téléchargez la dernière version du package à partir du document [Créer un cluster Service Fabric pour Windows Server](service-fabric-cluster-creation-for-windows-server.md).
2. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script s’exécute ne doit pas nécessairement faire partie du cluster.

    ```powershell

    ###### Connect to the cluster
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
3. Copiez le package téléchargé dans le magasin d’images du cluster.

    ```powershell

   ###### Get the list of available Service Fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"

   ###### Code example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

4. Inscrivez le package copié.

    ```powershell

    ###### Get the list of available Service Fabric versions
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file>

    ###### Code example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab

     ```
5. Démarrez une mise à niveau du cluster vers l’une des versions disponibles.

    ```Powershell

    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback

    ###### Code example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback

    ```
   Pour surveiller la progression de la mise à niveau, vous pouvez utiliser Service Fabric Explorer ou exécuter la commande Windows PowerShell suivante.

    ```powershell

    Get-ServiceFabricClusterUpgrade
    ```

    Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Vous pouvez, à ce moment-là, spécifier des stratégies d’intégrité personnalisées pour la commande start-serviceFabricClusterUpgrade. Pour plus d’informations, consultez [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx).

Après avoir corrigé les problèmes entraînant la restauration, vous devez relancer la mise à niveau, en suivant la procédure décrite précédemment.


## <a name="upgrade-the-cluster-configuration"></a>Mettre à niveau la configuration du cluster
Pour mettre à niveau la configuration du cluster, exécutez la commande Start-ServiceFabricClusterConfigurationUpgrade. La mise à niveau de la configuration est traitée par domaine de mise à niveau.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

```


## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de la structure du cluster Service Fabric](service-fabric-cluster-fabric-settings.md).
* Découvrez comment [diminuer et augmenter la taille de votre cluster](service-fabric-cluster-scale-up-down.md).
* Découvrez les [mises à niveau d’applications](service-fabric-application-upgrade.md).

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG

