---
title: "Suppression d’un cluster HDInsight | Microsoft Docs"
description: "Informations sur les différentes méthodes que vous pouvez utiliser pour supprimer un cluster HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 5ec4b964066687b506686709c3dc5ed5b402fbaf
ms.openlocfilehash: 8b1fbc541b3123961d092c63441b804109865f60


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Suppression d’un cluster HDInsight

La facturation du cluster HDInsight démarre à la création du cluster et s’arrête à sa suppression. La facturation est effectuée au prorata des minutes écoulées. Par conséquent, vous devez toujours supprimer votre cluster lorsqu’il n’est plus utilisé. Dans ce document, vous apprenez à supprimer un cluster à l’aide du portail Azure, d’Azure PowerShell et de l’interface de ligne de commande (CLI) Azure.

> [!IMPORTANT]
> La suppression d’un cluster HDInsight ne supprime pas les comptes de stockage Azure associés au cluster. Étant donné que le compte de stockage n’est pas supprimé, vos données sont conservées et peuvent être réutilisées à l’avenir.

## <a name="azure-portal"></a>Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez votre cluster HDInsight. Si votre cluster HDInsight n’est pas épinglé au tableau de bord, vous pouvez le rechercher par son nom dans le champ de recherche.
   
    ![recherche dans le portail](./media/hdinsight-delete-cluster/navbar.png)

2. Lorsque le panneau s’ouvre pour le cluster, sélectionnez l’icône **Supprimer** . Lorsque vous y êtes invité, sélectionnez **Oui** pour supprimer le cluster.
   
    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

À partir d’une invite PowerShell, utilisez la commande suivante pour supprimer le cluster :

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.

## <a name="azure-cli"></a>Interface de ligne de commande Azure

À partir d’une invite, utilisez la commande suivante pour supprimer le cluster :

    azure hdinsight cluster delete CLUSTERNAME

Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight.




<!--HONumber=Feb17_HO2-->


