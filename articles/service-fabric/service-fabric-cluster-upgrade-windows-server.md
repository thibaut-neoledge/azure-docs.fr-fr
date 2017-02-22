---
title: "Mettre à niveau un cluster Service Fabric autonome sur Windows Server | Microsoft Docs"
description: "Mettez à niveau le code et/ou la configuration Service Fabric qui exécute un cluster Service Fabric autonome, notamment en définissant le mode de mise à jour du cluster."
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
ms.date: 10/10/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 386102ad864d580ce280e3530bce428c532a751c


---
# <a name="upgrade-your-standalone-service-fabric-cluster-on-windows-server"></a>Mettre à niveau un cluster Service Fabric autonome sur Windows Server
> [!div class="op_single_selector"]
> * [Cluster Azure](service-fabric-cluster-upgrade.md)
> * [Cluster autonome](service-fabric-cluster-upgrade-windows-server.md)
> 
> 

Pour les systèmes modernes, la réussite à long terme d’un produit dépend de sa capacité à être mis à niveau. Un cluster Service Fabric est une ressource que vous possédez. Cet article explique comment veiller à ce que le cluster exécute toujours des versions prises en charge du code et des configurations Service Fabric.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Contrôle de la version de la structure qui s’exécute sur votre cluster
Vous pouvez définir votre cluster de façon à télécharger des mises à jour Service Fabric lorsque Microsoft publie une nouvelle version, ou opter pour la sélection de la version de structure prise en charge que vous voulez associer à votre cluster. 

Pour cela, définissez la configuration du cluster « fabricClusterAutoupgradeEnabled » sur true ou false.

> [!NOTE]
> Veillez à ce que votre cluster exécute toujours une version prise en charge de Service Fabric. Lorsque nous annonçons le lancement d’une nouvelle version de Service Fabric, la fin de la prise en charge de la version précédente est signalée 60 jours minimum après la date de lancement. Les nouvelles versions sont annoncées [sur le blog de l’équipe Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nouvelle version peut alors être sélectionnée. 
> 
> 

Vous ne pouvez mettre à niveau votre cluster vers la nouvelle version que si vous utilisez une configuration de nœud de type production, où chaque nœud Service Fabric est alloué à un ordinateur physique ou virtuel distinct. Si vous disposez d’un cluster de développement dans lequel plusieurs nœuds Service Fabric se trouvent sur un même ordinateur physique ou virtuel, vous devez supprimer votre cluster et le recréer avec la nouvelle version.

Il existe deux processus distincts pour la mise à niveau de votre cluster vers la dernière version ou une version prise en charge de Service Fabric : un processus permettant aux clusters disposant d’une connectivité de télécharger automatiquement la version la plus récente, et un autre processus permettant aux clusters ne disposant pas d’une connectivité de télécharger la version la plus récente de Service Fabric.

### <a name="upgrade-the-clusters-with-connectivity-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters disposant d’une connectivité pour qu’ils téléchargent le code et la configuration les plus récents
Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si vos nœuds de cluster disposent d’une connectivité Internet à [http://download.microsoft.com](http://download.microsoft.com) 

Pour les clusters qui peuvent se connecter à [http://download.microsoft.com](http://download.microsoft.com), nous vérifions régulièrement la disponibilité de nouvelles versions de Service Fabric.

Lorsqu’une nouvelle version de Service Fabric est disponible, le package est téléchargé localement dans le cluster et configuré pour la mise à niveau. Ensuite, pour informer l’utilisateur de cette nouvelle version, le système affiche un avertissement explicite concernant l’intégrité du cluster, semblable à ce qui suit :

« La prise en charge de la version actuelle du cluster (numéro de version) se terminera le [date] ». Une fois que le cluster exécute la version la plus récente, l’avertissement disparaît.

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
Quand vous recevez un avertissement concernant l’intégrité du cluster, vous devez faire ce qui suit :

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
   
    ###### Get the list of available service fabric versions 
    Get-ServiceFabricRegisteredClusterCodeVersion
    ```
   
    La sortie doit ressembler à ce qui suit :
   
    ![Obtenir des versions de Service Fabric][getfabversions]
3. Lancez une mise à niveau du cluster vers l’une des versions disponibles à l’aide de l’applet de commande PowerShell [Start-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt125872.aspx)
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou en exécutant la commande PowerShell suivante.
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Vous pouvez spécifier des stratégies d’intégrité personnalisées pour la commande ServiceFabricClusterUpgrade. Pour plus d’informations, lisez [ce document](https://msdn.microsoft.com/library/mt125872.aspx). 

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau, en suivant la procédure décrite précédemment.

### <a name="upgrade-the-clusters-with-uno-connectivityu-to-download-the-latest-code-and-configuration"></a>Mettre à niveau les clusters <U>ne disposant pas d’une connectivité</u> pour qu’ils téléchargent le code et la configuration les plus récents
Suivez ces étapes pour mettre à niveau votre cluster vers une version prise en charge, si vos nœuds de cluster **ne disposent pas** d’une connectivité Internet à [http://download.microsoft.com](http://download.microsoft.com) 

> [!NOTE]
> Si vous utilisez un cluster qui n’est pas connecté à Internet, vous devrez consulter régulièrement le blog de l’équipe Service Fabric pour être averti de la disponibilité des nouvelles versions. Le système **n’affiche pas** d’avertissement concernant l’intégrité du cluster pour vous en informer.  
> 
> 

1. Modifiez la configuration du cluster pour définir la propriété suivante sur false.
   
        "fabricClusterAutoupgradeEnabled": false,

Ensuite, lancez une mise à niveau de la configuration. Pour plus d’informations, consultez [Start-ServiceFabricClusterConfigurationUpgrade](https://msdn.microsoft.com/en-us/library/mt788302.aspx). Veillez à mettre à jour « clusterConfigurationVersion » dans votre code JSON avant de lancer la mise à niveau de la configuration.

```powershell

    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File> 

```

#### <a name="cluster-upgrade-workflow"></a>Flux de travail de mise à niveau de cluster
1. Téléchargez la dernière version du package à partir du document [Create Service Fabric cluster for Windows Server](service-fabric-cluster-creation-for-windows-server.md) (Créer un cluster Service Fabric pour Windows Server). 
2. Connectez-vous au cluster à partir d’une machine virtuelle disposant d’un accès administrateur à toutes les machines qui sont répertoriées en tant que nœuds dans le cluster. L’ordinateur sur lequel ce script est exécuté ne doit pas nécessairement faire partie du cluster. 
   
    ```powershell
   
    ###### connect to the cluster
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
   
   ###### Get the list of available service fabric versions
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file including the path to it> -ImageStoreConnectionString "fabric:ImageStore"
   
   ###### Here is a filled out example
    Copy-ServiceFabricClusterPackage -Code -CodePackagePath .\MicrosoftAzureServiceFabric.5.3.301.9590.cab -ImageStoreConnectionString "fabric:ImageStore"

    ```

1. Inscrire le package copié 
   
    ```powershell
   
    ###### Get the list of available service fabric versions 
    Register-ServiceFabricClusterPackage -Code -CodePackagePath <name of the .cab file> 
   
    ###### Here is a filled out example
    Register-ServiceFabricClusterPackage -Code -CodePackagePath MicrosoftAzureServiceFabric.5.3.301.9590.cab
   
     ```
2. Lancez une mise à niveau du cluster vers l’une des versions disponibles. 
   
    ```Powershell
   
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <codeversion#> -Monitored -FailureAction Rollback
   
    ###### Here is a filled out example
    Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion 5.3.301.9590 -Monitored -FailureAction Rollback
   
    ```
   Vous pouvez surveiller la progression de la mise à niveau avec Service Fabric Explorer ou en exécutant la commande PowerShell suivante.
   
    ```powershell
   
    Get-ServiceFabricClusterUpgrade
    ```
   
    Si les stratégies d'intégrité du cluster ne sont pas respectées, la mise à niveau est annulée. Vous pouvez spécifier des stratégies d’intégrité personnalisées pour la commande ServiceFabricClusterUpgrade. Pour plus d’informations, lisez [ce document](https://msdn.microsoft.com/library/mt125872.aspx). 

Une fois que vous avez corrigé les problèmes entraînant la restauration, vous devez initier à nouveau la mise à niveau, en suivant la procédure décrite précédemment.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser certains [paramètres de la structure du cluster Service Fabric](service-fabric-cluster-fabric-settings.md)
* Découvrez comment [mettre votre cluster à l’échelle](service-fabric-cluster-scale-up-down.md)
* Découvrez les [mises à niveau de l’application](service-fabric-application-upgrade.md)

<!--Image references-->
[getfabversions]: ./media/service-fabric-cluster-upgrade-windows-server/getfabversions.PNG



<!--HONumber=Dec16_HO2-->


