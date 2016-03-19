<properties
   pageTitle="Faire monter ou descendre en puissance un cluster Service Fabric | Microsoft Azure"
   description="Faites monter ou descendre en puissance un cluster Service Fabric pour qu’il corresponde à la demande en ajoutant ou en supprimant des nœuds de machine virtuelle."
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


# Faire monter ou descendre en puissance un cluster Service Fabric en ajoutant ou en supprimant des machines virtuelles

Vous pouvez mettre à l’échelle des clusters Azure Service Fabric pour qu’ils correspondent à la demande, en ajoutant ou en supprimant des machines virtuelles.

>[AZURE.NOTE] Votre abonnement doit avoir suffisamment de cœurs pour ajouter les nouvelles machines virtuelles qui composeront ce cluster.

## Mettre à l’échelle un cluster Service Fabric manuellement

### Choisir le type de nœud à mettre à l’échelle

Si votre cluster comporte plusieurs types de nœuds, vous devrez ajouter des machines virtuelles à des types de nœuds spécifiques ou en supprimer. Voici comment procéder :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

2. Accédez aux **clusters Service Fabric**. ![Page Clusters Service Fabric dans le portail Azure.][BrowseServiceFabricClusterResource]

3. Sélectionnez le cluster que vous souhaitez faire monter ou descendre en puissance.

4. Accédez au panneau **Paramètres** sur le tableau de bord de cluster. Si vous ne voyez pas le panneau **Paramètres**, cliquez sur **Tous les paramètres** sur la partie principale du tableau de bord de cluster.

5. Cliquez sur **Types de nœuds**, de manière à ouvrir le panneau de la **liste des types de nœud**.

6. Cliquez sur le type de nœud que vous souhaitez faire monter ou descendre en puissance, de manière à ouvrir le panneau des **détails du type de nœud**.

### Effectuer une montée en puissance par ajout de nœuds

Définissez le nombre de machines virtuelles sur la quantité souhaitée, puis enregistrez. Les nœuds/machines virtuelles seront ajoutés une fois le déploiement terminé.

### Effectuer une descente en puissance par suppression de nœuds

La suppression de nœuds est un processus en deux étapes :

1. Définissez le nombre de machines virtuelles sur la quantité souhaitée, puis enregistrez. L’extrémité inférieure du curseur indique le nombre minimal de machines virtuelles nécessaires pour ce type de nœud.

    >[AZURE.NOTE] Vous devez conserver au moins 5 machines virtuelles pour le type de nœud principal.

2. Une fois le déploiement terminé, les noms des machines virtuelles pouvant être supprimées vous sont communiqués. Vous devez ensuite accéder aux ressources de la machine virtuelle et les supprimer :

    a. Retournez au tableau de bord de cluster et cliquez sur le **Groupe de ressources**. Le panneau **Groupe de ressources** s’ouvre alors.

    b. Regardez sous **Résumé** ou ouvrez la liste des ressources en cliquant sur « **...** ».

    c. Cliquez sur le nom de la machine virtuelle dont le système a indiqué qu’elle peut être supprimée.

    d. Cliquez sur l’icône **Supprimer** pour supprimer la machine virtuelle.

>[AZURE.NOTE] Les clusters Service Fabric nécessitent un certain nombre de nœuds actifs en permanence pour maintenir la disponibilité et préserver l'état, situation appelée "conservation du quorum". Par conséquent, il est généralement déconseillé d'arrêter tous les ordinateurs du cluster, sauf si vous avez d'abord effectué une [sauvegarde complète de votre état](service-fabric-reliable-services-backup-restore.md).

## Mettre à l’échelle des clusters Service Fabric automatiquement

Pour l’instant, les clusters Service Fabric ne gèrent pas la mise à l'échelle automatique. Dans un avenir proche, les clusters seront créés par-dessus les jeux de mise à l’échelle de machine virtuelle. La mise à l’échelle automatique sera alors possible et se comportera de manière similaire à la mise à l’échelle automatique disponible dans les services cloud.

## Mettre à l’échelle des clusters à l’aide de PowerShell ou de l’interface de ligne de commande

Cet article aborde la mise à l’échelle des clusters à l’aide du portail. Toutefois, vous pouvez effectuer les mêmes actions à partir de la ligne de commande à l’aide de commandes Azure Resource Manager sur la ressource de cluster. La réponse GET de la ressource de cluster fournit la liste des nœuds qui ont été désactivés.

## Étapes suivantes

- [En savoir plus sur les mises à niveau de cluster](service-fabric-cluster-upgrade.md)
- [En savoir plus sur le partitionnement de services avec état pour une mise à l’échelle maximale](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png

<!---HONumber=AcomDC_0218_2016-->