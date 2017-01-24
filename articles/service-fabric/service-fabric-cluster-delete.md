---
title: Supprimer un cluster Azure et ses ressources | Microsoft Docs
description: "Découvrez comment supprimer complètement un cluster Service Fabric en supprimant le groupe de ressources dans lequel il se trouve ou en supprimant les ressources individuellement."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: de422950-2d22-4ddb-ac47-dd663a946a7e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: a957a70be915459baa8c687c92e251c6011b6172
ms.openlocfilehash: e73334373e9909069641cd4ad2c3280e51b5e2db


---
# <a name="delete-a-service-fabric-cluster-on-azure-and-the-resources-it-uses"></a>Supprimer un cluster Service Fabric sur Azure et les ressources qu’il utilise
Un cluster Service Fabric est composé de nombreuses ressources Azure en plus de la ressource de cluster elle-même. Pour supprimer complètement un cluster Service Fabric, vous devez également supprimer toutes les ressources qui le composent.
Vous pouvez procéder de deux façons : vous pouvez supprimer le groupe de ressources dans lequel est situé le cluster (ce qui supprime la ressource de cluster et toutes les autres ressources du groupe de ressources) ou supprimer spécifiquement la ressource de cluster et les ressources associées (mais pas d’autres ressources du groupe de ressources).

> [!NOTE]
> La suppression de la ressource de cluster n’entraîne **pas** celle de toutes les autres ressources composant votre cluster Service Fabric.
> 
> 

## <a name="delete-the-entire-resource-group-rg-that-the-service-fabric-cluster-is-in"></a>Supprimer l’ensemble du groupe de ressources dans lequel se trouve le cluster Service Fabric
Il s’agit du moyen le plus simple pour vous assurer de supprimer toutes les ressources associées à votre cluster, y compris le groupe de ressources. Vous pouvez supprimer le groupe de ressources à l’aide de PowerShell ou du Portail Azure. Si votre groupe de ressources comporte des ressources qui ne sont pas liées au cluster Service Fabric, vous pouvez supprimer des ressources spécifiques.

### <a name="delete-the-resource-group-using-azure-powershell"></a>Supprimer le groupe de ressources à l’aide d’Azure PowerShell
Vous pouvez également supprimer le groupe de ressources en exécutant les applets de commande Azure PowerShell suivantes. Vérifiez qu’Azure PowerShell 1.0 ou une version ultérieure est installé sur votre ordinateur. Si ce n’est déjà fait, suivez les étapes décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs)

Ouvrez une fenêtre PowerShell et exécutez les applets de commande PS suivantes :

```powershell
Login-AzureRmAccount

Remove-AzureRmResourceGroup -Name <name of ResouceGroup> -Force
```

Vous êtes invité à confirmer la suppression si vous n’avez pas utilisé l’option *-Force* . Après confirmation, le groupe de ressources et toutes les ressources qu’il contient sont supprimés.

### <a name="delete-a-resource-group-in-the-azure-portal"></a>Supprimer un groupe de ressources dans le Portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au cluster Service Fabric que vous souhaitez supprimer.
3. Cliquez sur le nom du groupe de ressources sur la page des éléments essentiels du cluster.
4. Cela fait apparaître la page **Essentials du groupe de ressources** .
5. Cliquez sur **Supprimer**.
6. Suivez les instructions de cette page pour procéder à la suppression du groupe de ressources.

![Supprimer un groupe de ressources][ResourceGroupDelete]

## <a name="delete-the-cluster-resource-and-the-resources-it-uses-but-not-other-resources-in-the-resource-group"></a>Supprimer la ressource de cluster et les ressources associées, mais pas d’autres ressources du groupe de ressources
Si votre groupe de ressources comporte uniquement des ressources qui sont liées au cluster Service Fabric à supprimer, il est plus facile de supprimer l’ensemble du groupe de ressources. Si vous souhaitez supprimer certaines ressources de votre groupe de ressources individuellement, procédez comme suit.

Si vous avez déployé votre cluster à l’aide du portail ou de l’un des modèles Resource Manager Service Fabric de la galerie de modèles, toutes les ressources utilisées par le cluster sont balisées avec les deux balises suivantes. Vous pouvez les utiliser pour décider des ressources à supprimer.

***Balise 1 :*** Clé = clusterName, Valeur = « nom du cluster »

***Balise 2 :*** Clé = resourceName, Valeur = ServiceFabric

### <a name="delete-specific-resources-in-the-azure-portal"></a>Supprimer des ressources spécifiques dans le portail Azure
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Accédez au cluster Service Fabric que vous souhaitez supprimer.
3. Sélectionnez **Tous les paramètres** dans le panneau des éléments essentiels.
4. Cliquez sur **Balises** sous **Gestion des ressources** dans le panneau des paramètres.
5. Cliquez sur l’une des **balises** dans le panneau des balises pour obtenir une liste de toutes les ressources associées à cette balise.
   
    ![Balises de ressource][ResourceTags]
6. Une fois affichée la liste des ressources balisées, cliquez sur chacune des ressources et supprimez-les.
   
    ![Ressources balisées][TaggedResources]

### <a name="delete-the-resources-using-azure-powershell"></a>Supprimer les ressources à l’aide d’Azure PowerShell
Vous pouvez supprimer les ressources individuellement en exécutant les applets de commande Azure PowerShell suivantes. Vérifiez qu’Azure PowerShell 1.0 ou une version ultérieure est installé sur votre ordinateur. Si ce n’est déjà fait, suivez les étapes décrites dans [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs)

Ouvrez une fenêtre PowerShell et exécutez les applets de commande PS suivantes :

```powershell
Login-AzureRmAccount
```
Pour chacune des ressources à supprimer, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName "<name of the resource group>" -Force
```

Pour supprimer la ressource de cluster, exécutez la commande suivante :

```powershell
Remove-AzureRmResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName "<name of the resource group>" -Force
```

## <a name="next-steps"></a>Étapes suivantes
Lisez les documents suivants pour en savoir plus sur la mise à niveau d’un cluster et le partitionnement des services :

* [En savoir plus sur les mises à niveau de cluster](service-fabric-cluster-upgrade.md)
* [En savoir plus sur le partitionnement de services avec état pour une mise à l’échelle maximale](service-fabric-concepts-partitioning.md)

<!--Image references-->
[ResourceGroupDelete]: ./media/service-fabric-cluster-delete/ResourceGroupDelete.PNG

[ResourceTags]: ./media/service-fabric-cluster-delete/ResourceTags.png

[TaggedResources]: ./media/service-fabric-cluster-delete/TaggedResources.PNG



<!--HONumber=Dec16_HO1-->


